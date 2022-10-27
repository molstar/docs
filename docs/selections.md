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
