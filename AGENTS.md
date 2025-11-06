# AGENT INSTRUCTIONS
- Read this entire document and follow it's process strictly. Details matter here.
- Do not re-invent the wheel.  Compare the descriptions of existing part of the project using ```./wheel.sh``` and compare the descriptions columns with what the user's prompt requirements are to find existing code paths and reusable codepaths instead of fully implementing code.
- Each section of this document is a critical part of the system and cannot be overlooked.
- DO NOT RE-INVENT THE WHEEL!!

# Agentic Data Storage and retrieval 
- provides accurate data retrieval and storage about a project
- decrease overhead by caching lots of data in a database
- replaces the need for massive context windows filled with file contents by using targeted sql statements to get relevant data

## when sqlite3 is available
- will use a local database to store project data 

### WHEEL.db
#### setup
- Use ```./wheel.sh``` to interact with the database.
- The database is in a sqlite3-backed file named `WHEEL.db` with the following tables.

> * `files` (id, relpath, description) // project files with detailed descriptions
> * `defs` (id, file_id, type, signature, parameters, description) // type is property, function, or signal (or any  other public construct accessible from other constructs, no private members). descriptions are paramount here and must be well-documented for every function and signal, and to lesser degree every property
> * `changes` (id, title,  context, status) // will be for change tracking
> * `todo` (id, change_id, def_id, file_id, change_id, description) // todo items to be done, one item for each definition in each definition of each file that is to be added or changed or removed. Must have all fields completed before starting work
>
> [OPTIONAL] `change_files` (id, change_id, file_id) // optional chage file tracking per change (for complex tasks with many changes across multiple files)
> [OPTIONAL] `change_defs` (id, change_id, file_id, def_id) // optional definition tracking per change (for complex tasks with many changes to many definitions)

- chmod +x wheel.sh 

- wheel.sh will verify and import the database from WHEEL.sql if WHEEL.db doesnt exist. 
- if WHEEL.sql is missing, then a blank database with the appropriate file structure will be created. 
- When a blank WHEEL.db is created from the condition where no WHEEL.db and no WHEEL.sql were present, scan the  project directory for source files and add their accessible definitions (functions, methods, exports) to the database with ```./wheel.sh defs``` (see ```./wheel.sh defs --help```)



### sqlite3 database tools for WHEEL.db 

#### wheel.sh
- You can choose to use this shortcut tool which provides access to WHEEL.db through a convenience shell script: 
> ``` wheel.sh ```

- Many common operations are already supported and relevant data can quickly be accessed.

- There are many more use cases for wheel.sh and prefer it over using sqlite3 direct statements or reading file contents into context. 
> ``` ./wheel.sh --help```

- How to query and merge multiple tables (INNER JOIN) -- use the --merge parameter  lke "ON" and multiple --table parameters with space-separated columns
> ```./wheel.sh query  files.relpath defs.signature defs.description --table files --table defs --merge defs.file_id,files.id```

- How to do a keyword search for rows containing all keywords in the same row (over all columns):
> ``` ./wheel.sh search term1 term2 term3 ... termX --table defs```

- How to keyword search and return specific columns but search the entire row:
> ```` ./wheel.sh search term1 term2 ... termX --table files --columns file.relpath,file.description```

- Manipulate todo lists  -- see ```./wheel.sh --help``` for more info
> ``` ./wheel.sh todo [list/add/del/search]``` 

- Also available shortcuts are:
> ```./wheel.sh files [list/add/del/search]```
> ``` ./wheel.sh defs [list/add/del/search] ```
> ``` ./wheel.sh changes [list/add/del/search] ```

- Many shortcuts have additional effort saving implementations such as 
> ```./wheel changes search --file ".cpp" ```  // finds all changes to files containing .cpp in their name

- You can chain together multiple calls to wheel.sh to aggregate information too. For example to see what functions were modified by the latest change record:
> ``` ./wheel.sh changes list  | cut -f1 -d  |  tail -n +3 | egrep "[0-9]+" | \
      tail --lines=1 | xargs -I{} ./wheel.sh query change_defs --where "change_id={}" --columns "def_id" | \
      tail -n +3 | xargs -I%% ./wheel.sh query defs --where "defs.id=%%" ```  

#### Uses

- Use wheel.sh to quickly find functions and their purpose in project files without reading though every single file or doing complex queries.
- Every function/method/routine/member that can be accessed or executed should be added to WHEEL.db via` wheel.sh` with the signature and parameters whenever new code is added.
- Always check with `wheel.sh` output to see what other things have already been invented in this project before creating implementation steps or writing code.
- Always reference `wheel.sh` output when directly when writing code and utilize existing types or helpers when possible instead of creating new ones. Try to use base classes to decrease the amount of overall code paths in the application by reusing existing ones when possible.
- If working with complex chages spanning multiple files, always before making any changes, create a new change entry using `wheel.sh` with specific details. For each definition that will be changed, add a new row into change_defs with the file_id and def_id or by using ./wheel.sh change_defs add --file "relpath" --def "signature" or --def_id 24 --file_id 14
- Once finished with the change table, then use ```./wheel.sh todo add```  command to add specific todo step needing to be changed and create todo items based on that output. This is not always necessary when the all of the changes can be made with minimal effort. 
- When requesting approval for changes, make sure to use output from wheel.sh for providing data. do not rely on contex window memory for reporting, reviewing, or notifying. 
- Do not read  WHEEL.sql into context window. It is automatically generated by ./wheel.sh and contains the latest database changes in text forma so that the database can be distributed in a non-binary format.
- If sqlite3 not available on system, track all of the data listed into a file called WHEEL.md which will contain each file, definitions (and descriptions off them)  but no change tracking will be put into WHEEL.md (to prevent context overload)
- Do not read or consider `WHEEL.sql` into the internal agent context.


# GENERAL CODING SUGGESTIONS
* Break down large problems into multiple simple specfic steps when creating Implementation steps.
* Dont reinvent the wheel or modify existing code unless absolutely necessary.
* Look for a way to implement changes by using existing code first, then if not possible, create new code paths.
* Work slowly and go step-by-step to make compact, requirement fulfilling, working, elegant code.
