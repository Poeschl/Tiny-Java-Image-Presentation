:css: style.css

.. title:: Tiny Java Images

----

:data-x: r2200
:data-y: 0
:class: cover-class

.. image:: images/title.png

Tiny Java Container Images
==========================

The journey from 333 to 60 MB
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
* Dockerfile with Java 17 Temurin JDK

----

:class: right-class

.. image:: images/jdk-layers.png
   :width: 800px

Lets look into the build image
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

Let's talk about Java modules
-----------------------------

* Since Java 9 the JDK is split into 95 modules (at the time of Java 9)
  * Encapsulated and mostly independent
* Modules can be individually used to build a custom JRE for every usecase
  * `jlink` as tool to build it
* Calls from classes inside a jar can be analysed
  * `jdeps` is the tool of choice

----

JLink
-----

----

Build our own custom JRE
-------------------------

