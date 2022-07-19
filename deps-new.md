# Understanding Deps

deps.edn is a *datastructure* that describes how the project basis must be constructed. A project's basis is all its dependent libraries, and how they are to be *resolved*, which can be from a maven repository, a git url, or a local file.

deps.edn can be specified on the command line with clj, or specified as file *deps.edn* 
