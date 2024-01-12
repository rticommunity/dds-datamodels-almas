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
RTI Connext 6.1.2 has been used. A convenient CMake script has been used to
generate code and build a library with all the types included in this datamodel.

In order to generate such library:
```
mkdir build
cd build
cmake ..
cmake --build .
```

This CMake script downloads the
[dds-datamodels-utils](https://github.com/rticommunity/dds-datamodels-utils)
repository that contains dependencies of this repo. You can also provide a local
copy of that repository by setting the cmake variable
`DDS_DATAMODELS_UTILS_DIR` to point to it:

```
cmake .. -DDDS_DATAMODELS_UTILS_DIR=../../dds-datamodels-utils
```

**NOTE**: if you are using a relative path to define `DDS_DATAMODELS_UTILS_DIR`,
it should be relative from the `CMAKE_CURRENT_BINARY_DIR`
