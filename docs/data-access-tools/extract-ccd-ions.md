# Extract ions from CCD
The [Chemical Component Dictionary (CCD)](https://www.wwpdb.org/data/ccd) is as an external reference file describing all residue and small molecule 
components found in PDB entries.

This script extracts all ions from the CCD and provides their names as TypeScript set.

## Example
    node --max-old-space-size=4096 lib/commonjs/cli/chem-comp-dict/create-ions.js src/mol-model/structure/model/types/ions.ts

## Usage
| Argument | Description |
| --- | --- |
| `out` | Generated file output path |
| `--forceDownload`, `-f` | Force download of CCD |

    create-ions.js [-h] [--forceDownload] out