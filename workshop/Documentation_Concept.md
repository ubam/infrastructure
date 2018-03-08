# UBAM Docs Concept

*Developing an organizational structure to use in a GitHub repo*

**Purpose:** UBAM (at time of conception still just the UBFR) aims to create a document space separate from docs.ubports.com to document workflows, hold information about the community infrastructure and to have a cache of knowledge to point newcomers to on repeated questions.

*UBFR docs specific:* As this will naturally be almost exclusively aimed at newcomers, the information recorded will not go into technical detail or tackle more advanced problems, but display processes near to the start of a new users experience more detailled than the UBports Docs. To quickly re-discover already documented information and improve on existing one, or being able to create a new document after quickly determining a topic is not already available, the documentation must be managed in a very organized and clear fashion, especially with incrasing amounts of stored knowledge.

Current state:    **[ in execution ]**

- [X] develop initial structural concept
- [X] create hosting space ([github.com/ubam](https://github.com/ubam/))
- [X] create/populate with structure
- [X] transition existing pads to md documents
- [ ] add (and settle on versions of existing) workflows


## Hosting

Apart from creating another website like docs.ubports.com and adopting the hassle to manage and maintain yet another system, the idea of just using a GitHub repository came up.
The repo could be something like http://github.com/ubports/ubfr
Depending on the planned usage, it is to discuss whether this repo should be public *(→ having quick links to point newcomers to on a recurring question, potentially greatly speeding up the process and relieving the UBFR)*. Either way, each member of the ubfr team will probably be an admin to freely use and manage the repo. It would be possible to have just one or two admins tasked with also supervising the quality of the changes, though the net benefits of that are more than questionable.


## Structural concepts

*These have been updated according to the original discussions and decisions in [infrastructure#7](https://github.com/ubam/infrastructure/issues/7)*

### Repositories



### Directory structure

#### General conventions of informational (non-code) UBAM repositories

- All directories and files that are not `.md` documents (and thus not immediately viewable) can have only small letters and contain no spaces or special symbols (which may be replaced with an underscore `_`).
- `.md` document names can contain capital letters at the start of words (or in CamelCase), which can be separated by underscores instead of spaces. Special symbols are discouraged.
- Usually, `.md` documents carry a top level headline that repeats their name (without the `.md`), in case of a `README.md` this is the name of their parent directory or repository, capitalized as a `.md` name would be.
- Every directory is generally encouraged to contain a `README.md`, explaining at least what the directory is all about (e.g. nextcloud: what is nextcloud, relation to UT of the contained docs).

#### Repository trees

 Of course, some thought that went into this is not clear from this structure; the respective contexts will be explained in the eventual `README.md`s, but could be added here earlier on request.

	ubfr-docs/
	|-	install/
	|	|-	flowchart
	|	|-	Known_Issues.md
	|	|-	README.md
	|	\-	[...]
	|-	directory-enquiries
	|-	internal/
	|	|-	Quick_Links.md
	|	|-	README.md
	|	\-	[...]
	|-	FAQ.md
	|-	README.md
	\-	[...]


	infrastructure/
	|-	community/
	|	|-	developers
	|	|-	foundation
	|	|-	ubam
	|	|-	ubfr
	|	|-	ublangs/
	|	|	|-	Language_Captain_Duty_List.md
	|	|	|-	Language_Group_Creation_Guidelines.md
	|	|	\-	README.md
	|	|-	events
	|	|-	README.md
	|	\-	[...]
	|-	platforms/
	|	|-	irc
	|	|-	matrix
	|	|-	telegram/
	|	|	|-	language-groups/
	|	|	|	|-	Responsibilities_Overview.md
	|	|	|	\-	README.md
	|	|	|-	logos/
	|	|	|	|-	template.svg
	|	|	|	|-	sg.svg
	|	|	|	|-	sg.png
	|	|	|	|-	ubam.svg
	|	|	|	|-	ubam.png
	|	|	|	|-	ubfr.svg
	|	|	|	|-	ubfr.png
	|	|	|	|-	README.md
	|	|	|	\-	[...]
	|	|	|-	Group_Creation_Guidelines.md
	|	|	|-	Responsibilities_Overview.md
	|	|	\-	README.md
	|	|-	bridges/
	|	|	|-	telegram-irc
	|	|	\-	[...]
	|	|-	ubports-forum
	|	\-	[...]
	|-	public-relations/
	|	|-	announcements
	|	|-	facebook
	|	|-	twitter
	|	|-	soundcloud
	|	|-	ubports.com
	|	\-	[...]
	|-	workshop/
	|	|-	Documentation_Concept.md
	|	|-	UBFR_Assistant_Bot.md
	|	\-	README.md
	|-	README.md
	\-	[...]


	community/
	|-	nextcloud/
	|	|-	Install_RPI3.md
	|	|-	UT_App.md
	|	\-	README.md
	|-	linuxbrew
	|-	transcripts/
	|	\-	qna/
	|		|-	de/
	|		|	|-	2018-01-06_20.md
	|		|	|-	2018-01-20_21.md
	|		|	\-	[...]
	|		|-	es/
	|		|	|-	2018-01-06_20.md
	|		|	|-	2018-01-20_21.md
	|		|	\-	[...]
	|		|-	2018-01-06_20.md
	|		|-	2018-01-20_21.md
	|		\-	[...]
	|-	README.md
	\-	[...]


	doc-templates/
	|-	directory/
	|	|-	structure/
	|	|	|-	random-file.png
	|	|	\-	Random_Document.md
	|	\-	README.md
	|-	Concept.md
	|-	Guidelines.md
	|-	Vote_Issue.md
	|-	README.md
	\-	[...]

These areas will likely dynamically grow.

### Document structure

Use Markdown (\*.md) for all text documents to take advantage of minimal text editing and the GitHub md rendering engine: https://guides.github.com/features/mastering-markdown/

> Since UBports documentation (docs.ubports.com) already uses ReadTheDocs to organize and display documentation, which is uses Gihub as 'backend' to store documentation, we could use it too to have a consistent layout and style. We can have i.e. www.github.com/ubports/ubfr-doc to manage ReadTheDocs pages and something like www.ubports.com/ubfr/ to access it

*[...]*

