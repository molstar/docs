# Viewer State Management

## ``Canvas3D`` Properties
Properties of the [``Canvas3D``](https://github.com/molstar/molstar/blob/master/src/mol-canvas3d/canvas3d.ts) can be 
changed using [``PluginCommands``](https://github.com/molstar/molstar/blob/master/src/mol-plugin/commands.ts).  


### Change background, select, or highlight color
```ts
import { ColorNames } from 'molstar/lib/mol-util/color/names';
import { PluginCommands } from 'molstar/lib/mol-plugin/commands';

const renderer = plugin.canvas3d!.props.renderer;
PluginCommands.Canvas3D.SetSettings(plugin, { settings: { renderer: { ...renderer, backgroundColor: ColorNames.red /* or: 0xff0000 as Color */ } } });
```
Similarly, `highlightColor` and `selectColor` can be updated.


##Interactivity
TODO some high-level/introductory text here...


A ``Loci`` references a collection of objects and can be created by a [``Selection``](selections.md). For example, the
``Loci`` captures all atoms in chain 'B' of a protein:
```ts
import { Script } from 'molstar/lib/mol-script/script';
import { StructureSelection } from 'molstar/lib/mol-model/structure/query';

const data = plugin.managers.structure.hierarchy.current.structures[0]?.cell.obj?.data;
if (!data) return;

const selection = Script.getStructureSelection(Q => Q.struct.generator.atomGroups({
    'chain-test': Q.core.rel.eq(['B', Q.ammp('label_asym_id')])
}), data);
const loci = StructureSelection.toLociWithSourceUnits(selection);
```
A ``Loci`` can be used to achieve custom [behaviors](#behaviors).


### Log message to Mol* console
The built-in console in the bottom center of the plugin shows log entries.
```ts
plugin.log.message('This message will appear in the Mol* console');
```
Other log levels are: `info`, `warn`, and `error`.


### Show toast message
Toast messages will appear in the bottom right of the plugin and will linger for a limited time before disappearing.
```ts
import { PluginCommands } from 'molstar/lib/mol-plugin/commands';

PluginCommands.Toast.Show(plugin, {
    title: 'Custom Message',
    message: 'A custom toast message that will disappear after 2 seconds.',
    key: 'toast-custom',
    timeoutMs: 2000
});
```

## Behaviors
TODO some high-level/introductory text here...


### Highlight ``Loci``
Highlighting adds a transient overpaint to a representation that will linger until the mouse enters hovers over another 
object. Highlights can be applied to a previously defined ``Loci`` by:
```ts
plugin.managers.interactivity.lociHighlights.highlightOnly({ loci });
```
Reset all highlights by:
```ts
plugin.managers.interactivity.clearHighlights();
```


### Select ``Loci``
Selected elements will appear with distinct visuals and, if applicable, the corresponding sequence positions will be 
shown in the Sequence Viewer panel. Selections persist until removed, for example by clicking the background. A ``Loci``
is selected by:
```ts
plugin.managers.interactivity.lociSelects.select({ loci });
```
Deselect a specific ``Loci`` by:
```ts
plugin.managers.interactivity.lociSelects.deselect({ loci });
```
To deselect everything:
```ts
plugin.managers.interactivity.lociSelects.deselectAll();
```


### Focus ``Loci``
The focus representation shows a ``Loci`` in ball-and-stick representation and, additionally, visualizes non-covalent
interactions between atoms of the ``Loci`` as well as interactions with surrounding residues (default: 5 Å).
```ts
plugin.managers.structure.focus.setFromLoci(loci);
```
Extend an existing focus representation by:
```ts
plugin.managers.structure.focus.addFromLoci(loci);
```
Reset by:
```ts
plugin.managers.structure.focus.clear();
```


### Zoom ``Loci``
A ``Loci`` can also be used to manipulate the camera. Zoom in by:
```ts
plugin.managers.camera.focusLoci(loci);
```
Restore the default camera position by:
```ts
plugin.managers.camera.reset();
```