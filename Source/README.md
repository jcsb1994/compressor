## Template overview

### `processBlock()`
- `getTotalNumInputChannels()` : how many channels the host sends
- `getTotalNumOutputChannels()` : how many must be output by the plugin

Examples:
| Plugin type	| In | Out |
|-------|-------|-------|
| Mono FX | 1 | 1 |
| Stereo FX |	2 |	2 |
| Mono→Stereo | 1 | 2 |
| Synth |	0 |	2 |

## Steps of things to do:
Do not add a "main component". That may be good for standalone apps but not plugins
- The plugin already has a main component: PluginProcessor + PluginEditor.
  - Processor: Audio (read) thread
  - Editor: UI (write) thread

### 1. APVTS
It bridges the gap between the processor (audio thread) and the editor (UI thread), offering several key advantages

The apvts instance must be added to the processor class.

### 2. Parameters
For each param that must be controllable, automated by the DAW, ... a param must be added to the apvts instance.

For each parameter you want fast access in processBlock:
- Add atomic ptr in processor private: `std::atomic<float>* gainParam = nullptr;`
  - APVTS owns the atomic, just keep a pointer to it.
  - It is a thread safe ptr, and params are accessed by editor (UI) to write and processor (audio) to read.
- Initialize in constructor: `gainParam = apvts.getRawParameterValue("gain");`
- Then read inside processBlock
  - Must load the atomics from the apvts, e.g. `float gainDb = gainParam->load();`