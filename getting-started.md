# Getting started

## JDK version

The JDK for the project is initially set to "11"; this can be changed in the **Project Settings/Project** screen of the **Project Structure** dialog. Whichever version is used it is _highly recommended_ that all members of the development team standardize on a standard naming convention for JDKs configured in IntelliJ; otherwise, the `.idea/misc.xml` file, which references the JDK by name, will vary from machine to machine. Since this file is currently included in the Git repository, differences in JDK names will result in needless merge conflicts, unless this file is removed from version control. For more information, see the relevant lines (9&ndash;16, initially) in `.gitignore`. 

## Root module name

Initially, the root module of the project is named `project` (note the `project.iml` file in the project root directory). Typically, this root module name will become part of the name of JAR files built by the project; thus, it should be changed to something more relevant to the specific project. This is most easily done through the **Project Settings/Modules** list in the **Project Structure** dialog.

## Source and resource folders

This starter includes the `src`, `test`, `resources`, and `test-resources` directories, marked as source, test source, resource, and test resource folders (respectively) of the root module. These assignments can be changed via the right-click+**Mark Directory As** context menu command, or in the module's **Sources** tab in the **Project Structure** dialog.

Since Git tracks only files and their paths relative to the repository root, but not directories themselves, each of these directories initially contains a `.keep` file, so that the directory structure is preserved in Git. If/when files are added to any given one of these directories, the `.keep` file in that directory can be deleted.

# JAR artifact

A build specification for a JAR file is included in this starter. The intent is to build an executable JAR file from the root module. However, it will need to be modified in at least one respect (and probably more): The value of the `Main-Class` attribute in the `resources/META-INF/MANIFEST.MF` file must be set to the fully qualified name of the main class in the root module. This can be done directly in that file (there's no standard format for comments&mdash;let alone `TODO` comments&mdash;in JAR manifest files, so there isn't such a comment in this case), or under **Project Settings/Artifacts** in the **Project Structure** dialog.

In addition to setting the main class of the JAR, you will almost certainly want to modify the name of the JAR file that is created; this is done in **Project Settings/Artifacts** in the **Project Structure** dialog.

## Dependencies

We do not recommend that dependencies be incorporated directly as JAR files in the project, unless those JARs are _not_ otherwise accessible in the Maven Central repository. Instead, whenever possible, project dependencies should be declared (using Maven coordinates) in the **Project Settings/Libraries** list in the **Project Structure** dialog, and adding them to the relevant modules, with the appropriate scopes.

Note that JUnit4 is already included as a project dependency, and as a test-scoped dependency of the root module. Here are Maven coordinates and typical scopes of current versions (as of 2023-01-17) of just a few other frequently used libraries:

| Library | Maven coordinates                                    |  Scope  |
|:--------|:-----------------------------------------------------|:-------:|
| JUnit5  | `org.junit.jupiter:junit-jupiter-engine:5.9.2`       |  Test   |
| Gson    | `com.google.code.gson:gson:2.10.1`                   | Compile |
| Jackson | `com.fasterxml.jackson.core:jackson-databind:2.14.1` | Compile |
| Jansi   | `org.fusesource.jansi:jansi:2.4.0`                   | Compile |

When adding any of the above (and most dependencies, in general), don't forget to specify that transitive dependencies should be included.

## Version control

The `.gitignore` file included in this project repository is specifically intended for use with a native IntelliJ project. Please note that in some cases, the metadata structure for such a project combines local configuration settings (which usually wouldn't be tracked in version control) and build configuration settings (which should be tracked) in the same files. This can lead to needless merge conflicts, damaged project configurations, or both.

As noted in ["JDK version"](#jdk-version), the `.idea/misc.xml` can be particularly problematic. Among other elements, this file includes the name of the JDK used by the project (as configured in the **Project Structure** dialog); however, that name may vary from machine to machine, potentially resulting in merge conflicts if this file is placed under version control. 

On the other hand, in a project with external dependencies, artifacts, or multiple modules defined, the `.idea/libraries/` directory, `.idea/artifacts/` directory, or `.idea/modules.xml` file (respectively) should be tracked in version control. But if any of these are present when a project is first opened, and `.idea/misc.xml` is not, IntelliJ will have difficulty configuring the project correctly. Fixing this kind of configuration issue is most easily performed by closing a project, removing the `.idea` directory from the project directory, and re-opening it---but this could result in having to redefine the projects' dependencies, artifacts, or modules.

Given the above, it is **strongly suggested** that all of the developers working in the same repository configure IntelliJ so that they use _exactly_ the same name for the JDK used by the project. That way, including `.idea/misc.xml` in the repository won't cause merge conflicts, and there shouldn't be a need to reconfigure dependencies, artifacts, and modules when the project is first opened.

The list of included/excluded files and directories in `.gitignore` is close to&mdash;but not exactly the same as&mdash;the corresponding list for a Gradle or Maven project. If, at some moment, the project is converted to a Maven or Gradle project, the following steps should be taken _while the project is not open in IntelliJ:_

1. Remove the `.idea/` directory and all of its contents from the repository, using  the `git rm .idea/` command. 

2. All IML files should be removed from the repository, using the `git rm *.iml` command. 

3. Instruct Git to ignore those directories and files completely by replacing the relevant lines in `.gitignore` (lines 4&ndash;16, initially) with these two lines:

    ```gitignore
    *.iml
    .idea/
    ```
    
These changes (as well as the creation of the necessary build configuration files for Gradle or Maven) should be made by one team member, on one branch of the repository, then propagated (to remotes, other branches, other team members' local clones, etc.) via the usual Git mechanisms.