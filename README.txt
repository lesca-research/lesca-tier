
# README for "Study title"

Note: this file is in markdown format (https://www.markdownguide.org/getting-started/). The open-source software "abricotine" is recommended to view and edit. You can get it there: https://github.com/brrd/Abricotine/releases.

> **Template doc**
> This a template where a block of guidelines start with "> **Template doc**".
> These guidelines indicate how to adapt the template to a given project. They should be removed when they are no longer needed.

## File organization (lesca_TIER_v2.2)

The file organisation is based on TIER version 3.0, see [the TIER protocol documentation](https://www.projecttier.org/tier-protocol/).

### Overview of main folders

* `command_files` (versionned): all scripts and code (versionned).
* `data_origin` (versionned): source data , not to be touched.
* `data_analysis` (not versionned): processed data and result figures, can be deleted and generated again from `data_origin` using only code in `command_files`.
* `doc` (versionned): reporting and human-written documents.

The label "(versionned)" refer to (git)[https://git-scm.com/] versioning that is way to store and document the history of all modifications. 
> **Template doc**
> Using git is optional
 
### Detailed description 

> **Template doc**
> Adapt the following with brief content descriptions. 
> Detailled decriptions will go in other parts of this README.

```
|- command_files                             # All code
                                             -
     * data_nst_origin_to_analysis.m        # Convert NIRS data from 
                                             # "data_origin" to 
                                             # "data_analysis", creating ready-
                                             # to-analyse data.
                                             -
     * get_nst_options.m                     # Define some common options
                                             # for importation and analysis
                                             # scripts
                                             -
|- data_origin                               # Raw data, untouched.
                                             # TODO: add project-specific data file
	                                         #       organization
                                             -
     |- metadata                             # Data description.
         * metadata_guide.txt                # Document file naming and format rules.
         * nirs_manifest.tsv                 # Keeps track of nirs data entry.
         * nirs_manifest.tsvs                # Data rules for nirs manifest file 
                                             -
|- data_analysis                             # Processed data, with results.
                                             # Everything is generated from 
                                             # "data_origin" using only the code
                                             # in command_files.
                                             # However, there is some manual
                                             # intervention. See section "manual 
                                             # marking"
                                             -
|- docs                                      # All human-written docs.
                                             -
* README.txt                                 # The current README file.
                                             # Contains all info to understand
                                             # how to go from raw data to the
                                             # final results.
                                             -
* .gitignore                                 # Ignore rules for file 
                                             # synchronisation.
                                             # Note that "data_analysis" is not
                                             # versioned.
```

## Configuration

The indicated software versions are those which are known to work. Other versions might work, but not guarenteed.

Operating system: Linux, Windows, MacOS

[CSV validator](http://digital-preservation.github.io/csv-validator/), version [1.2-RC2](https://search.maven.org/search?q=g:uk.gov.nationalarchives%20AND%20v:1.2-RC2). The package csv-validator-cmd is mandatory. The package csv-validator-ui is optional, to manually check CSV files.
Note that the command "csv-validator-cmd/bin/validate" is expected to be accessible via the command "csv-validate" in the PATH.

> **Template doc**
> Adapt version with project-specific ones.

Matlab environment for NIRS processing using nirstorm:
- version 2017a
- Brainstorm v3.191001
- Nistorm v0.5
- Required matlab toolboxes: Signal processing

## Experiment

> **Template doc**
> Describe the experiments involved in the projects.
> Focus only on details required to understand the content of the raw data and the analysis. For instance:
>   - description of tasks
>   - description of stimulations: 
>        * duration, number and order of sessions / blocks / trials
>        * expected participant's answers
>        * how stimulation timing was recorded?
>   - measurement devices 
>   - how many time points?

## Origin data files

> **Template doc**
> Add documentation about data files and their metadata.
> Add a manifest: a documented list of all data entries (see `data_origin\metadata\nirs_manifest.tsv` for example)
> Fill-in and refer to `data_origin\metadata\metadata_guides.txt` for the description of variables and how the manifest should be filled.

> **Template doc**
> NIRS-specfic details for the manifest:

See `data_origin\metadata\nirs_manifest.tsv` for a complete list of acquisitions with their dates, and usability (see `data_origin\metadata\nirs_manifest.tsvs` for a description of all fields). This manifest is the reference file on which the importation step will rely to know wether a given NIRS acquisition can be imported. 
It is the central place to store any relevant information about the content of the data.
The main field of interest is "usability" which can take the following:
- "ok": the data can be imported and analysed. Only data marked this way will be taken actually imported. All other usability value will lead to the data being not processed.
- "missing": the acquisition was planned but no data is available.
- "truncated": the data is available but only partially. There might be an opportunity to fix that later by using missing-data analysis.
- "to_check": there is a specific problem that could be fixed. This should be further described in the comment field.
 
Folder `data_origin` contains all raw data, coming direclty from the acquisition software (under labview). Only filenames have been changed from the original files.

> **Template doc**
> Indicate cases where raw data were not available and replaced by manually processed data:
 
There is an exception for ..., where raw data were not available for these acquisitions.

> **Template doc**
> Describe how file names are formatted in `data_origin`

In `data_origin`, each subfolder is an acquisition. The name of the subfolder has the following format: ...

In each subfolder, data files are:
...

## Data processing

> **Template doc**
> This section fully describes how to go from raw data to the final paper figures:
>    - how the folder `data_analysis` is created from `origin_data`
>    - how to use scripts
>    - document output files
>    - description of any specific manual intervention (must be minimal)

### Convert origin to analysis data

> **Template doc**
> Describe how to convert raw data into data that can be analyzed. 
> Especially, indicate what script to use in `command_files`.

> **Template doc**
> The following is an example for NIRS data

This step converts data from `data_origin` into data that are ready for the analysis (target folder is `data_analysis`). All operations are done by the matlab script  `command_files/data_nst_origin_to_analysis.m`.
They consists of the following steps:
- filter data based on the the manifest `data_origin\metadata\nirs_manifest.tsv`, where usuability is "ok".
- convert from labview (tdms) format to Homer .nirs
- copy CW6 .nirs files (already in Homer .nirs format)
- dump optode coordinates
- import nirs and optode coordinates in the brainstorm database under the protocol "PROTOCOL_NAME".
- extract stimulation events (eg from AUX channels)
- if available, load manual markings from `data_origin/manual_markings` (see next section)

The nirstorm function used to import data in brainstorm is `nst_ppl_surface_template_V1('import',...)`. It has its own convention to label the imported data. **Do not change the names or organization of the imported data**. See [this wiki page for more details](https://github.com/Nirstorm/nirstorm/wiki/%5BWIP%5D-GLM-pipeline:-surface-and-template-based#3-importation).

### Manual marking under brainstorm

> **Template doc**
> This subsection is specific NIRS data processing with nirstorm

Motion correction events are tagged manually using brainstorm. If necessary, channels with bad signals are tagged as bad channels (too noisy, too many artefacts, flat lines...).
After this manual marking step, run the script `command_files/save_brainstorm_markings.m`
It will export motion events and bad channel tags into `data_origin\export_markings`. These markings are saved there and not in `data_analysis` because they are source data needed to replicate the study. These files are versionned using git.

### GLM pipeline

> **Template doc**
> This subsection is specific NIRS data processing with nirstorm

The script `command_files/run_nst_GLM.m` runs the whole analysis pipeline comprising:
- preprocessing
- cortical projection
- within-subject GLM
- group-level GLM

It relies on the nirstorm function `nst_ppl_surface_template_V1('analysis',...)`. See [this wiki page for more details](https://github.com/Nirstorm/nirstorm/wiki/%5BWIP%5D-GLM-pipeline:-surface-and-template-based#5-preprocessings-and-glm).

Result figures are saved in `data_analysis\evoked_task_figs`:
- t-maps for HbO, HbR, HbT, for all contrasts and subjects
- group-level t-maps for HbO, HbR, HbT, for all contrasts
