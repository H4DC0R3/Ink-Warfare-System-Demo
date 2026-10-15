# Demo Build

> **Config reference**  
The demo config files used by the downloadable demo are included in `Demo/Config/DefaultEngine.ini` and `Demo/Config/DefaultEditor.ini`. Use them only as reference files when comparing the demo presentation with a clean Unreal Engine project, because Unreal's default project settings can produce a different visual result.

The demo project is included inside the plugin package and is intended to show Ink Warfare System working in a playable environment.

## What the demo shows

- **Ink Battle** (`LVL_IWS_Gameplay_01`): team turf war with bots, swimming in team paint, swim trails, ink tank and refill, zones and match score.
- **Spray** (`LVL_IWS_Demo_Spray`): first person spray cans with free colors, caps and intensity.
- **Cleaning** (`LVL_IWS_Demo_Cleaning`): a water hose that washes dirty walls and floors, with running and drying water.
- Main menu with map choice, lobby with teams and bots, late join sync.
- Save and load of the paint (F5 / F9 in the tool demos).

See [Paint Tools and Demos](../Docs/PaintToolsAndDemos.md) for the controls.

## What the demo does not include

- Plugin source code.
- Complete production AI.
- Final game-ready matchmaking.
- Final project-specific balancing.

## Demo project location

The demo project is included inside the plugin package. Use it as the first reference project after installing the plugin.

A packaged Windows executable is distributed through the GitHub Releases page of this repository:

```text
InkWarfareSystem_Demo_Windows.zip
```

## How to test

1. Install the plugin package.
2. Open the included demo project.
3. Open `LVL_IWS_MainMenu` and start Play In Editor, or run the packaged demo.
4. Host and pick a map.
5. Shoot, spray or wash paintable surfaces.
6. Verify that paint appears and team colors behave as expected.
7. For multiplayer, join with a second instance after the match started and check that it sees the paint.

## Notes

Packaged demos can be large. A standalone executable build is attached to a GitHub Release instead of committed directly to the repository.
