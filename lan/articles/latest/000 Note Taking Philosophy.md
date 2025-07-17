#article

1. [0.1 Objective](#01-objective)
2. [0.2 Public by default](#02-public-by-default)
3. [0.3 Digestible and technical](#03-digestible-and-technical)
4. [0.4 On Use of Large Language Models (LLMs)](#04-on-use-of-large-language-models-llms)
	1. [0.4.1 How LLMs are used](#041-how-llms-are-used)
	2. [0.4.2 How LLMs will not be used](#042-how-llms-will-not-be-used)
5. [0.5 Writing and methodical critique over this entry](#05-writing-and-methodical-critique-over-this-entry)
6. [1 Change log (Version: v1.0.2)](#1-change-log-version-v102)

## 0.1 Objective
Documentation is an important part of the development process. In this project we propose a note taking system that can lead to higher quality creative work. The aim is to empower authors, collaborators, students, and users with the key information they need. For creators, ideally to capture the conditions of the creative process and open it up for refinement and critique over time. 

## 0.2 Public by default
The key policy is that all notes are public by default. What is private is assessed to not add much value to be read by others. For example, private communications or schedule information, which may be too personal and variable. 

By making all notes public by default, the aim is to maximize transparency in the conceptual and code development. The notes evolve with the project via version control, so the evolution of ideas can be tracked just like changes in source code.

This contrasts with notes that are private by default, where often the project is a final deliverable but the process that had materialized its creation remains hidden or becomes lost with time. 

## 0.3 Digestible and technical
There are different types of possible users of this work:
* Some are professional, looking to incorporate it into a product
* Some are curious, looking to explore what is possible.
* Some are looking to contribute or co-author the work, and are interested in the goal of expressing their creative process transparently.

Because of this, there will be entries tailored for different types of user. Reader-facing entries (like articles) will focus on presenting information in a simple and digestible manner. Articles may link to other articles, but they remain focused on teaching others about the work. Creator-facing entries (like task notes, research journals, sketches, ...) can be more technical, informal, exploratory, and relational. It can incorporate any use of technology for the purpose of developing a prototype.

For the reader-facing entries, the goal is to communicate very clearly to others about the work or some concept. For creator-facing entries, the goal is to capture the creative process in an expressive and efficient manner that can be referenced by the creators for further work.

## 0.4 On Use of Large Language Models (LLMs)
This section explores the use of LLMs in the creative process, and discusses problems with quality that may result from their use.

Any use of LLMs is documented to remain consistent with the public-by-default goal outlined above.

LLMs will not be considered co-authors of this work. Architecture, data modeling, design choices, and implementation choices are the primary concern of the project authors. Any code that is submitted must be explainable and verified by its owners.

### 0.4.1 How LLMs are used
* **For quick retrieval of information from documentation and search online.** Used similar to and alongside a traditional search engine like Google. Traditional search engines offer precision around querying, while LLMs offer more contextual flexibility via technologies like vector search.
* **For diagnostics.** Pasting error logs, screenshots of problems, and expressing the problems in human language while using LLMs to find solutions. This serves the dual purposes of documenting technical issues efficiently and quick retrieval of solutions to common problems. This is part of the diagnostic toolkit which includes reading documentation, creating reproducible minimal examples of issues, capturing issues as tests, tracking and contributing to issue tickets.
* T**o document concept exploration in dialogue format.** This can somewhat simulate conversations with peers, responding to critique, and exploring contrast in problem solving approaching. This is limited and does not replace critique from peers. With permission, digests of actual critique received by peers will also be documented. 

### 0.4.2 How LLMs will not be used
* **To have any code authorship.** As stated above, all submitted works must be explainable by the authors. 
* **To push code faster.** Time efficiency gains can be achieved through effective use of third party resources.
* **To be used for flexibility over conceptual clarity.** Use of this technology can tend to favor ambiguity in critical code infrastructure aspects such as error handling and modeling. In prototyping, the attention of authors may be on a specific problem where otherwise they accept the simplest solution for efficiency. This will be a positive decision made by the authors and will be made with sharp contrast.
* **To be used to provisionally complete objectives.** The authors aims to make optimal, economic, and parsimonious decisions. Prototyping is about establishing concept feasibility. This can be done in a sharp and non-vague manner such as solving key mathematical problems, demonstrating economic scalability, and establishing costs to development teams to refine the prototype into an end product.

## 0.5 Writing and methodical critique over this entry

Peer criticism and refinement for this entry is captured below.

- Peer 1
	- v1.0.0->v.10.1
		* Initial wording in the document appeared indirect and not informative. Short descriptions were provided such as "LLMs will be used for search and diagnostics", or "LLMs will be used for brainstorming". More details are given to remedy this issue.
		* Public perception: Use of LLMs alongside projects risk communicating that convenience is primary and quality is secondary. This is a primary motivation for this note entry to try to address. Although use of LLMs may rise suspicions on quality, if it is used, it is best transparently declared to remain in line with maximum transparency. 
- Peer 2
	- v1.0.1->v1.0.2
		- Refinement of sentence structure. To clarify the problem being solved. 

# 1 Change log (Version: v1.0.2)
- v1.0.0: First entry
- v1.0.1: Expanded on how LLMs are used and not used. Added change log and digest of peer review.
- v1.0.2: Added objective, reworded major sections