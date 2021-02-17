# Volume Server

Provides near-instantaneous access to volumetric data including density maps (for instance, from X-ray crystallography 
or cryo-electron microscopy experiments), spatial distribution data, output from electrostatic calculations. It works by
utilizing adaptive downsampling (similar to how Google Earth works). 

## Example
```sh
node lib/commonjs/servers/volume/server --idMap x-ray '/opt/data/xray/${id}.mdb'
```

## Usage
| Argument= | Description |
| --- | --- |
| `--cfg` | JSON config file path. If a property is not specified, cmd line param/OS variable/default value are used. |
| `--printCfg` | Print current config for validation and exit. |
| `--cfgTemplate` | Prints default JSON config template to be modified and exit. |
| `--apiPrefix` | Specify the prefix of the API, i.e. &lt;host&gt;/&lt;apiPrefix&gt;/&lt;API queries&gt; |
| `--defaultPort` | Specify the port the server is running on |
| `--shutdownTimeoutMinutes` | Server will shut down after this amount of minutes, 0 for off. |
| `--shutdownTimeoutVarianceMinutes` | Modifies the shutdown timer by +/- `timeoutVarianceMinutes` (to avoid multiple instances shutting at the same time) |
| `--idMap` | Map `id`s for a `type` to a file path. Example: `x-ray '../../data/mdb/xray/${id}-ccp4.mdb'` - JS expressions can be used inside `${}`, e.g. `${id.substr(1, 2)}/${id}.mdb` - Can be specified multiple times. - The `TYPE` variable (e.g. `x-ray`) is arbitrary and depends on how you plan to use the server. By default, Mol* Viewer uses `x-ray` and `em`, but any particular use case may vary. |
| `--maxRequestBlockCount` | Maximum number of blocks that could be read in 1 query. This is somewhat tied to the ``maxOutputSizeInVoxelCountByPrecisionLevel`` in that the `&lt;maximum number of voxel&gt; = maxRequestBlockCount * &lt;block size&gt;^3`. The default block size is 96 which corresponds to 28,311,552 voxels with 32 max blocks. |
| `--maxFractionalBoxVolume` | The maximum fractional volume of the query box (to prevent queries that are too big). |
| `--maxOutputSizeInVoxelCountByPrecisionLevel` | What is the (approximate) maximum desired size in voxel count by precision level - Rule of thumb: `&lt;response gzipped size&gt;` in `[&lt;voxel count&gt; / 8, &lt;voxel count&gt; / 4]`. The maximum number of voxels is tied to maxRequestBlockCount. |

```sh
node lib/commonjs/servers/volume/server [-h] [-v]
        [--cfg CFG]
        [--printCfg] 
        [--cfgTemplate]
        [--apiPrefix PREFIX]
        [--defaultPort PORT]
        [--shutdownTimeoutMinutes TIME]
        [--shutdownTimeoutVarianceMinutes VARIANCE] 
        [--idMap TYPE PATH]
        [--maxRequestBlockCount COUNT] 
        [--maxFractionalBoxVolume VOLUME]
        [--maxOutputSizeInVoxelCountByPrecisionLevel LEVEL [LEVEL ...]]
```