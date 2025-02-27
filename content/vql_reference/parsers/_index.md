---
title: Parsers
weight: 30
linktitle: Parsers
index: true
---

Many Velociraptor artifacts rely on specialized parsing of file
formats. This page outlines all the plugins and functions designed
to allow the client to parse information for various files.

Simple file formats may be parsed using regular expressions and
other generic rules. However some specialized file formats have
dedicated parsers. These dedicated parsers are exported into VQL
plugins so their results may be used in further queries.


<div class="vql_item"></div>


## grok
<span class='vql_type pull-right'>Function</span>

Parse a string using a Grok expression.

This is most useful for parsing syslog style logs (e.g. IIS, Apache logs).

You can read more about GROK expressions here
https://www.elastic.co/blog/do-you-grok-grok




<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
grok|Grok pattern.|string (required)
data|String to parse.|string (required)
patterns|Additional patterns.|Any
all_captures|Extract all captures.|bool



<div class="vql_item"></div>


## olevba
<span class='vql_type pull-right'>Plugin</span>

Extracts VBA Macros from Office documents.

This plugin parses the provided files as OLE documents in order to
recover VB macro code. A single document can have multiple code
objects, and each such code object is emitted as a row.




<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
file|A list of filenames to open as OLE files.|list of string (required)
accessor|The accessor to use.|string
max_size|Maximum size of file we load into memory.|int64



<div class="vql_item"></div>


## parse_auditd
<span class='vql_type pull-right'>Plugin</span>

Parse log files generated by auditd.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
filename|A list of log files to parse.|list of string (required)
accessor|The accessor to use.|string
buffer_size|Maximum size of line buffer.|int



<div class="vql_item"></div>


## parse_binary
<span class='vql_type pull-right'>Function</span>

Parse a binary file into a datastructure using a profile.

This plugin extract binary data from strings. It works by applying
a profile to the binary string and generating an object from
that. Profiles are a json structure describing the binary format. For
example a profile might be:

```json
[
  ["StructName", 10, [
     ["field1", 2, "unsigned int"],
     ["field2", 6, "unsigned long long"],
   ]]]
]
```

The profile is compiled and overlayed on top of the offset specified,
then the object is emitted with its required fields.

You can read more about profiles here https://github.com/Velocidex/vtypes




<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
filename|Binary file to open.|string (required)
accessor|The accessor to use|string
profile|Profile to use (see https://github.com/Velocidex/vtypes).|string
struct|Name of the struct in the profile to instantiate.|string (required)
offset|Start parsing from this offset|int64



<div class="vql_item"></div>


## parse_csv
<span class='vql_type pull-right'>Plugin</span>

Parses events from a CSV file.

Parses records from a CSV file. We expect the first row of the CSV
file to contain column names.  This parser specifically supports
Velociraptor's own CSV dialect and so it is perfect for post
processing already existing CSV files.

The types of each value in each column is deduced based on
Velociraptor's standard encoding scheme. Therefore types are properly
preserved when read from the CSV file.

For example, downloading the results of a hunt in the GUI will produce
a CSV file containing artifact rows collected from all clients.  We
can then use the `parse_csv()` plugin to further filter the CSV file,
or to stack using group by.

### Example

The following stacks the result from a
`Windows.Applications.Chrome.Extensions` artifact:

```sql
SELECT count(items=User) As TotalUsers, Name
FROM parse_csv(filename="All Windows.Applications.Chrome.Extensions.csv")
Order By TotalUsers
Group By Name
```




<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
filename|CSV files to open|list of string (required)
accessor|The accessor to use|string
auto_headers|If unset the first row is headers|bool
separator|Comma separator|string



<div class="vql_item"></div>


## parse_ese
<span class='vql_type pull-right'>Plugin</span>

Opens an ESE file and dump a table.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
file||string (required)
accessor|The accessor to use.|string
table|A table name to dump|string (required)



<div class="vql_item"></div>


## parse_evtx
<span class='vql_type pull-right'>Plugin</span>

Parses events from an EVTX file.

This plugin parses windows events from the Windows Event log files (EVTX).

A windows event typically contains two columns. The `EventData`
contains event specific structured data while the `System` column
contains common data for all events - including the Event ID.

You should probably almost always filter by one or more event ids
(using the `System.EventID.Value` field).

### Example

```sql
SELECT System.TimeCreated.SystemTime as Timestamp,
       System.EventID.Value as EventID,
       EventData.ImagePath as ImagePath,
       EventData.ServiceName as ServiceName,
       EventData.ServiceType as Type,
       System.Security.UserID as UserSID,
       EventData as _EventData,
       System as _System
FROM watch_evtx(filename=systemLogFile) WHERE EventID = 7045
```




<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
filename|A list of event log files to parse.|list of string (required)
accessor|The accessor to use.|string
messagedb|A Message database from https://github.com/Velocidex/evtx-data.|string



<div class="vql_item"></div>


## parse_float
<span class='vql_type pull-right'>Function</span>

Convert a string to a float.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
string|A string to convert to int|Any (required)



<div class="vql_item"></div>


## parse_json
<span class='vql_type pull-right'>Function</span>

Parse a JSON string into an object.

Note that when VQL dereferences fields in a dict it returns a Null for
those fields that do not exist. Thus there is no error in actually
accessing missing fields, the column will just return nil.




<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
data|Json encoded string.|string (required)



<div class="vql_item"></div>


## parse_json_array
<span class='vql_type pull-right'>Function</span>

Parse a JSON string into an array.

This function is similar to `parse_json()` but works for a JSON list
instead of an object.




<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
data|Json encoded string.|string (required)



<div class="vql_item"></div>


## parse_json_array
<span class='vql_type pull-right'>Plugin</span>

Parses events from a line oriented json file.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
data|Json encoded string.|string (required)



<div class="vql_item"></div>


## parse_jsonl
<span class='vql_type pull-right'>Plugin</span>

Parses a line oriented json file.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
filename|JSON file to open|string (required)
accessor|The accessor to use|string



<div class="vql_item"></div>


## parse_lines
<span class='vql_type pull-right'>Plugin</span>

Parse a file separated into lines.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
filename|A list of log files to parse.|list of string (required)
accessor|The accessor to use.|string
buffer_size|Maximum size of line buffer.|int



<div class="vql_item"></div>


## parse_mft
<span class='vql_type pull-right'>Plugin</span>

Scan the $MFT from an NTFS volume.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
filename|A list of event log files to parse.|string (required)
accessor|The accessor to use.|string



<div class="vql_item"></div>


## parse_ntfs
<span class='vql_type pull-right'>Function</span>

Parse an NTFS image file.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
device|The device file to open. This may be a full path - we will figure out the device automatically.|string (required)
inode|The MFT entry to parse in inode notation (5-144-1).|string
mft|The MFT entry to parse.|int64
mft_offset|The offset to the MFT entry to parse.|int64



<div class="vql_item"></div>


## parse_ntfs_i30
<span class='vql_type pull-right'>Plugin</span>

Scan the $I30 stream from an NTFS MFT entry.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
device|The device file to open. This may be a full path - we will figure out the device automatically.|string (required)
inode|The MFT entry to parse in inode notation (5-144-1).|string
mft|The MFT entry to parse.|int64
mft_offset|The offset to the MFT entry to parse.|int64



<div class="vql_item"></div>


## parse_ntfs_ranges
<span class='vql_type pull-right'>Plugin</span>

Show the run ranges for an NTFS stream.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
device|The device file to open. This may be a full path - we will figure out the device automatically.|string (required)
inode|The MFT entry to parse in inode notation (5-144-1).|string
mft|The MFT entry to parse.|int64
mft_offset|The offset to the MFT entry to parse.|int64



<div class="vql_item"></div>


## parse_pe
<span class='vql_type pull-right'>Function</span>

Parse a PE file.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
file|The PE file to open.|string (required)
accessor|The accessor to use.|string



<div class="vql_item"></div>


## parse_records_with_regex
<span class='vql_type pull-right'>Plugin</span>

Parses a file with a set of regexp and yields matches as records.  The
file is read into a large buffer. Then each regular expression is
applied to the buffer, and all matches are emitted as rows.

The regular expressions are specified in the [Go
syntax](https://golang.org/pkg/regexp/syntax/). They are expected to
contain capture variables to name the matches extracted.

For example, consider a HTML file with simple links. The regular
expression might be:

```
regex='<a.+?href="(?P<Link>[^"]+?)"'
```

To produce rows with a column Link.

The aim of this plugin is to split the file into records which can be
further parsed. For example, if the file consists of multiple records,
this plugin can be used to extract each record, while
parse_string_with_regex() can be used to further split each record
into elements. This works better than trying to write a more complex
regex which tries to capture a lot of details in one pass.


### Example

Here is an example of parsing the /var/lib/dpkg/status files. These
files consist of records separated by empty lines:

```
Package: ubuntu-advantage-tools
Status: install ok installed
Priority: important
Section: misc
Installed-Size: 74
Maintainer: Ubuntu Developers <ubuntu-devel-discuss@lists.ubuntu.com>
Architecture: all
Version: 17
Conffiles:
 /etc/cron.daily/ubuntu-advantage-tools 36de53e7c2d968f951b11c64be101b91
 /etc/update-motd.d/80-esm 6ffbbf00021b4ea4255cff378c99c898
 /etc/update-motd.d/80-livepatch 1a3172ffaa815d12b58648f117ffb67e
Description: management tools for Ubuntu Advantage
 Ubuntu Advantage is the professional package of tooling, technology
 and expertise from Canonical, helping organisations around the world
 manage their Ubuntu deployments.
 .
 Subscribers to Ubuntu Advantage will find helpful tools for accessing
 services in this package.
Homepage: https://buy.ubuntu.com
```

The following query extracts the fields in two passes. The first pass
uses parse_records_with_regex() to extract records in blocks, while
using parse_string_with_regex() to further break the block into
fields.

```sql
SELECT parse_string_with_regex(
   string=Record,
   regex=['Package:\\s(?P<Package>.+)',
     'Installed-Size:\\s(?P<InstalledSize>.+)',
     'Version:\\s(?P<Version>.+)',
     'Source:\\s(?P<Source>.+)',
     'Architecture:\\s(?P<Architecture>.+)']) as Record
   FROM parse_records_with_regex(
     file=linuxDpkgStatus,
     regex='(?sm)^(?P<Record>Package:.+?)\\n\\n')
```




<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
file|A list of files to parse.|list of string (required)
regex|A list of regex to apply to the file data.|list of string (required)
accessor|The accessor to use.|string



<div class="vql_item"></div>


## parse_recyclebin
<span class='vql_type pull-right'>Plugin</span>

Parses a $I file found in the $Recycle.Bin



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
filename|Files to be parsed.|list of string (required)
accessor|The accessor to use.|string



<div class="vql_item"></div>


## parse_string_with_regex
<span class='vql_type pull-right'>Function</span>

Parse a string with a set of regex and extract fields. Returns a dict with fields populated from all regex capture variables.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
string|A string to parse.|string (required)
regex|The regex to apply.|list of string (required)



<div class="vql_item"></div>


## parse_usn
<span class='vql_type pull-right'>Plugin</span>

Parse the USN journal from a device.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
device|The device file to open.|string (required)
start_offset|The starting offset of the first USN record to parse.|int64



<div class="vql_item"></div>


## parse_xml
<span class='vql_type pull-right'>Function</span>

Parse an XML document into a dict like object.




<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
file|XML file to open.|string (required)
accessor|The accessor to use|string



<div class="vql_item"></div>


## plist
<span class='vql_type pull-right'>Function</span>

Parse plist file



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
file|A list of files to parse.|string (required)
accessor|The accessor to use.|string



<div class="vql_item"></div>


## plist
<span class='vql_type pull-right'>Plugin</span>

Parses a plist file.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
file|A list of files to parse.|list of string (required)
accessor|The accessor to use.|string



<div class="vql_item"></div>


## prefetch
<span class='vql_type pull-right'>Plugin</span>

Parses a prefetch file.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
filename|A list of event log files to parse.|list of string (required)
accessor|The accessor to use.|string



<div class="vql_item"></div>


## regex_replace
<span class='vql_type pull-right'>Function</span>

Search and replace a string with a regexp. Note you can use $1 to replace the capture string.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
source|The source string to replace.|string (required)
replace|The substitute string.|string (required)
re|A regex to apply|string (required)



<div class="vql_item"></div>


## rot13
<span class='vql_type pull-right'>Function</span>

Apply rot13 deobfuscation to the string.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
string||string



<div class="vql_item"></div>


## split_records
<span class='vql_type pull-right'>Plugin</span>

Parses files by splitting lines into records.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
filenames|Files to parse.|list of string (required)
accessor|The accessor to use|string
regex|The split regular expression (e.g. a comma)|string (required)
columns|If the first row is not the headers, this arg must provide a list of column names for each value.|list of string
first_row_is_headers|A bool indicating if we should get column names from the first row.|bool
count|Only split into this many columns if possible.|int



<div class="vql_item"></div>


## sqlite
<span class='vql_type pull-right'>Plugin</span>

Opens an SQLite file and run a query against it.



<div class="vqlargs"></div>

Arg | Description | Type
----|-------------|-----
file||string (required)
accessor|The accessor to use.|string
query||string (required)
args||Any

