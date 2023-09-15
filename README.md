# dds-datamodels-almas

This datamodel is an enhanced version of the OMG® ALMAS 1.3 beta 1 datamodel
that you can find here: https://www.omg.org/spec/ALMAS/1.3/Beta1/About-ALMAS

## Repo Organization

### Versioning & Branches

This repository stores the different versions of the ALMAS datamodel in
different branches. Additionally, it contains `enhanced` versions of the
original datamodel. This enhanced versions modifies the original datamodel
including the latest IDL features and other potential improvements. The
different changes are explained in their own readme file.

The branches in this repo follow this pattern:

 - main: this contains the latest enhanced version
 - version/x.y\[-(version_specifier\[-enhanced\]\]

For example, `version/2.0-beta-enhanced`

The `version_specifier` is added if a non-final version is being used. This
information appears in the datamodel website.

The `-enhanced` indicates that it contains the enhanced version of the specified
datamodel version.

### Folders

This repository contains one folder called `datamodel` that contains the
representation of this datamodel. Internally, that folder contains the different
files that implement the datamodel. It must contain an `idl` folder that
includes the IDL files of the datamodel. Additionally, other folders with the
name of the technology used for the representation of the datamodel may be
present. For example: `xml`, `json`...

## Changes on the Datamodel

This enhanced version contains several changes in the datamodel for the
ALMAS version 1.3 beta 1:

 - Replaced the key definition with the IDL 4.2 `@key` annotation.

## Testing

In order to test this datamodel after the applied changes, `rtiddsgen` from
RTI Connext 6.1.2 has been used. A convenient script has been used to run this
automatically. You can find the script here: https://github.com/angelrti/run-rtiddsgen

In order to run the script in all files the following command was called:
```
run-rtiddsgen -v -D datamodel/idl -o ./delete_generated_files --additional-options="-verbosity 1" --dds-types --skip-files "*DLRL*"
```
