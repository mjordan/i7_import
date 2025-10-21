## Purpose
This repository contains a script to extract metadata from an Islandora Legacy (7.x) repository and 
generate a CSV file. This CSV file can be used as the basis for an [islandora_workbench](https://github.com/mjordan/islandora_workbench)
source file to facilitate migration to Islandora (8.x).

## Requirements
- Python 3.x
- Access to the Islandora Legacy repository's Solr instance and HTTP API.

## Installation 
1. Clone this repository:
   ```bash
   git clone <this repo's URL>
   cd <this repo's directory>
   ```
2. Install the required Python packages:
   ```bash
   pip install -r requirements.txt
   ```

## Configuration
You can find a sample configuration file [here](configs/islandora7_import_sample.config).
This is a YAML file that contains parameters for connecting to the Islandora Legacy repository and
specifying records to select and the metadata to output.

1. Copy the sample configuration file and rename it (e.g., `islandora7_import.config`).
2. Edit the configuration file to set the correct parameters (see [Configuration Parameters](#configuration-parameters) below).

**Note**: The Solr filters and collection/content model restrictions can be used to limit the records retrieved.
But they all are applied with AND logic.

The `pids_to_skip` parameter can be used to exclude specific PIDs from the results.

### Field name assumptions
- `RELS_EXT_isMemberOfCollection_uri_s`: This field is assumed to contain the collection membership information.
- `RELS_EXT_hasModel_uri_s`: This field is assumed to contain the content model information.
- That both of the above fields are indexed with an `"info:fedora/"` prefix.

## Usage
Run the script with the configuration file as an argument:
```bash
python3 get_islandora_7_content.py --config path/to/your_config_file.config
```
This will generate a CSV file as specified in the configuration file at the location specified in the configuration file.

## Configuration Parameters
- `solr_base_url`: (string) The base URL to the Islandora Legacy Solr endpoint (i.e "http://localhost:8080/solr")
- `islandora_base_url`: (string) The base URL to the Islandora Legacy website (i.e "http://localhost:8000")
- `csv_output_path`: (string) The path to generate the CSV file at. (i.e "output.csv")
- `obj_directory`: (string) The directory to export binary files to (if required).
- `failure_report`: (string) The path to generate a failure report file at. (i.e "failures.txt")
- `log_file_path`: (string) The path to generate the log file at (i.e. "islandora_content.log")
- `fetch_files`: (boolean) Whether to fetch files down to the `obj_directory`, (default False).
- `get_file_url`: (boolean) Whether to get the file URL to store in the CSV file, (default True).
- `namespace`: (string) Namespace filter to restrict records, (default "*")
- `standard_fields`: (list<string>) A list of standard SOLR fields to retrieve.
  (default `[
            "PID",
            "RELS_EXT_hasModel_uri_s",
            "RELS_EXT_isMemberOfCollection_uri_ms",
            "RELS_EXT_isMemberOf_uri_ms",
            "RELS_EXT_isConstituentOf_uri_ms",
            "RELS_EXT_isPageOf_uri_ms",
        ]`)
- `field_pattern`: (string) A regular expression pattern to include fields from all available fields, (default "mods_.*(_s|_ms)$")
- `field_pattern_do_not_want`: (string) A regular expression pattern to exclude from all available fields, (default "(marcrelator|isSequenceNumberOf)")
- `id_field`: (string) The Solr field name of the ID, (default "PID")
- `id_start_number`: (int) The number to start numbering rows with (default 1)
- `datastreams`: (list<string>) The datastreams to try to retrieve for each record, stops on first match. (default ["OBJ", "PDF"])
- `debug`: (boolean) Include debug information in the log (default False)
- `deep_debug`: (boolean) Include verbose debug information in the log (default False)
- `collection`: (string) Restrict retrieve records to be a part of this collection.
- `collections`: (list<string>) Restrict retrieve records to be a part of these collections.
- `content_model`: (string) Restrict retrieve records to be of this content model.
- `solr_filters`: (dict) Additional Solr filters to apply (key = field name, value = field value).
- `start`: (int) Record to start Solr rows at (default 0).
- `rows`: (int) Number of rows to return (default 100000).
- `secure_ssl_only`: (boolean) Whether to require a SSL connection (default True).
- `pids`: (list<string>) List of specific PIDs to include.
- `pids_to_skip`: (list<string>) List of PIDs to not retrieve.

Additional documentation is available in the [Islandora Workbench docs](https://mjordan.github.io/islandora_workbench_docs/exporting_islandora_7_content/), but the documentation in this README is updated more frequently.
