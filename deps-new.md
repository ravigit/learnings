# Understanding Deps

The <ins>deps.edn</ins> is a *data structure* that describes how the project basis must be constructed. A project's basis is all its dependent libraries, and how they are to be *resolved*, which can be from a maven repository, a git url, or a local file. Each of those descriptions is called a coordinate.

deps.edn can be specified on the command line with clj, or specified as file `deps.edn` . 
- <ins>tools.deps</ins> is the library that does the dependency resolution from `deps.edn`
- <ins>tools.build</ins> is the library that is used to build the jars, uberjars. The build description is a program and *not* a declarative description. It needs a `build.clj` file. The library has functions to clean target dirs, build jars and uberjars, but a user description is needed to use them. The functions in the build.clj file are targets on command line, for example:

```bash
> clj -Tbuild build-jar
```
where build-jar is a function defined in `build.clj` file. The argument to option "-T",*build*, is an alias for tools.build tool. To see all the installed tools locally, one can do
```bash
> clj -Ttools list
TOOL     LIB                              TYPE  VERSION
build    io.github.clojure/tools.build    :git  v0.8.3
clj-new  com.github.seancorfield/clj-new  :git  v1.2.399
new      io.github.seancorfield/deps-new  :git  v0.4.12
tools    io.github.clojure/tools.tools    :git  v0.2.5
```
The argument *tools* is an alias for tools.tools tool.

- <ins>deps-new</ins> is a library written by Sean Corfield, that can be used to create empty projects based on templates. It also provides a template to create new templates. However, the templates *cannot* be packaged into a jar as of git tag "v0.8.2". One has to refer the template by git url or local folder. Here's an example of how to create a project from a deps-new template 

``` bash
clj -Sdeps '{:deps {net.clojars.ravi/mytemplate {:local/root "mytemplate"}}}' -Tnew create :template ravi/mytemplate :name ravi/new-app
```

This creates a new project called new-app.
