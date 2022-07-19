# Understanding Deps

The <ins>deps.edn</ins> is a *data structure* that describes how the project basis must be constructed. A project's basis is all its dependent libraries, and how they are to be *resolved*, which can be from a maven repository, a git url, or a local file. Each of those descriptions is called a coordinate.

deps.edn can be specified on the command line with clj, or specified as file `deps.edn` . 
- <ins>tools.deps</ins> is the library that does the dependency resolution from `deps.edn`
- <ins>tools.build</ins> is the library that is used to build the jars, uberjars. The build description is a program and *not* a declarative description. It needs a `build.clj` file. The library has functions to clean target dirs, build jars and uberjars, but a user description is needed to use them. The functions in the build.clj file are targets on command line, for example:

```bash
> clj -Tbuild build-jar
```
where build-jar is a function defined in `build.clj` file. The argument to option "-T",*build*, is an alias for tools.build tool. To see all the installed tools locally, one can
```bash
> clj -Ttools list
```
The argument *tools* is an alias for tools.tools tool.

- <ins>deps-new</ins> is a library written by Sean Corfield, that can be used to create empty projects based on templates. It also provides a template to create new templates
