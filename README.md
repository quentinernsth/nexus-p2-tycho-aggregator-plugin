Patched Repository Aggregation
------------------------------
<b> Management Summary:</b> This patched plug-in uses Tycho generated p2-metadata to aggregate the p2-repository. Experimental.

I created a fork and pushed my modifications to it. It is highly untested and hackish, can surely be optimized and will only work if you build your bundles and features with Tycho. It does not work for artifacts of classifier "binary". 

Build the jar (unfortunately the tests do not happen, but the jar is materialized correctly). Install the nexus-p2-bridge-plugin and nexus-capabilities-plugin (both in version 2.0) and the nexus-p2-repository-plugin in version 2.1-SNAPSHOT (get the bundled version from repository.sonatype.org) to the plugin-repository. Than replace the jar in the nexus-p2-repository-plugin with the one you build (it defines its dependencies to the 2.0 versions).   

The nexus-p2-repository-plugin basically provides two features: A p2-Metadata Generator, which is not touched by this patch, and a p2-Metadata Aggregator. I modified this Aggretator to use the metadata that is already there from the Tycho build (and not the ones from the Generator, so you don't need to run the Generator-Task). This was possible because the xml filenames are slightly diffrent:

The Nexus Plug-In generates

{artifactId}p2Artifacts.xml and {artifactId}p2Content.xml

Tycho is uploading this ones

{artifactId}p2artifacts.xml and {artifactId}p2metadata.xml

Unfortunately, the files generated by Tycho do not frame their content in a proper <repository> header, and so the p2 runtime called by the plug-in refuses to do the merge job. So I had to add the missing lines to both files before merging.

Afterwards I had to find a new approach to create the links from the /plugins and /features to the physical jars. This was quite difficult and the solution I found is pretty hackish (but works for me). 

Thats it. Additionally, I had to adjust some dependencies within the pom.xml, otherwise the plug-in wont compile (see the thread on this list).