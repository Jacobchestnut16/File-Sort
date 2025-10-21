# Config file

Inside of the config folder of the project add a file called `config.json` either copy
the config example file or the example at the end of this file to get a pre-defined config that you may modify. 

## Main components

### Main-Path
```
    "Main-Path":"/home/$user/Documents/input", # defines where input begins
```
This folder is designated as the normal input folder for files that are undefined. This
does not mean this is the only input folder most storage-defs will be treated as a input
unless a policy is put in place to not.
### Storage-Defs
```
    "Storage-Defs":{
        "images":"/home/$user/images/", # define where things get stored
                                                 # can be narrow or broad
        "MULTI-PATH":{
            "BASE_PATH":"/home/$user/Documents/", # defines a base path
            "DEF_NAME":"dmv",
            "docs":"Docs", # now becomes /home/$user/Documents/Docs
            "music":"Music",
            "video":"Video",
        },
        "school":"/home/$user/School/",
    }
```
#### Path def
In the above example a path is simply wrote as `"images":"/home/$user/images/"` for future defs defining
how to treat the path you will use its name on the left.
#### MULTI-PATH def
Contains many components:
- `BASE_PATH` the base path for every following directory
- `DEF_NAME` the name of the def alike the above Path def `"images"`
  - To make future defs of every path inside of the base path use the DEF_NAME example from above is `dmv`
  - To make future defs of specific paths inside of the base path use the DEF_NAME/path def example for docs is `dmv/docs`
- defining paths inside of a multi-path is similar to making a path def `"docs":"Docs", # now becomes /home/$user/Documents/Docs` this just help you exclude rewriting */home/$user/Documents/* each time
### Defs
Defs get big and complicating, so I will try to break it down as much as I can

```
"Defs":{
        "images":{
```
#### group-by

```
            "group-by":["name","type"],
```
groups file(name) and/or file(type) and/or file(ext)into folders type is a higher priority than name eg file bird.png -> image/bird
folders are based on highest common name eg. many files are named bird with a break(_-.~|\ )<-"\ " is a space
then a folder called bird will be made

#### exclude
THESE ALL DO NOT EXCLUDE THE FILE FROM BEING SORTED IT EXCLUDES A FOLDER FROM BEING CREATED.
to exclude a file use `exclude-file` example at the end of the below code blob.
```
            "exclude-begins-with":["img"], # exclude files begin with this from creating a folder
                                           # does not exclude them from being sorted
                                           # only stops in this case from creating a folder named img
            
            "exclude-ends-with":["img"],   # opposite of beings with
            
            "exclude-contains":["img"],    # exclude is this is in the name
            
            "exclude-contains-break":true, # helps contains will force the check to be only catch
                                           # files that are named with a break
                                           # eg finds img 102, 102 img, 1 img 02 but will not fild lolimg1234
            
            "exclude-file":["lolimg1234"],  # exclude specific files from being grouped

```
#### reverse-lookup
```
            "reverse-lookup":true, # looks up every word in the name
                                   # (default false is to check only the first word)
                                   # moving everything based on highest valued name
                                   # eg Bird Raven, egal Bird
                                   # these both have birth in their name
                                   # they belong to a folder named bird/
```
#### waterfall
```
            "waterfall":false, # makes a folder for every multiple category
                               # eg files Bird egal, bird egal 2, bird raven
                               # there will be a folder called bird/
                               # and another  nested  called egal/
            
            "waterfall-priority-order":false, # this provides priority to names with most value
                                              # eg files Bird egal, bird egal 2, bird raven
                                              # two outputs egal/bird/ bird/
                                              # egal comes before bird since the word appears more times
                                              # any file named with just egal will easily show in egal/ 
                                              # without entering egal/bird unless it says both
            
            "waterfall-priority-merge":false, # this merges nested directories for
                                              # waterfall-priority-order does apply here
                                              # naming is based on most overall word 
                                              # eg Bird egal, bird egal 2, bird raven
                                              # two outputs bird-egal/ bird/
```
#### ext-defs overwrite
To reference *based on the below ext-defs* `"image":["png","jpg","jpeg","heic"]`
this is a way to overwrite main definitions for specific folders so below instead of nameing
all folders image it names the folder for eg `png,jpeg` as web-ready.
```
            "ext-defs":{
                # ext-defs that trumps the overhead one eg.
                "web-ready":["png","jpeg"],
                "high-quality":["heic","raw"]
                # right now jpg files will be the only file that gets sorted
                # into a folder called image (based on the below ext-defs)
            },
        },
```
You can also define aliases `"pics":"image"` this renames image to pics while reserving the rest of the definition
#### Multi-Path def example
(1 of 2 ways)
```
        "dmv/docs":{
            "group-by":["name","type"],
            "ext-defs":{
                "pdf":["pdf"],
                "docs":"text" # defines a alias
            },
```
(2 of 2 ways) //Not used in the full example

this defins the whole dmv base path and its children folders
```
        "dmv":{
            "group-by":["name","type"],
            "ext-defs":{
                "pdf":["pdf"],
                "docs":"text" # defines a alias
            },
```
#### ext-type
```
            "ext-type":["docs","sheet","pdf"], # defines the ext-types that can be sorted into this folder
```
#### exclude-path
```
            "exclude-path":false # searches everywhere from any known location eg Main-Path
                                 # and anything defined in Storage-Defs
                                 # Default is to only search Main-Path
        },
        "dmv/music":{
            "group-by":["name"],
            "ext-type":["audio"],
            "exclude-path":true # searches only inside of the directory $dmv["BASE-PATH"]+$dmv["music"]
        },
        "dmv/video":{
            "group-by":["name"],
            "ext-type":["video"],
            "exclude-path":["images","dmv"], # dmv here excludes $dmv["BASE-PATH"]+*
```
#### include-path
```
            "include-path":["dmv/video"] # defines an allowed path which has higher priority than the exclude-path
        },
        "school":{
            "group-by":["name","type"],
            "reverse-lookup":true,
            "waterfall":true,
            "exclude-begins-with":["Joe Schmo"],
            "exclude-contains-break":true,
```
#### tag
```
            "tag":["school","project","class","econ"] # files tagged with these will end up in this folder
                                                      # files without these tags are skipped
                                                      # if the folder contains the file without one of the tags
                                                      # the file will remain there and be sorted like any other file
                                                      # the tag def in any on of these does
                                                      # enforce a no look up policy so that other folders cannot
                                                      # remove a file from these folders
```
#### tag policy
```
            #"enforce-tag-policy":false # default is true
                                        # when this is false
                                        # files without these tags may be resorted somewhere else
                                        # this may also be defined to [low|medium|high]
                                        # which stages how much to enforce
            # low - any file without a tag may be moved if it doesn"t have a matched name
                                                            # (eg exclude-[begins-with|ends-with|contains] definition
                                                            # defines a matchable name
                                                            # or use "match-name"
            # medium - any file not in a child directory of the parent/def may be moved
            # high - any file without a tag may be reviewed
        },
```
#### default
similar to how switches work this is the catch all of whatever was not caught from 
above this will attempt to find the best home otherwise its 
sorted inside of the main input path
```
        "default":{ # gets applied to anything that is not defined above
            "group-by":["name","type"],
            "reverse-lookup":false,
            "waterfall":false,
        }
    }
```
### ext-defs
```
    "ext-defs":{
        # file extension class. this defines group-by type folder named
        # format
        # classname: [ext(without the \.),...]
        "image":["png","jpg","jpeg","heic"],
        "video":["mp4","avi","mkv","mpg","mpeg","avi","mkv"],
        "audio":["mp3","wav","ogg","flac","wav","aiff","ogg"],
        "presentation":["ppt","pptx"],
        "text":["txt","odt","docx"],
        "sheet":["xls","xlsx","csv"],
        "database":["sqlite","sqlite3"],
        "webpage":["htm","html","htm","htm"],
        "stylesheet":["css","css"],
        "program":["py","pyc","js"],
        "executable":["exe","dmg","sh","bat","jar"],
    }
``` 
Define the name, *a capitolized version will be the folder name*, 
on the right define an array of file extensions without the `.` that makes up the ext class.

Below is a full example of a config file that is copy and pate ready
```
{
    "Main-Path":"/home/$user/Documents/input",
    "Storage-Defs":{
        "images":"/home/$user/images/",
        "MULTI-PATH":{
            "BASE_PATH":"/home/$user/Documents/",
            "DEF_NAME":"dmv",
            "docs":"Docs",
            "music":"Music",
            "video":"Video"
        },
        "school":"/home/$user/School/"
    },
    "Defs":{
        "images":{
            "group-by":["name","type"],
            "exclude-begins-with":["img"],
            "exclude-ends-with":["img"],  
            "exclude-contains":["img"],   
            "exclude-contains-break":true,
            "exclude-file":["lolimg1234"],
            "reverse-lookup":true,
            "waterfall":false,
            "waterfall-priority-order":false,
            "waterfall-priority-merge":false,
            "ext-defs":{
                "web-ready":["png","jpeg"],
                "high-quality":["heic","raw"]
            }
        },
        "dmv/docs":{
            "group-by":["name","type"],
            "ext-defs":{
                "pdf":["pdf"],
                "docs":"text"
            },
            "ext-type":["docs","sheet","pdf"], 
            "exclude-path":false
        },
        "dmv/music":{
            "group-by":["name"],
            "ext-type":["audio"],
            "exclude-path":true
        },
        "dmv/video":{
            "group-by":["name"],
            "ext-type":["video"],
            "exclude-path":["images","dmv"], 
            "include-path":["dmv/video"]
        },
        "school":{
            "group-by":["name","type"],
            "reverse-lookup":true,
            "waterfall":true,
            "exclude-begins-with":["Joe Schmo"],
            "exclude-contains-break":true,
            "tag":["school","project","class","econ"],
            "enforce-tag-policy":true 
        },
        "default":{ 
            "group-by":["name","type"],
            "reverse-lookup":false,
            "waterfall":false
        }
    },
    "ext-defs":{
        "image":["png","jpg","jpeg","heic"],
        "video":["mp4","avi","mkv","mpg","mpeg","avi","mkv"],
        "audio":["mp3","wav","ogg","flac","wav","aiff","ogg"],
        "presentation":["ppt","pptx"],
        "text":["txt","odt","docx"],
        "sheet":["xls","xlsx","csv"],
        "database":["sqlite","sqlite3"],
        "webpage":["htm","html","htm","htm"],
        "stylesheet":["css","css"],
        "program":["py","pyc","js"],
        "executable":["exe","dmg","sh","bat","jar"]
    }
}
```