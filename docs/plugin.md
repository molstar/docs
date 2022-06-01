# Creating Plugin Instance

There are 4 basic ways of instantiating the Mol* plugin.

## ``Viewer`` wrapper

- The most basic usage is to use the ``Viewer`` wrapper. This is best suited for use cases that do not require much custom behavior and are mostly about just displaying a structure.
- See ``Viewer`` class is defined in [src/apps/viewer/app.ts](https://github.com/molstar/molstar/blob/master/src/apps/viewer/app.ts) for available methods and options.

Example usage without using WebPack:

```HTML
<style>
    #app {
        position: absolute;
        left: 100px;
        top: 100px;
        width: 800px;
        height: 600px;
    }
</style>
<!-- 
    molstar.js and .css are obtained from
    - the folder build/viewer after cloning and building the molstar package 
    - from the build/viewer folder in the Mol* NPM package
-->
<link rel="stylesheet" type="text/css" href="molstar.css" />
<script type="text/javascript" src="./molstar.js"></script>

<div id="app"></div>

<script type="text/javascript">
    var viewer = new molstar.Viewer('app', {
        layoutIsExpanded: false,
        layoutShowControls: false,
        layoutShowRemoteState: false,
        layoutShowSequence: true,
        layoutShowLog: false,
        layoutShowLeftPanel: true,

        viewportShowExpand: true,
        viewportShowSelectionMode: false,
        viewportShowAnimation: false,

        pdbProvider: 'rcsb',
        emdbProvider: 'rcsb',
    });
    viewer.loadPdb('7bv2');
    viewer.loadEmdb('EMD-30210', { detail: 6 });
</script>
```

When using WebPack (or possibly other build tool) with the Mol* NPM package installed, the viewer class can be imported using 

```ts
import { Viewer } from 'molstar/build/viewer/molstar'

function initViewer(target: string | HTMLElement) {
    return new Viewer(target, { /* options */})
}
```

## ``PluginContext`` with built-in React UI

- For more customization options it is possible to use the [``PluginContext``](https://github.com/molstar/molstar/blob/master/src/mol-plugin/context.ts) directly.
- When creating the plugin instance it is possible to customize the [``PluginSpec``](https://github.com/molstar/molstar/blob/master/src/mol-plugin/spec.ts).
- The default [``PluginSpec``](https://github.com/molstar/molstar/blob/master/src/mol-plugin/spec.ts) is available [here](https://github.com/molstar/molstar/blob/master/src/mol-plugin/spec.ts).
- [``PluginConfig``](https://github.com/molstar/molstar/blob/master/src/mol-plugin/config.ts) object provides additional customization options.
- See the [Viewer State Management](viewer-state.md) section for more information on customizing things like background.
- See the [Data State Management](data-state.md) section for more information on build the state.

```ts
import { DefaultPluginUISpec, PluginUISpec } from 'molstar/lib/mol-plugin-ui/spec';
import { createPluginAsync } from 'molstar/lib/mol-plugin-ui/index';
import { PluginConfig } from 'molstar/lib/mol-plugin/config';

const MySpec: PluginUISpec = {
    ...DefaultPluginUISpec(),
    config: [
        [PluginConfig.VolumeStreaming.Enabled, false]
    ]
}

async function createPlugin(parent: HTMLElement) {
    const plugin = await createPluginAsync(parent, MySpec);

    const data = await plugin.builders.data.download({ url: '...' }, { state: { isGhost: true } });
    const trajectory = await plugin.builders.structure.parseTrajectory(data, format);
    await this.plugin.builders.structure.hierarchy.applyPreset(trajectory, 'default');

    return plugin;
}

createPlugin(document.getElementById('app')!); // app is a <div> element
```

To use the plugin (with the React UI) inside another React app:

```ts
import { useEffect, createRef } from "react";
import { createPluginUI } from "molstar/lib/mol-plugin-ui";
import { PluginUIContext } from "molstar/lib/mol-plugin-ui/context";
import "molstar/build/viewer/molstar.css";

declare global {
  interface Window {
    molstar: PluginUIContext;
  }
}

window.molstar = window.molstar || {};

export function MolStarWrapper() {
  const parent = createRef<HTMLDivElement>();

  useEffect(() => {
    async function init() {
      window.molstar = await createPluginUI(parent.current as HTMLDivElement);

      if (window.molstar) {
        const data = await window.molstar.builders.data.download(
          { url: "https://files.rcsb.org/download/3PTB.pdb" }, /* replace with your URL */
          { state: { isGhost: true } }
        );
        const trajectory =
          await window.molstar.builders.structure.parseTrajectory(data, "pdb");
        await window.molstar.builders.structure.hierarchy.applyPreset(
          trajectory,
          "default"
        );
      }
    }
    init();
    return () => {
      window.molstar?.dispose();
    };
  }, []);

  return <div ref={parent} style={{ width: 640, height: 480 }}/>;
}

```

## ``PluginContext`` without built-in React UI

- The [``PluginContext``](https://github.com/molstar/molstar/blob/master/src/mol-plugin/context.ts) can be instantiated without using the default React UI.

```HTML
<div id='molstar-parent' style='position: absolute; top: 0; left: 0; right: 0; bottom: 0'>
    <canvas id='molstar-canvas' style='position: absolute; top: 0; left: 0; right: 0; bottom: 0'></canvas>
</div>
```

```ts
import { DefaultPluginSpec, PluginSpec } from 'molstar/lib/mol-plugin/spec';
import { PluginContext  } from 'molstar/lib/mol-plugin/context';
import { PluginConfig } from 'molstar/lib/mol-plugin/config';

const MySpec: PluginSpec = {
    ...DefaultPluginSpec(),
    config: [
        [PluginConfig.VolumeStreaming.Enabled, false]
    ]
}

async function init() {
    const plugin = new PluginContext(MySpec);
    await plugin.init();

    const canvas = <HTMLCanvasElement> document.getElementById('molstar-canvas');
    const parent = <HTMLDivElement> document.getElementById('molstar-parent');

    if (!plugin.initViewer(canvas, parent)) {
        console.error('Failed to init Mol*');
        return;
    }

    const data = await plugin.builders.data.download({ url: '...' }, { state: { isGhost: true } });
    const trajectory = await plugin.builders.structure.parseTrajectory(data, format);
    await plugin.builders.structure.hierarchy.applyPreset(trajectory, 'default');
}

```

## ``Canvas3D`` without built-in state management

- The ``PluginContext`` object from the above examples can be completely omitted.
- See [Browser Tests](https://github.com/molstar/molstar/tree/master/src/tests/browser) for example usage.

```ts
const canvas = document.getElementById('canvas'); // parent <canvas> element
const canvas3d = Canvas3D.create(Canvas3DContext.fromCanvas(canvas));
canvas3d.animate();
// use the canvas3d object here
```
