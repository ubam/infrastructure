# UBFR assist bot

*A bot conceived by Peter Nerlich written in NodeJS to assist and streamline the work of the UBFR*

**Purpose:** Create mostly passive Telegram bot to assist UBFR (mainly) quickly responding to newcomers, by quickly dispatching centralized notifications (spanning all UBports groups) and differentiating between proper newcomers and cross-joins.


Current state:    **[ abandoned** (running, but not helpful)**]**
- prototype (v0.0.2) running (supposedly 24/7)
- attached to @ubfrassistant_bot
- broadcasting new members to @ubfrnewmember and bot test lab (https://t.me/joinchat/BrrodBIXsiwvmMSF5wvsWA) — only joined (=supervising) bot test lab and UBFR+ (former UB_Welcome_Room) at the moment


## Base idea: Functionality requirements (quick sketch)

- notify about new users
	- info about which group
	- send channel link and suggested response to assigned UBFR member
- record additional trivia about persons interests/skills
- web interface: login via phone number/tag handle (if UBFR member) ? validate with code sent via pm

Optional usage possibilities:
- manage who does the welcome
- collect statistics about growth across all groups, individual group usage (e.g. avg. message per member) and individual member engagement (most active)
- let (UBFR) members individually track specific #hashtags
- kick out everyone but members of a certain team (e.g. UBFR) after __ minutes of inactivity from discussion rooms / ask members in private chat to leave group (?this way they can join again on their own)

example situations:
- bot registers: user joins group
	- bot searches db for user
		- if user found, bot checks for membership in other groups
			- if membership found, bot sends all-clear to dedicated group or channel (e.g. UBFR team), optionally providing insight about other memberships and time since last activity
		- if user not found, bot creates user in db
			- bot dispatches new user alert in dedicated group or channel (e.g. UBFR team), providing username and channel link for quick access
	- bot adds user to group db entry
- bot registers: user leaves group
	- bot updates db entry
- UBFR member chats with user, discovers interest in [ ]
	- UBFR member messages bot in private chat to view user annotations
		- bot sends team memberships of user and annotations (interests, skills)
	- UBFR member messages bot in private chat to update annotations for user
		- bot saves annotations

## Restrictions

Getting all members of a group is currently not implemented in the bot api, the solution being a database tracking users updated by join and leave messages. This would however require the bot to be online at all times (normally guarantueed in production) to not fall out of sync. As a failsafe and initial setup a manual method to "register" users into the database would be necessary, which could be automated by checking all usernames inputted with telegramBot.getChatMember(chatId, userId) to validate membership and telegramBot.getChatMembersCount(chatId) to detect incoherences and, initially, whether a group entry has been fully synched already. [https://stackoverflow.com/a/37506156/4067384]

## Proper requirements draft

bot database:
- groups (inherited from joined groups)
	- derive type (team, discussion room ?kick-out feature ?, ...) from name
- user list
	- member of which groups (+ group history)
	- member of which teams
	- trivia: skills, interests (+ update history)
	- time of last activity (per group)

## High-level prototyping (because I'm more into trial and error)

```
event listeners to provide:

.onPrivate      ?        (msg)                // private/direct message
.onOrganic      ?        (msg)                // organic msg, with user (from) and group (chat)
.onUserData     ?        (user)                // msg with user (from, new_chat_members, ...), firing for each individual extracted user obj
.onGroupData    ?        (group)                // msg with group (chat), firing for each individual extracted group obj
.onJoin         ?        (user, group)        // join notice (new_chat_member, new_chat_members), firing for each individual extracted user obj
.onLeave        ?        (user, group)        // leave notice (left_chat_member, left_chat_members), firing for each individual extracted user obj


methods to provide:

updateUser(user)        ?    Promise    // updates or creates user in db (if needed)
updateGroup(group)      ?    Promise    // updates or creates group in db (if needed)
updateMember(member)    ?    Promise    // updates or creates member in db (if needed)

existsUser(user.id)     ?    Promise    // check if user entry exists in db
existsGroup(user.id)    ?    Promise    // check if group entry exists in db
existsMember(user.id)   ?    Promise    // check if member entry exists in db

getUser(user.id)                ?    Promise    (user)        // get user from db
getGroup(group.id)              ?    Promise    (group)       // get group from db
getMember(user.id, group.id)    ?    Promise    (member)      // get member from db
getMemberships(user.id)         ?    Promise    ([member])    // get array of member entries for user from db

queryUser(user.id)              ?    Promise    (user)        // query user from telegram
queryGroup(group.id)            ?    Promise    (group)       // query group from telegram
queryMember(user.id, group.id)  ?    Promise    (member)      // query member from telegram


signalNewjoin(user, group)                ?    Promise    // send join notification to destined channels/groups (from config)
signalCrossjoin(user, group, [member])    ?    Promise    // send cross join notification to destined channels/groups (from config)
signalRejoin(user, group)                 ?    Promise    // send rejoin notification to destined channels/groups (from config)
signalLeave(user, group)                  ?    Promise    // send leave notification to destined channels/groups (from config)
```

--------

```
Tel.onUserData(updateUser);
Tel.onGroupData(updateGroup);

Tel.onOrganic((msg) => {
		queryMember(msg.from.id, msg.chat.id).then((member) => {
				updateMember(member);
		});
});

Tel.onJoin((user, group) => {
		existsUser(user.id).then((u) => {
				getMemberships(user.id).then((members) => {
						if (members.length > 1 || (members.length == 1 && members[0].groupId == group.id)) {
								signalCrossjoin(user, group, members);
						} else {
								signalRejoin(user, group)
						}
				});
		}).catch((e) => {
				// create user
		});
		
		// not organic, update member
		queryMember(msg.from.id, msg.chat.id).then((member) => {
				updateMember(member);
		});
});

Tel.onJoin((user, group) => {
		signalLeave(user, group)
		
		// not organic, update member
		queryMember(msg.from.id, msg.chat.id).then((member) => {
				updateMember(member);
		});
});


Tel.onPrivate((msg) => {
		// [TODO]
		// if user authorized for command (according to config, e.g. explicit or by group membership)
				// do stuff
		// else
				// unauthorized, report user and command to destined channel/group (from config)
				// send error message to user
});
```

## Sample data for messages (using node-telegram-bot-api)

The chat id of private conversations (direct / not group/channel) is identical to the id of the participant.

The from integer is a unix timestamp meaning from\*1000 to create JS Date

```
normal text message
{ message_id: 4,
	from: 
	 { id: XXXXXXXXX,
		 is_bot: false,
		 first_name: 'Peter',
		 last_name: 'Nerlich',
		 username: 'peternerlich',
		 language_code: 'en' },
	chat: 
	 { id: XXXXXXXXX,
		 first_name: 'Peter',
		 last_name: 'Nerlich',
		 username: 'peternerlich',
		 type: 'private' },
	date: 1511532514,
	text: 'j' }

bot got joined
{ message_id: 5,
	from: 
	 { id: XXXXXXXXX,
		 is_bot: false,
		 first_name: 'Peter',
		 last_name: 'Nerlich',
		 username: 'peternerlich',
		 language_code: 'en' },
	chat: 
	 { id: XXXXXXXXX,
		 title: 'bot test lab',
		 type: 'group',
		 all_members_are_administrators: true },
	date: 1511534146,
	new_chat_participant: 
	 { id: XXXXXXXXX,
		 is_bot: true,
		 first_name: 'UBFR assistent',
		 username: 'UBFRass_bot' },
	new_chat_member: 
	 { id: XXXXXXXXX,
		 is_bot: true,
		 first_name: 'UBFR assistent',
		 username: 'UBFRass_bot' },
	new_chat_members: 
	 [ { id: XXXXXXXXX,
			 is_bot: true,
			 first_name: 'UBFR assistent',
			 username: 'UBFRass_bot' } ] }


user left group/got kicked
{ message_id: 6,
	from: 
	 { id: XXXXXXXXX,
		 is_bot: false,
		 first_name: 'Peter',
		 last_name: 'Nerlich',
		 username: 'peternerlich',
		 language_code: 'en' },
	chat: 
	 { id: XXXXXXXXX,
		 title: 'bot test lab',
		 type: 'group',
		 all_members_are_administrators: true },
	date: 1511534178,
	left_chat_participant: 
	 { id: XXXXXXXXX,
		 is_bot: false,
		 first_name: 'Peter',
		 last_name: 'Nerlich' },
	left_chat_member: 
	 { id: XXXXXXXXX,
		 is_bot: false,
		 first_name: 'Peter',
		 last_name: 'Nerlich' } }

user joined/got added
{ message_id: 7,
	from: 
	 { id: XXXXXXXXX,
		 is_bot: false,
		 first_name: 'Peter',
		 last_name: 'Nerlich',
		 username: 'peternerlich',
		 language_code: 'en' },
	chat: 
	 { id: XXXXXXXXX,
		 title: 'bot test lab',
		 type: 'group',
		 all_members_are_administrators: true },
	date: 1511534195,
	new_chat_participant: 
	 { id: XXXXXXXXX,
		 is_bot: false,
		 first_name: 'Peter',
		 last_name: 'Nerlich' },
	new_chat_member: 
	 { id: XXXXXXXXX,
		 is_bot: false,
		 first_name: 'Peter',
		 last_name: 'Nerlich' },
	new_chat_members: 
	 [ { id: XXXXXXXXX,
			 is_bot: false,
			 first_name: 'Peter',
			 last_name: 'Nerlich' } ] }
```