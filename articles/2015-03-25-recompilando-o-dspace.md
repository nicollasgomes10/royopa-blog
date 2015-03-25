{
"title" : "Recompilando o DSpace",
"author":"Royopa",
"date":"25-03-2015",
"tag":"dspace",
"slug" : "recompilando-o-dspace",
"category":"DSpace"
}

https://wiki.duraspace.org/display/DSPACE/Rebuild+DSpace

Rebuild DSpace
Icon
The following directions are for DSpace versions from 1.5.x up to 1.8.x. For DSpace 3 and above, see the official docs for the appropriate version (3.x, 4.x).
Directories:
[dspace] - The DSpace Installation directory
[dspace-source]/dspace/ - The DSpace Assembly project within the DSpace source code
[dspace-source]/dspace/target/dspace-[version]-build.dir/ - The directory where the DSpace Assembly project builds a new installation package for DSpace.
[Tomcat]/webapps/ (Mac OSX Server: /library/jboss/3.2/deploy)
Note: JBOSS comes pre-installed with Mac OS X server. However, for both server and desktop editions Tomcat may be used as with other platforms.
Quick Restart (Just restarts the web server after configuration changes*)
(*Exception – Changes to Messages.properties always requires a rebuild!)
Stop Tomcat (WARNING: this will bring down the website)
(Linux / OS X / Solaris) [Tomcat]/bin/shutdown.sh
(Mac OS X Server) Use Server Admin to stop Tomcat ("Application Server")
(Windows) Use Tomcat Service Monitor (in Notification Area) to stop Tomcat
Start Tomcat
(Linux / OS X / Solaris) [Tomcat]/bin/startup.sh
(Mac OS X Server) Use Server Admin to start Tomcat ("Application Server")
(Windows) Use Tomcat Service Monitor (in Notification Area) to start Tomcat
Quick Build: (Quick build after smaller, usually JSP based or XMLUI-Theme based, changes)
Log on to the server DSpace is running on (e.g. ssh). Make sure to login as the user who initially installed DSpace!
Open a command prompt (if you don't have one already), and cd [dspace-source]/dspace/
mvn package (recompiles all DSpace code and rebuilds the DSpace installation package)
cd [dspace-source]/dspace/target/dspace-[version]-build.dir/ (move into the target directory where DSpace has been rebuilt)
ant update (updates your DSpace install based on the newly built content in your targetdirectory)
Alternatively, if you do not need to reinstall JAR files, you could instead run ant update_webapps (which just copies over web application changes to your [dspace])
If you do not have Tomcat pointing at your [dspace]/webapps/ directory, you will also need to copy your final web application(s) into your [Tomcat]/webapps/directory.
cp -R [dspace]/webapps/ [Tomcat]/webapps/
(For Mac OS X Server) cp build/*.war /library/jboss/3.2/deploy
Test your changes in DSpace
Full Refresh/Rebuild: (Completely refresh all of DSpace)
Log on to the server DSpace is running on (e.g. ssh). Make sure to log in as the user who initially installed DSpace!
Open a command prompt (if you don't have one already), and cd [dspace-source]/dspace/
mvn clean package (removes all old compiled code and recompiles all DSpace code and rebuilds the DSpace installation package)
cd [dspace-source]/dspace/target/dspace-[version]-build.dir/ (move into the target directory where DSpace has been rebuilt)
ant update (updates your DSpace install based on the newly built content in your targetdirectory)
Alternatively, if you do not need to reinstall JAR files, you could instead run ant update_webapps (which just copies over web application changes to your [dspace])
If you do not have Tomcat pointing at your [dspace]/webapps/ directory, you will also need to copy your final web application(s) into your [Tomcat]/webapps/directory.
cp -R [dspace]/webapps/ [Tomcat]/webapps/
(For Mac OS X Server) cp build/*.war /library/jboss/3.2/deploy
To force Tomcat to recompile everything, you may also wish to remove any DSpace related web application directories created in [Tomcat]/work/Catalina/localhost
Start Tomcat
(Linux / OS X / Solaris) [Tomcat]/bin/startup.sh
(Mac OS X Server) Use Server Admin to start Tomcat ("Application Server")
(Windows) Use Tomcat Service Monitor (in Notification Area) to start Tomcat
Test your changes in DSpace
Exclude individual modules
It doesn't happen often that someone needs all of the DSpace modules. Most prominent example are the web interfaces - most people need either JSPUI or XMLUI, but not both. You may cut down on build time by excluding unneeded modules. But beware, some modules have hidden interdependencies, so it's not always possible to exclude all modules. If a build fails, it may be for this reason. In that case try the full build again.
To build dspace without the JSPUI and LNI modules ("profiles" in Maven terminology), add this option to the Maven command:
mvn package -P !dspace-jspui,!dspace-lni
The full list of DSpace modules that you may choose to exclude from the build include:
dspace-jspui (JSPUI)
dspace-lni (LNI)
dspace-oai (OAI-PMH)
dspace-sword (SWORD v.1)
dspace-swordv2 (SWORD v.2)
dspace-xmlui (XMLUI)
