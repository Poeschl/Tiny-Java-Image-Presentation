:css: style.css

.. title:: Tiny Java Images

----

:data-x: r2200
:data-y: 0
:class: cover-class

.. image:: images/title.png

Tiny Java Container Images
==========================

The journey from 333 to 80 MB
-----------------------------

Markus Pöschl

----

:class: right-class

.. image:: images/example-folder.png
   :width: 600px

Starting point
--------------

* Jar artifact of "HandsOn Kittens"
    * Spring Boot
    * Webserver on Port 8080 serving 1 kitten image
    * ~18 MB
* Dockerfile with Java 17 Eclipse Temurin JDK
    * from Adoptium (Eclipse Foundation)

----

:class: right-class

.. image:: images/jdk-layers.png
   :width: 800px

Lets look into the built image
------------------------------

* Tool `dive` to analyse the image
* Total size: *333 MB*
    * First 5 layers from the base image
        * 30 MB for JDK requriements
        * 278 MB for the Temurin JDK
    * Last layer is our Jar artifact
        * 18 MB
* Do we really need the JDK?
    * Temurin also provides a JRE

----

:class: right-class

.. image:: images/jre-diff.png
   :width: 600px


Build again with JRE
--------------------

* `17-jdk-alpine` -> `17-jre-alpine`
* Only runtime environment available

----

:class: right-class

.. image:: images/jre-layers.png
   :width: 800px

Look at the image internals again
---------------------------------

* Total size: *195 MB* (59% of initial image)
* JRE 140 MB (138 MB smaller as JDK)
* Can we get the JRE even smaller?

----

:class: right-class

.. image:: images/java-modules.png
   :width: 600px

Let's talk about Java modules
-----------------------------

* Since Java 9 the JDK is split into 95 modules (at the time of Java 9)
    * Encapsulated and mostly independent
* Modules can be individually used to build a custom JRE for every usecase
    * ``jlink`` as tool to build it
* Calls from classes inside a JAR can be analysed
    * ``jdeps`` as tool of choice

----

:data-x: r0
:data-y: r1200

``jdeps``
------------------

* Dependency analysis tool
* Bytecode (class files or JARs)
* Tells which internal java APIs are used
* Available since Java 9
* Maven/Gradle adaptions available

``jlink``
------------------

* Creates custom JRE images
* Advantages of a custom-tailored JRE
    * smaller memory usage
    * smaller size

----

:data-x: r2200
:data-y: r0

Build a custom JRE
------------------

* Steps
    1. Analyse dependencies
    2. Build custom JRE
    3. Build final image
* Pitfalls
    * JDK 17 has a bug inside detecting logic
        * Better use the one from JDK 18
    * Spring Boot artifact is a custom "fat JAR"
        * Dependencies are in ``BOOT-INF`` folder
        * Unpack the custom structure first

----

:data-x: r0
:data-y: r1200

:class: right-class

.. image:: images/jdeps-stage.png
   :width: 800px

Build a custom JRE - ``jdeps`` Code
-----------------------------------

* Use Dockerfile as multi-stage build
    * Specify multiple stages in one files
    * Will build through all stages
    * Last stage will get exported as image
* First stage is used to detect dependencies
    * ``/deps.info`` contains list of used modules
    * Looks like ``java.base,java.desktop,java.instrument,...``

----

:class: right-class

.. image:: images/jlink-stage.png
   :width: 800px

Build a custom JRE - ``jlink`` Code
-----------------------------------

* Second stage to build the JRE
    * Use the dependency information from first stage
    * Removal of additional things like man pages or header files
    * Creates the build JRE at ``/slim-jre``

----

:class: right-class

.. image:: images/final-stage.png
   :width: 800px

Build a custom JRE - Final image
--------------------------------

* Last stage create the final image
    * Use alpine as base image
    * Copy JRE into it
    * Copy JAR artifact (similar to Dockerfile before)

----

:data-x: r2200
:data-y: r0

:class: right-class

.. image:: images/custom-layers.png
   :width: 800px

Dive in the custom tailored image
---------------------------------

* Total size: *80 MB* (24% of initial image)
* Custom JRE 56 MB (84 MB smaller as regular JRE)

----

:class: right-class

.. image:: images/ytho.gif
   :width: 400px

But why though?
---------------

* Smaller storage footprint of image
    * Saves space on paid environments (GitHub)
    * Faster transmission of image
* Smaller memory usage at runtime
    * Unused JRE modules aren't loaded

Final image Sizes:
    * JDK: 333 MB
    * JRE: 140 MB
    * Custom JRE: 80 MB

----

:class: right-class

Even smaller? ThinJar
---------------------

* Spring Boot has a experimental Thin Launcher project
* Dependencies will get downloaded from maven central on first run
* Requires internet connection
* https://github.com/spring-projects-experimental/spring-boot-thin-launcher
