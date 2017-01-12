---
layout: post
title:  "What does a Software Engineer do?"
date:   2017-01-12 22:40:00
youtube_timelapse_construction: AhLk7L1B_fE
youtube_timelapse_deconstruction: i-2Y2MYpl2g
---

I often get asked the question "What does a Software Engineer do?" soon after mentioning my profession. After several iterations of answering the question, I now have an analogy to convey effectively what our profession is about, which I have decided to blog and share for anyone else who is looking for a simple illustration.

Methaphorically speaking, **Software Engineers build skyscrapers.**  

Here is a time lapse video of an actual skyscraper being built:

{% include youtubePlayer.html id=page.youtube_timelapse_construction %}  
<br/>

Building a skypscraper involves:

- **_A blueprint for architecture_** - how it will look inside and out i.e. its design
- **_Materials for construction_** - concrete, metal, glass etc.
- **_Tools for construction_**- cranes, bulldozers, forklifts etc.
- **_Phases for construction_** - these phases are sequential and layered. Foundation first; then the core exterior structure; then each floor; then the windows; then the interior; then the electrical cabling, plumbing, installation of elevators, fire alarm systems; then the decorative touches such as carpets and painting the walls etc...
- **_Testing of the construction_** - testing after every phase of construction is complete with different criteria to ensure integrity, correctness, saftey to continue. At the end of construction final tests are carried out such as performance testing of electricity and water throughput in the building, health and saftey standards, fire alarm and evacuations, elevators etc.   


Building software is no different to this as it follows the same requirements. Using the same categories as above, building software involves: 

- **_A blueprint for architecture_** - this is known as [Software Architecture][]. This outlines a solution where it defines what fundamental structural components will exist,
what established methodologies and paradigms will be implemented and how (e.g. [Software Design Patterns][]), how all components will all work together in harmony end to end, the rationale behind these choices and the approach
- **_Materials for construction_** - programming languages, frameworks, existing code libraries to use (e.g. [Open Source][])
- **_Tools for construction_** -  computer, servers, operating systems, databases, software development applications (e.g. [Integrated Development Environments][]), source code compilation and packaging tools
- **_Phases for construction_** -  building components one at a time, or layer by layer of components in a sequential manner. The phases are better outlined in the chosen [Software Development Process][] as it clarifies the methodology, the approach and therefore the phases for construction 
- **_Testing of the construction_** - performing functional testing of individual components and all components together to prove correctness at individual layers and the software as a whole (testing [Functional Requirements][]). Additionally, quality of the developed software is tested to ensure acceptable performance and to expose vulnerabilities (testing [Non-Functional Requirements][]). [Software Testing][] approaches have many different aspects established already and is continually evolving.    

Modern day software engineers focus on not just building software that works, they focus on building software that does not fail. They focus on not just _what_ to build, but _how_ to build it. In other words, **Software Engineering is about building the right software and building the software right.**
_Building the software right_ essentially means it is built robustly to not have vulnerabilities and the software and source code architecture allows for amendments to be made easily in the future. This is where the skill levels of software engineers vary and define their true talents in my opinion.

Vulnerabilites in software are factors that will compromise the operation of it. There are many but the most common are [security][], [performance][], [error handling][], [scalability][], [efficiency][] and [resource consumption][]. A software may be correct functionally but it may be wholly unusable if these vulnerabilities are not addressed.

Good software and source code architecture means the software can be maintained easily; extended for future enhancements (known as [Extensibility][]); have existing components reused, altered or removed entirely without causing undesired effects and cascading issues in other (potentially unrelated) parts of the software that would additionally need to be resolved i.e. to avoid [Software Regression][]. We categorize this architecture and its components as being [Modular][], achieving [High Cohesion][] and [Low Coupling][]. These are the fundamental principles and rules of thumb that software engineers strive to achieve ideally in all software architecture.  
Using the skyscraper analogy again as an identical visual example to demonstrate good software architecture, in this video a skyscraper is being demolished one level at a time without affecting the rest of it:

{% include youtubePlayer.html id=page.youtube_timelapse_deconstruction %}

Each floor is treated as a self contained component, modular, highly cohesive and loosely coupled to the rest of the building. Changing it does not impact other floors or other parts of the building.  
Bad architecture would lead to skyscrapers collapsing on its own or while extending it or removing parts of it. The exact same concepts and effects here apply to software too.

In conclusion I hope the similarities of building skyscrapers and software has enlightened you to what the software engineering profession is about and insight into the general aspects of it.

[Integrated Development Environments]: https://en.wikipedia.org/wiki/Integrated_development_environment
[Open Source]: https://en.wikipedia.org/wiki/Open-source_software
[Software Architecture]: https://en.wikipedia.org/wiki/Software_architecture
[Software Design Patterns]: https://en.wikipedia.org/wiki/Software_design_pattern
[Software Development Process]: https://en.wikipedia.org/wiki/Software_development_process
[Software Testing]: https://en.wikipedia.org/wiki/Software_testing
[Software Regression]: https://en.wikipedia.org/wiki/Software_regression
[Functional Requirements]: https://en.wikipedia.org/wiki/Functional_requirement
[Non-Functional Requirements]: https://en.wikipedia.org/wiki/Non-functional_requirement
[Modular]: https://en.wikipedia.org/wiki/Modular_programming
[High Cohesion]: https://en.wikipedia.org/wiki/Cohesion_(computer_science)
[Low Coupling]:https://en.wikipedia.org/wiki/Coupling_(computer_programming)
[Extensibility]: https://en.wikipedia.org/wiki/Extensibility
[performance]: https://en.wikipedia.org/wiki/Computer_performance
[error handling]: https://en.wikipedia.org/wiki/Exception_handling#Exception_handling_in_software
[security]: https://en.wikipedia.org/wiki/Computer_security
[scalability]: https://en.wikipedia.org/wiki/Scalability
[resource consumption]: https://en.wikipedia.org/wiki/System_resource
[efficiency]: https://en.wikipedia.org/wiki/Algorithmic_efficiency