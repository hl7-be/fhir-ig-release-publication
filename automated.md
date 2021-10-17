Script for automated releases
The release-py script does:

1. Opens a release token json file which contains the following data about the release that we want to publish:
```json
{
    "ig": "fhir",
    "version": "1.2.2",
    "status": "release",
    "date": "2021-10-10",
    "desc": "eHealth",
    "sequence": "Official Releases",
    "history_template_repo": "https://github.com/hl7-be/fhir-ig-history-template.git",
    "intro":"These are the publications for this ImplementationGuide ",
    "category" :"Federal",
    "ci_url" :"http://build.fhir.org/ig/hl7-be/be-core",
    "org" :"eHealth"  
}
    
```

2. opens the ig (.xml or .json) and extracts the following data
* id
* url
* title
* version
* packageId
* fhirVersion

3. validates that the IG matches what is in the release token described in 1.

4. ensures that the ig's url is in the form {base}/{id}/ImplementationGuide/{id}

5. download the ig-history repository 

6. download the ig-registry repository

7. run the publisher once to see if it's ok

8. creates a folder called `./www` under the ig root (i.e. sibling to output, input, etc.)

9. copy the contents of the history template to the `./www` folder

10. read the `./www/publish.ini` if it exists, or creates a new one if not

11. reads the package_list.json if it exists. If not, creates one with default content
* package_id
* title (obtained from the IG title)
* canonical (obtained from the IG url)
* introduction (obtained from the release token)
* category (obtained from the release token)
* list
   * version 
   * desc (obtained from the release token)
   * path (obtained from the release token)
   * status (obtained from the release token)
   * current ('true' - it's assumed that the version we are publishing is the current one)

12. Add the current release to the package-list:
* path (obtained from the IG url)
* fhirVersion (obtained from the IG url)
* current = true

13. Checks and warns if the package_list already contains the version we are about to publish.

14. copy the content of the `output` folder to the `www` folder

15. copy the content of the `output` folder to the `www/{release}` folder

16. run the publisher.update:
`java -jar ..\publisher.jar -publish-update -root . -folder www -registry ./ig-registry/fhir-ig-list.json -history ./fhir-ig-history-template'`

17. at the end, replace the `assets-dist` folder content with the content of the `assets`

