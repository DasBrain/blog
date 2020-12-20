# Hacking OpenJDK in Eclipse IDE

This guilde should help developers who want develop on the OpenJDK project itself with Eclipse.  
This guilde only covers development of the Java side.

## The short story:

* Use one project per java module.
* Use the class path, not module path for your project and dependenies. (You might need to rename/remove the `module-info.java`)
* Add the compiled output of your module from the exploded image to the class path.

## Step-by-step guide

**Important:** Make sure that you have a successful build (`make images`) of the openjdk first.

1. Tempoarily rename the `module-info.java` of the module you intend to edit to something else - preferably not ending in `.java`.
2. Disable automatic building: "Project -> Build Automatically".
3. Create a new Java Project.
4. Don't use the default location - use "jdk/src/java.base".
5. Click Finish.
6. Remove all source folders except the the shared/classes and (optional) one platform specific source path.
7. Remove the java runtime from the build path: Rightclick on the Project -> Build Path -> Configure Build Path -> Libraries -> Select "JRE System Library" -> Remove.
8. Add the dependency of your module to the classpath. Consult the `module-info.java` for a list of dependencies.
  * If you have to edit files in that module as well, create a new Java Project (using this steps) and add that that to the classpath.
  * Otherwise, you can use the generated classes, located under `jdk/build/<your-platform>/jdk/modules/<module-name>`. (Library -> Add External Class Folder).  
    (Optional) Add `jdk/build/<your-platform>/images/jdk/lib/src.zip` as source attachment to get javadoc & source lookup support for those classes.
9. If your module contains classes that are generated during build, add the generated classes **to the class path**. See the point above, basically add your own module as external dependency.
10. Let eclipse build the project - this may take a minute.
11. Restore the `module-info.java`.

After this, you should be able to make changes to the OpenJDK with the compfort of most\* Eclipse features.

## Known Limitations:

* The suggested project location will put additional files into the source folder. Don't commit them.
* Because the class path is used, eclipse doesn't care that an used package in an other module is not exported. `make images` should complain through.
* The "managment" of the `module-info.java` is manual. You may need to remove it before doing a "build" in eclipse - the build is nessecary so eclipse has the needed meta-information.

## Why this works:

The class path allows us to have split packages - a missing class (where the generated `.java` file is generated at build time) is then taken from an other locations.

