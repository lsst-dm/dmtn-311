# Using Tape RSE at the USDF

```{abstract}
The USDF Tape RSE is designed to backup Rucio Datasets
This document will briefly summarize the Tape RSE setting 
and operations at the USDF, and will focus on how to use 
it to trigger archiving of data in Rubin Rucio.
```

## Architecture of Tape RSE at the USDF

The USDF Tape RSE is different from the traditional RSE 
(Rucio Storage Element). It doesn't implement direct data
transfer in and out of the RSE. Instead, it is designed to
archive datasets in Rucio that other RSEs hold a copy.

...

### Prerequisits

It is possible to have the Tape RSE to archive data files in 
both shared file system and remote RSE or non-posix storage
(e.g. CEPH). The current configuration only work with data 
files that it can read through a shared file system. 

## Set up and Configure a Tape RSE

As it is designed, a regular RSE and a Tape RSE needs to
work as a pair - to arhcive datasets in a regular RSE such
as SLAC_RAW_DISK, a Tape RSE (for example, SLAC_RAW_TAPE)
needs to be setup. 

In the Tape RSE configuration file, the name of the pairing
regular RSE (SLAC_RAW_DISK) and the Rucio scopes that it
will work on needs to be specified. To work with new scopes,
those scopes need to be added to the configuration file and
the Tape RSE instacne needs to be restarted.

A Tape RSE only work with one shared file system.

## How to Archive Data to the Tape RSE

Using SLAC_RAW_DISK as an example, To trigger a dataset 
arhciving, do the following:

1. Close the dataset in Rucio (*rucio close <did>*)
2. Add a Rucio metadata **arcBackup=SLAC_RAW_TAPE:need**. 
   (*rucio set-metadata --did <did> --key arcBackup --value SLAC_RAW_TAPE:need*)

The arhiving process is not immediately. But once it is 
completed, the metadata will be updated to **arcBackup=SLAC_RAW_TAPE:done**.
(To check, run *rucio get-metadata --plugin json <did> | grep arcBackup*),
The corresponding archive zip file(s) can be found on
the HPSSFS mount point at
*sdfdtn005://local-Rubin-RSE-Backups/lsst/rucio/<scope>/<Dataset name>*.

If a dataset is later openned again and new files are added,
A new archiving will be needed. To do that, delete the copy
on tape, and redo the above procedure.

## Retrieve from Archive

Retrieving a dataset can be as simple as copying the zip 
archive files from the above HPSSFS mount point.

Retrieving a single file from a dataset ...

