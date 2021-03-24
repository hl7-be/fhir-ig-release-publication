# Process for Publishing a FHIR Implementation Guide (for non-HL7 IGs)

This process describes a way to publish implementationGuides to 



# 1. Prepare the environment

1. Create a folder for your implementation guide publication (all releases of the IG will be in added to that folder, and the folder can be updated to the web server)
 e.g. Publication
<br/>

2. Download the publisher jar to this folder
<br/>

3. Clone the history template to a folder called fhir-ig-history-template in this folder (or add it as a submodule if you want)  
```
git clone https://github.com/hl7-be/fhir-ig-history-template.git fhir-ig-history-template
```
You can use other history templates:
```
git clone https://github.com/openhie/fhir-ig-history-template.git fhir-ig-history-template
git clone https://github.com/HL7/fhir-ig-history-template.git fhir-ig-history-template
git clone https://github.com/IHE/fhir-ig-history-template.git fhir-ig-history-template (soon)
```
<br/>

4. Add the registry (clone or submodule) submodule to a folder called ig-registry   

```
git clone https://github.com/FHIR/ig-registry.git
```
<br/>

<br/>
5.  Copy the IG root folder to a folder called **{ig-root}** e.g. ***my-ig*** under this working folder  

<br/>
<br/>

6. Change the working folder to the IG root folder  

```
example: cd my-ig
```
<br/>
<br/>
7. Create a folder that will be your web root (typically the folder that you will publish to the web)  

```
example: md www
```
<br/>

<br/>
8. Copy history template to web root folder

```
example: xcopy /s /y ..\fhir-ig-history-template\* www
```

<br/>
9. Create a publish.ini in the webroot folder, like this:  

```ini
[website]
style=fhir.layout
server=apache
url= http://hl7belgium.org/fhir/core
org=HIE
no-registry=1

[feeds]
package=package-feed.xml
publication=publication-feed.xml
```

Note: the url must be same as the IG's canonical URL



<br/>
<br/>
<br/>

# 2. Setup the IG for publication  

1. Change or retain these parameters from the ImplementationGuide:  

```
{ig_id}: ImplementationGuide.id: e.g. be-core
{ig_url}: ImplementationGuide.url: e.g. http://hl7belgium.org/fhir/be-core/ImplementationGuide/be-core
     this should take the form {base}/{ig_id}/ImplementationGuide/{ig_id}
{ig_version}: ImplementationGuide.version: e.g. 0.1.0
{ig_package_id}: ImplementationGuide.packageId: e.g. hl7.be.fhir.be-core
{ig_fhir_version}: ImplementationGuide.fhirVersion: e.g. 4.0.1
```

<br/>
2.  Check the ig filename. Best if it is in the form
**{ig\_id}**.json /  **{ig\_id}**.xml  
<br/>
<br/>

3. Run the publisher to make sure everything is ok:  

```
java -jar ..\publisher.jar -ig ig.ini
```
<br/>
<br/>

4. Create a file called package-list.json in the webroot folder. First, it only includes the current version for CI-build  

```json
{
  "package-id": "{ig_package_id}",
  "title": "Belgium HL7 Core specifications",
  "canonical": "{canonical}",
  "introduction": "Belgium HL7 Core specifications",
  "category": "Research",
  "list": [
    {
      "version": "current",
      "desc": "Continuous Integration Build",
      "path": {canonical},
      "status": "ci-build",
      "current": true
    }
  ]
}
```
Note:
  - **{canonical}** = **{base}**/**{ig-id}** e.g.  
    http://hl7belgium.org/fhir/be-core
  - make sure there is a category



# 3.	Add a new release (run this for every new release)  

<br/>
1. Check and/or change these IG parameters:
• ```{ig_version}```: ImplementationGuide.version: e.g. 0.1.0
• ```{ig_status}```: ImplementationGuide.status: e.g. draft
• ```{ig_fhir_version}```: ImplementationGuide.fhirVersion: e.g. 4.0.1

<br/>
<br/>

2.	Do not change:  

• ```{ig_id}```: ImplementationGuide.id: e.g. be-core
• ```{ig_url}```: ImplementationGuide.url: e.g. http://hl7belgium.org/fhir/be-core/ImplementationGuide/be-core
• ```{ig_package_id}```: ImplementationGuide.packageId: e.g. hl7.be.fhir.be-core

<br/>
<br/>

3.	Define the following for the new release:   

• ```ig_id```: the IG id, must be the same as **{ig_version}**
• ```rel_version```: the version of the IG, must be the same as **{ig_version}**
• ```rel_fhir_version```: the version of the IG, must be the same as **{ig_fhir_version}**
• ```rel_status```: the publication status (see valueset above)
• ```rel_desc```: the description of this release
• ```rel_date```: the date of this release

<br/>
<br/>

4.	Run publication to make sure everything is ok:  

```java -jar ..\publisher.jar -ig ig.ini```

<br/>
<br/>

5.	Run the publisher with -publish option  

```.\_genonce.bat -publish (base)/(ig_id)/(ig_version)```
e.g.
```.\_genonce.bat -publish http://hl7-belgium.org/fhir/be-core/0.1.0```


<br/>
<br/>

6.	add a list entry to package-list.json  

```json
{
  "version" = {ig_version}, 
  "date" = {rel-date},
  "desc" = {rel-desc},
  "path" = {canonical}/{ig_version}, 
  "status" = {rel-status}, 
  "sequence" = ... 
  "current" = true *
  fhir_version = {rel_fhir_version}, 
  changes: (optional link to a page describing changes)
}
```
example:   
```json
{
  "version": "0.2.0",
  "date": "2021-03-19",
  "desc": "Belgium HL7 Core specifications - Release 0.2.0",
  "path": "http://hl7belgium.org/fhir/be.core/0.2.0",
  "status": "trial-use",
  "sequence": "Trial Use 2",
  "fhirversion": "4.0.1",
  "current": true
}
```
* *Note: Besides the CI build version that should stay the same, only one entry can have "current": true*  
<br/>
<br/>

7. Copy the output content to the root folder  

```xcopy /s /y output <webroot>```
e.g.
```xcopy /s /y output www```

<br/>
<br/>

8. copy the output content to a folder named after the version  

```xcopy /s /y output {webroot}\{ig_version}\```
e.g.
```xcopy /s /y output www\0.2.0\```

<br/>
<br/>

9. Run the publisher in update mode  

```java -jar ..\publisher.jar -publish-update -folder <webroot> -registry ..\ig-registry\fhir-ig-list.json -history ..\fhir-ig-history-template```
e.g. 
```java -jar ..\publisher.jar -publish-update -folder www -registry ..\ig-registry\fhir-ig-list.json -history ..\fhir-ig-history-template```

<br/>
<br/>

10.	Just in case, IF you have a custom template, copy the template’s style files to history (we’ll fix this)  

```xcopy /s /y <webroot>\assets <webroot>\assets-hist```
e.g. 
```xcopy /s /y www\assets www\assets-hist```
                 
