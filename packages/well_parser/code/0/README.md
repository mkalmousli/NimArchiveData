# Well parser

![image](./empower.png)

About
=====

This project is intended to parse Texas Railroad Commission data provided in an unsuitable and non-transparent format. As of January 2024, this code is able to parse [Drilling Permit Master and Trailer (Includes Latitudes and Longitudes)](https://www.rrc.texas.gov/media/zgccdjpi/drillingpermitmasterpluslatitudeslongitudes_oga049m_july1.pdf) and [Underground Injection Control Data](https://www.rrc.texas.gov/media/v3onmigl/uic_manual_uia010_3116.pdf)

Future development of other datasets is not guaranteed due to time constraints.

Installation
=====

You can install this by downloading the git, cd'ing to it and running

```
nimble install
```

or, when available through the Nim repository:

```
nimble install well_parser
```


Usage
=====
You can either convert the data to `json` format or insert the data in MongoDB.

### Drilling Permits

For [drilling permits](https://www.rrc.texas.gov/media/zgccdjpi/drillingpermitmasterpluslatitudeslongitudes_oga049m_july1.pdf) you can convert the data to `json` with the following code:

```nim
let jsonConversion = getDrillingData("/path/drilling_wells/daf420.dat.11-30-2023")
# and save file
jsonConversion.writeJson("/path/output.json")
```

To insert data to a MongoDb collection the following example may be used:

```nim
import well_parser, anonimongo, asyncdispatch

const 
    pathToFile = "/path/drilling_wells/daf420.dat.11-30-2023"
    URI = "mongodb://localhost:27017/"

var
   mongoDB = newMongo[AsyncSocket](MongoUri URI)
   localdb = mongoDB["texas-ccs"]
   col = localdb["drilling-permits"]
   iteration = 0

assert waitFor mongoDB.connect 
let s = getDrillingDataMongo(pathToFile)
for stuff in s:
    try:
        let insertM = waitFor col.insert(@[stuff])
        if not insertM.success:
            echo "Something went wrong!"
            break
    except MongoError:
        echo stuff
        quit()
```

If you require authentication, please refer to [Anonimongo's](https://github.com/mashingan/anonimongo) documentation.


### Injection wells

For [Underground Injection Control Data](https://www.rrc.texas.gov/media/v3onmigl/uic_manual_uia010_3116.pdf) converting to `json` differs slightly. As the file is one big flat file, `json` files are divided into chunks:

```nim
let sizeOfChunk = 500
getInjectionData("/path/to/uif700a.txt", "/directory/output/, sizeOfChunk)
```

this will create several jsons with a size of 500 elements.

To insert documents to MongoDB for the first time:

```nim
import well_parser, anonimongo, asyncdispatch

const 
    pathToFile = "/media/steamgames/uif700a.txt"
    URI = "mongodb://localhost:27017/"
    
var
   mongoDB = newMongo[AsyncSocket](MongoUri URI)
   localdb = mongoDB["texas-ccs"]
   col = localdb["injection-wells"]
   
assert waitFor mongoDB.connect
col.getInjectionDataMongo(pathToFile, false)
```

This will parse the entire file and insert every single injection well. If users wish to only update the collection the final argument of the `getInjectionDataMongo` proc needs to be changed to:

```nim 
...
col.getInjectionDataMongo(pathToFile, true)
```

This will still traverse the entire file, but only insert new documents.
