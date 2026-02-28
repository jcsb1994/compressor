## Steps of things to do:
Do not add a "main component". That may be good for standalone apps but not plugins
- The plugin already has a main component: PluginProcessor + PluginEditor.
- What you want is structure, not more top-level classes.

1. APVTS
It bridges the gap between the processor (audio thread) and the editor (UI thread), offering several key advantages
