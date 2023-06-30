# Selections

## Selection from data
Programmatically select with the structures data

```typescript
import { Structure } from '../mol-model/structure';

const ligandData = plugin.managers.structure.hierarchy.selection.structures[0]?.components[0]?.cell.obj?.data;
const ligandLoci = Structure.toStructureElementLoci(ligandData as any);
plugin.managers.camera.focusLoci(ligandLoci);
plugin.managers.interactivity.lociSelects.select({ loci: ligandLoci });
```

## Selection callbacks
If you want to subscribe to selection events (e.g. to change external state in your application based on a user selection), you can use: `plugin.behaviors.interaction.click.subscribe`

Here's an example of passing in a React "set" function to update selected residue positions.
```typescript
import {
  Structure,
  StructureProperties,
} from "molstar/lib/mol-model/structure"
// setSelected is assumed to be a "set" function returned by useState
// (selected: any[]) => void
plugin.behaviors.interaction.click.subscribe(
  (event: InteractivityManager.ClickEvent) => {
    const selections = Array.from(
      plugin.managers.structure.selection.entries.values()
    );
    // This bit can be customized to record any piece information you want
    const localSelected: any[] = [];
    for (const { structure } of selections) {
      if (!structure) continue;
      Structure.eachAtomicHierarchyElement(structure, {
        residue: (loc) => {
          const position = StructureProperties.residue.label_seq_id(loc);
          localSelected.push({ position });
        },
      });
    }
    setSelected(localSelected);
  }
)
```