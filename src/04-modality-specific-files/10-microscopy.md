# Microscopy

Support for Microscopy was developed as a
[BIDS Extension Proposal](../07-extensions.md#bids-extension-proposals).

<!--- Time to write a paper and add link to a paper in `src/01-introduction.md#citing-bids`-->
Please see [Citing BIDS](../01-introduction.md#citing-bids)
on how to appropriately credit this extension when referring to it in the
context of the academic literature.

<!--- TODO: complete examples on bids_examples and add their name in this description-->
Two [example Microscopy datasets](https://github.com/bids-standard/bids-examples)
(microscopy_XX and microscopy_YY) have been formatted using this specification and can be
used for practical guidance when curating a new dataset.

<!--- TODO: update SEM dataset and add the link.
It would be great to have an OMETIFF real example dataset as well. -->
Further Microscopy datasets are available from [somewhere](<link_to_somewhere>).

## Microscopy recording data
<!---
The following table will be generated automatically from the `src/schema/datatypes/microscopy.yaml`
file after community review.  A "manual" table is provided here to facilitate the review process.
-->
Template:
```
sub-<label>/
    [ses-<label>/]
        microscopy/
            sub-<label>[_ses-<label>]_sample-<label>[_chunk-<index>][_acq-<label>][_stain-<label>][_run-<index>]_<suffix>.<extension>
            sub-<label>[_ses-<label>]_sample-<label>[_chunk-<index>][_acq-<label>][_stain-<label>][_run-<index>]_<suffix>.json
```
Microscopy data MUST be stored in the `microscopy` directory.

### File formats
The Microscopy community uses a variety of formats for storing raw data, and there is no single
standard that all researchers agree on. However, a standardized file structure has been developed
by the [Open Microscopy Environment](https://www.openmicroscopy.org/) for whole-slide imaging with
the [OME-TIFF file specifications](https://docs.openmicroscopy.org/ome-model/6.1.2/ome-tiff/file-structure.html).
The OME-TIFF file allows for multi-pages TIFF file to store multiple image planes and supports
multi-resolution pyramidal tiled images. An OME-XML data block is also embedded inside the
file’s header.

In Microscopy-BIDS, we accept different file formats to accommodate datasets stored in 2D image
formats and whole-slide imaging formats, to accommodate lossless and lossy compression,
and to avoid unnecessary conversions of the original data from a non-tiled to a tiled format,
or vice-versa.

For BIDS, Microscopy raw data MUST be stored in one of the following formats:

- [Portable Network Graphics](http://www.libpng.org/pub/png/) (`.png`)
- [Tag Image File Format](https://www.adobe.io/open/standards/TIFF.html) (`.tif`)
- [OME-TIFF](https://docs.openmicroscopy.org/ome-model/6.1.2/ome-tiff/specification.html#)
(`.ome.tif` for standard TIFF files or `.ome.btf` for
[BigTIFF](https://www.awaresystems.be/imaging/tiff/bigtiff.html) files)

If different from PNG, TIFF or OME-TIFF, the original unprocessed data in the native format MAY be
stored in the [`/sourcedata` directory](../02-common-principles.md#source-vs-raw-vs-derived-data).

Future versions of Microscopy-BIDS may extend this list of supported file formats.
For example with the [OME-NGFF](https://ngff.openmicroscopy.org/latest/) format currently
developed by OME as a successor to OME-TIFF for better remote sharing of large datasets.

### Modality suffixes
Microscopy data currently support the following imaging modalities:
<!---
The following table will be generated automatically from the `src/schema/suffixes` yaml files
after community review. A "manual" table is provided here to facilitate the review process.
-->
| **Name**                                         | `suffix` |
| ------------------------------------------------ | -------- |
| Transmission electron microscopy                 | TEM      |
| Scanning electron microscopy                     | SEM      |
| Micro-CT                                         | CT       |
| Bright-field microscopy                          | BF       |
| Dark-field microscopy                            | DF       |
| Phase-contrast microscopy                        | PC       |
| Differential interference contrast microscopy    | DIC      |
| Fluorescence                                     | FLUO     |
| Confocal microscopy                              | CONF     |
| Polarized-light microscopy                       | PLI      |
| Coherent anti-Stokes Raman spectroscopy          | CARS     |
| 2-photon excitation microscopy                   | 2PE      |
| Multi-photon excitation microscopy               | MPE      |
| Super-resolution microscopy                      | SR       |
| Nonlinear optical microscopy                     | NLO      |
| Optical coherence tomography                     | OCT      |
| Selective plane illumination microscopy          | SPIM     |

### Filename entities

In the context of Microscopy, a session ([`ses-<label>`](../99-appendices/09-entities.md#ses))
can refer to all the acquisitions between the start and the end of an imaging experiment
for ex vivo imaging or a subject lab visit for biopsy procedure and/or in vivo imaging.

The [`sample-<label>`](../99-appendices/09-entities.md#sample) entity is REQUIRED for
Microscopy data and is used to distinguish between different samples from the same subject.
The label MUST be unique per subject and is RECOMMENDED to be unique throughout the dataset.

<!--- Generate following example with MACRO make_filetree_example after community review -->
For example:
Three brain slices (`sample-01` to `sample-03`) extracted from subject `sub-01`.
```Text
sub-01/
    microscopy/
        sub-01_sample-01_<modality_suffix>.<ext>
        sub-01_sample-02_<modality_suffix>.<ext>
        sub-01_sample-03_<modality_suffix>.<ext>
        sub-01_<modality_suffix>.json
```
In this example, JSON metadata is common for all samples of `sub-01`.
JSON metadata may be defined per subject or per sample as appropriate, per the
[inheritance principle](../02-common-principles.md#the-inheritance-principle).

The [`chunk-<index>`](../99-appendices/09-entities.md#chunk) entity is used when multiples
regions (2D images or 3D volumes files) of the same physical sample are imaged with different
fields of view, regardless if they overlap or not.

<!--- Generate following example with MACRO make_filetree_example after community review -->
For example:
Four chunks (`chunk-01` to `chunk-04`) from the same brain sample (`sample-01`) of subject `sub-01`.
```Text
sub-01/
    microscopy/
        sub-01_sample-01_chunk-01_<modality_suffix>.<ext>
        sub-01_sample-01_chunk-01_<modality_suffix>.json
        sub-01_sample-01_chunk-02_<modality_suffix>.<ext>
        sub-01_sample-01_chunk-02_<modality_suffix>.json
        sub-01_sample-01_chunk-03_<modality_suffix>.<ext>
        sub-01_sample-01_chunk-03_<modality_suffix>.json
        sub-01_sample-01_chunk-04_<modality_suffix>.<ext>
        sub-01_sample-01_chunk-04_<modality_suffix>.json
```
In this example, JSON metadata is different for each chunk of `sub-01_sample-01`.
JSON metadata may be defined per sample or per chunk as appropriate, per the
[inheritance principle](../02-common-principles.md#the-inheritance-principle).

In some cases, the chunks can be "ordered" and correspond to the displacement of the
microscope stage for example.
In others cases, the chunks can be different images of the same sample with no explicit
spatial relation between them.

Examples of different chunks configurations can be seen in Figure 1.
<!--- This figure could be prettier, maybe we should also remove rotation and shear
from example c), it adds nothing but confusion -->
![Figure 1](images/microscopy_chunks.png "Examples of Microscopy chunks")

Figure 1: a) Ordered 2D chunks without overlap, b) ordered 2D chunks with overlap,
c) unordered 2D chunks with and without overlap, d) ordered 2D chunks on different 3D planes,
e) ordered 3D chunks

The [`acq-<label>`](../99-appendices/09-entities.md#acq) entity corresponds to a custom label that
MAY be used to distinguish a different set of parameters used for acquiring the same modality.
For example, two images of the same sample acquired with different magnification of 40x and 60x.
In such case two files could have the following names:
`sub-01_sample-01_acq-40x_<modality_suffix>.<ext>` and
`sub-01_sample-01_acq-60x_<modality_suffix>.<ext>`, however the user is free to choose any other
label as long as they are consistent across subjects and sessions.

The [`stain-<label>`](../99-appendices/09-entities.md#stain) entity can be used to distinguish
image files from the same sample using different stains or antibodies for contrast enhancement.

<!--- Generate following example with MACRO make_filetree_example after community review -->
For example:
Brain slice (`sample-01`) extracted from subject `sub-01`, imaged with three stains
(`stain-01`, `stain-02`, `stain-03`) in three separate files.
In this example, the entity stain is used to distinguish images with different
stains in separate files from the same sample.
```Text
sub-01/
    microscopy/
        sub-01_sample-01_stain-01_<modality_suffix>.<ext>
        sub-01_sample-01_stain-01_<modality_suffix>.json
        sub-01_sample-01_stain-02_<modality_suffix>.<ext>
        sub-01_sample-01_stain-02_<modality_suffix>.json
        sub-01_sample-01_stain-03_<modality_suffix>.<ext>
        sub-01_sample-01_stain-03_<modality_suffix>.json
```
In this example, the entity stain is used to distinguish images with different
stains in separate files from the same sample.
In the case where a single file contains different staining in each channel, the
`stain-<label>` is omitted.

Stains or antibodies SHOULD be indicated as appropriate in the `"SampleStaining"`,
`"SamplePrimaryAntibodies"` and/or `"SampleSecondaryAntobodies"` keys in the sidecar JSON file,
although the label may be different.

If more than one run of the same sample, chunk, acquisition and stain are acquired during
the same session, the [`run-<index>`](../99-appendices/09-entities.md#run) entity MUST be used:
`_run-1`, `_run-2`, `_run-3`, and so on.
If only one run was acquired the `run-<index>` can be omitted.

In microscopy, many pyramidal file formats store multiple resolutions for the same acquisition.
In the case where a multiple resolutions file format is converted to single resolution file format,
only the higher resolution file is present in the raw data.
Lower resolutions files MUST be placed under the `derivatives` folder and use the
[`res-<label>`](../99-appendices/09-entities.md#res) entity.
See [Preprocessed, coregistered and/or resampled volumes](../05-derivatives/03-imaging.md#preprocessed-coregistered-andor-resampled-volumes)

### Microscopy metadata (Sidecar JSON)

Microscopy data MUST be described by metadata fields, stored in sidecar JSON files.

#### Device Hardware
<!---
The following table will be generated automatically from the `src/schema/metadata` yaml files
after community review. A "manual" table is provided here to facilitate the review process.

Note: For "StationName", we would like to remove "Corresponds to DICOM Tag 0008, 1010 `Station Name`.".
It would require changes to `src/schema/metatata/StationName.yaml` and `src/04-modality-specific-files/01-magnetic-resonance-imaging-data.md`.
Follow-up with the maintainers to do.
-->
| **Key name**              | **Requirement level** | **Data type** | **Description**                                                                                                                                                                        |
| ------------------------- | --------------------- | ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Manufacturer              | RECOMMENDED           | string    | Manufacturer of the equipment that produced the measurements. |
| ManufacturersModelName    | RECOMMENDED           | string    | Manufacturer’s model name of the equipment that produced the measurements. |
| DeviceSerialNumber        | RECOMMENDED           | string    | The serial number of the equipment that produced the measurements. A pseudonym can also be used to prevent the equipment from being identifiable, so long as each pseudonym is unique within the dataset. |
| StationName               | RECOMMENDED           | string    | Institution defined name of the machine that produced the measurements. Corresponds to DICOM Tag 0008, 1010 `Station Name`. |
| SoftwareVersions          | RECOMMENDED           | string    | Manufacturer’s designation of software version of the equipment that produced the measurements. |
| InstitutionName           | RECOMMENDED           | string    | The name of the institution in charge of the equipment that produced the measurements. |
| InstitutionAddress        | RECOMMENDED           | string    | The address of the institution in charge of the equipment that produced the measurements. |
| InstitutionDepartmentName | RECOMMENDED           | string    | The department in the institution in charge of the equipment that produced the measurements. |


#### Image Acquisition
<!---
The following table will be generated automatically from the `src/schema/metadata` yaml files
after community review. A "manual" table is provided here to facilitate the review process.
-->
| **Key name**               | **Requirement level** | **Data type** | **Description**                                                                                                                                                                        |
| -------------------------- | --------------------- | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PixelSize                  | REQUIRED              | array of numbers    | A 2- or 3-numbers array of the physical size of a pixel, either`[PixelSizeX, PixelSizeY]` or `[PixelSizeX, PixelSizeY, PixelSizeZ]`, where X is the width, Y the height and Z the depth. If the file format is OME-TIFF, these values need to be consistent with `PhysicalSizeX`, `PhysicalSizeY` and `PhysicalSizeZ` OME metadata fields, after converting in `PixelSizeUnits` according to `PhysicalSizeXunit`, `PhysicalSizeYunit` and `PhysicalSizeZunit` OME fields. |
| PixelSizeUnits             | REQUIRED              | string              | Unit format of the specified PixelSize. MUST be in micrometers (`"um"`). |
| Immersion                  | OPTIONAL              | string              | Lens immersion medium. If the file format is OME-TIFF, the value MUST be consistent with the `"Immersion"` OME metadata field. |
| NumericalAperture          | OPTIONAL              | number              | Lens numerical aperture (for example, `1.4`). If the file format is OME-TIFF, the value MUST be consistent with the `"LensNA"` OME metadata field. |
| Magnification              | OPTIONAL              | number              | Lens magnification (for example, `40`) If the file format is OME-TIFF, the value MUST be consistent with the `"NominalMagnification"` OME metadata field.|
| ImageAcquisitionProtocol   | OPTIONAL              | string or URI       | Description of the image acquisition protocol or URI from [protocols.io](https://www.protocols.io/). |
| OtherAcquisitionParameters | OPTIONAL              | string              | Description of other relevant image acquisition parameters. |

#### Sample
<!---
The following table will be generated automatically from the `src/schema/metadata` yaml files
after community review. A "manual" table is provided here to facilitate the review process.

Note : For BodyPart, we would like to remove: "Corresponds to DICOM Tag 0018, 0015 `Body Part Examined`. For example: `"BRAIN"`.",
and replace with "from [DICOM Body Part Examined](http://dicom.nema.org/medical/dicom/current/output/chtml/part16/chapter_L.html#chapter_L) (for example: `"BRAIN"`)".
It would require changes to `src/schema/metatata/BodyPart.yaml` and `src/04-modality-specific-files/09-positron-emission-tomography.md`.
Follow-up with the maintainers to do.
-->
| **Key name**                | **Requirement level** | **Data type** | **Description**                                                                                                                                                                        |
| --------------------------- | --------------------- | -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BodyPart                    | RECOMMENDED           | string                     | Body part of the organ / body region scanned. Corresponds to DICOM Tag 0018, 0015 `Body Part Examined`. For example: `"BRAIN"`. |
| BodyPartDetails             | OPTIONAL              | string                     | If needed, additional details about body part or location (for example: `"corpus callosum"`). |
| BodyPartDetailsOntology     | OPTIONAL              | string                     | If applicable, information about the ontology used for specifying BodyPartDetails (for example: `"UBERON (https://www.ebi.ac.uk/ols/ontologies/uberon)"`). |
| Environment                 | RECOMMENDED           | string                     | RECOMMENDED. Indicate if the sample is imaged `"invivo"`, `"exvivo"` or `"invitro"`. |
| SampleEmbedding             | OPTIONAL              | string                     | Description of the tissue sample embedding (for example: `"Epoxy resin"`). |
| SampleFixation              | OPTIONAL              | string                     | Description of the tissue sample fixation (for example: `"4% paraformaldehyde, 2% glutaraldehyde"`). |
| SampleStaining              | RECOMMENDED           | string or array of strings | Name(s) of the tissue sample stains and dyes. MAY be an array of strings if different stains are used in each channel of the file (for examples: `"Osmium"` or `["LFB", "PLP"]`). |
| SamplePrimaryAntibody       | RECOMMENDED           | string or array of strings | Description of the primary antibody used for immunostaining. Either an [RRID](https://scicrunch.org/resources) or the name, supplier and catalogue number of a commercial antibody. For non-commercial antibodies either an [RRID](https://scicrunch.org/resources) or the host-animal and immunogen used. MAY be an array of strings if different antibodies are used in each channel of the file (for examples: `"Rabbit anti-Human HTR5A Polyclonal Antibody, Invitrogen, Catalog # PA1-2453"` or `"RRID:AB_2122563"`). |
| SampleSecondaryAntibody     | RECOMMENDED           | string or array of strings | Description of the secondary antibody used for immunostaining. Either an [RRID](https://scicrunch.org/resources) or the name, supplier and catalogue number of a commercial antibody. For non-commercial antibodies either an [RRID](https://scicrunch.org/resources) or the host-animal and immunogen used. MAY be an array of strings if different antibodies are used in each channel of the file (for examples: `"Goat anti-Mouse IgM Secondary Antibody, Invitrogen, Catalog # 31172"` or `"RRID:AB_228322"`). |
| SliceThickness              | OPTIONAL              | number                     | Slice thickness of the tissue sample in the unit micrometers (`"um"`) (for example: `90`). |
| ShrinkageFactor             | OPTIONAL              | number                     | Estimated shrinkage factor of the tissue, given in percent (between 0 and 100%) of the original tissue dimensions (for example: `3`). |
| SampleExtractionProtocol    | OPTIONAL              | string or URI              | Description of the sample extraction protocol or URI from [protocols.io](https://www.protocols.io/). |
| SampleExtractionInstitution | OPTIONAL              | string                     | The name of the institution in charge of the extraction of the sample, if different from the institution in charge of the equipment that produced the image. |

#### Chunk transformations

Chunk transformations metadata describes the spatial relation between chunks
of the same sample in an implicit coordinate system.

- The source frame of reference is the frame of reference of the associated image.
- The target frame of reference is the implicit coordinate system of the transform.
- The target frame of reference has the same units as the `PixelSizeUnits` metadata.
- The chunk transform is described by 2 metadata fields: a transformation matrix and a
description of the axis of the matrix.
- Other transformations should be described in derivatives.
<!---
The following table will be generated automatically from the `src/schema/metadata` yaml files
after community review. A "manual" table is provided here to facilitate the review process.
-->
| **Key name**                  | **Requirement level**                                            | **Data type**              | **Description**                                                                                                                                                                        |
| ----------------------------- | ---------------------------------------------------------------- | -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChunkTransformationMatrix     | OPTIONAL                                                         | array of arrays of numbers | 3x3 or 4x4 matrix describing spatial chunk transformation, for 2D and 3D respectively (for examples: `[[2, 0, 0], [0, 3, 0], [0, 0, 1]]` in 2D for 2x and 3x scaling along the first and second axis respectively or `[[1, 0, 0, 0], [0, 2, 0, 0], [0, 0, 3, 0], [0, 0, 0, 1]]` in 3D for 2x and 3x scaling along the second and third axis respectively). Note that non-spatial dimensions like time and channel are not included in the transformation matrix. |
| ChunkTransformationMatrixAxis | OPTIONAL, but REQUIRED if `ChunkTransformationMatrix` is present | arrays of strings          | Describe the axis of the ChunkTransformationMatrix (for examples: `['X', 'Y']` or `['Z', 'Y', 'X']`) |


#### Example (`*_<suffix>.json`)
<!--- We have to make sure that those values are "plausible" together -->
```JSON
{
        "Manufacturer": "Hamamatsu",
        "ManufacturersModelName": "C9600-12",
        "PixelSize": [0.23, 0.23],
        "PixelSizeUnits": "um",
        "Magnification": 40,
        "BodyPart": "BRAIN",
        "BodyPartDetails": "corpus callosum",
        "Environment": "exvivo",
        "SampleFixation": "4% paraformaldehyde, 2% glutaraldehyde",
        "SampleStaining": "LFB",
        "SliceThickness": 90,
        "ShrinkageFactor": 3
}
```

## Required Samples file (`samples.tsv`)

For Microscopy data, the [Samples file](../03-modality-agnostic-files.md#samples-file) is REQUIRED.

Additional optional columns in `samples.tsv` MAY be used to describe other samples' attributes.

## Recommended participant data (`participants.tsv`)
<!---This section may be removed or modified depending if it is included in the "general"
participants.tsv file or not.
See PR #816 (https://github.com/bids-standard/bids-specification/pull/816#issuecomment-909343123)
-->
In addition to the species column in the [Participants file](../03-modality-agnostic-files.md#participants-file),
the following data SHOULD be included for species different from `homo sapiens`:

-   `strain`: string value indicating the strain of the species, for example: `C57BL/6J`.
-   `strain_rrid`: research resource identifier ([RRID](https://scicrunch.org/resources/Organisms/search))
    of the strain of the species, for example: `RRID:IMSR_JAX:000664`.

For example:
```Text
participant_id species strain strain_rrid
sub-01 mus musculus C57BL/6J RRID:IMSR_JAX:000664
sub-02 mus musculus C57BL/6J RRID:IMSR_JAX:000664
```

## Photos of the samples (`*_photo.jpg`)
Photos of the tissue sample, overview microscopy scans or blockface images from cutting
MAY be included for visualization of large samples or to indicate the location of chunks
in a sample.
<!--- Generate following example with MACRO make_filetree_example after community review -->
```Text
sub-<label>/
    [ses-<label>/]
        microscopy/
            sub-<label>[_ses-<label>]_sample-<label>[_acq-<label>]_photo.jpg
```
The [`acq-<label>`](../99-appendices/09-entities.md#acq) entity can be used to indicate
acquisition of different photos of the same sample.
<!--- Generate following example with MACRO make_filetree_example after community review -->
For example:
```Text
sub-01/
    microscopy/
        sub-01_sample-01_acq-1_photo.jpg
        sub-01_sample-01_acq-2_photo.jpg
```
<!--- It would be great to have an example here -->
Example of an overview scan from...
```Text
    <filename>_photo.jpg
```
![image title](images/<link-to-image> "title text")

<!-- Link Definitions -->
