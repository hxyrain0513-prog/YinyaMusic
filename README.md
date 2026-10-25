# Yinya Music · Interactive Music App

**A Unity prototype that turns a Figma interface design into playable rhythm games, mood journaling, calming music and five-key music creation. The current offline version runs without Arduino hardware.**

![Yinya Music interface overview](Documentation/OfflineOverview.png)

## About the Project

Yinya combines simple key presses, immediate sound and character feedback to support everyday musical interaction between children and caregivers. Children can imitate rhythms, explore instrument sounds, choose a feeling and save melodies. Caregivers can review saved creations, game performance and mood records.

This is a design and development prototype. Game scores describe performance within a task. The project has not established therapeutic effectiveness and does not implement automatic emotion recognition or pronunciation assessment.

## Current Status

| Area | Status |
| --- | --- |
| Offline interaction in the Unity Editor | Implemented and checked in the Editor |
| Local saving, reloading and creation playback | Implemented and checked with isolated test data |
| Five-key touch input, keyboard input and four timbres | Implemented; iPad multitouch still requires device testing |
| WebGL audio activation, native browser text input and storage adaptation | Code written; WebGL build and Safari validation remain pending |
| Arduino / HM-10 BLE | Input interface reserved; real Bluetooth transport is not connected |
| Native iPad installation package | Not yet built or signed |

This repository contains Unity project source. Publishing it on GitHub does not automatically produce a playable website. GitHub Pages requires a separate WebGL build and deployment.

## Features

### Music Games

- **Rhythm play:** listen to a demonstration, wait for a countdown, respond with five keys and receive feedback over four rounds. The current round can be replayed.
- Response timing uses the audio DSP clock. Target notes are spaced 0.7 seconds apart, with a matching window of approximately ±0.35 seconds.
- Results show rhythm accuracy, melody matching and task completion. Wrong keys and missed notes affect the result.
- **Word play:** four offline English word prompts paired with notes. Listen, sing or speak, then confirm completion yourself after the prompt finishes. No microphone recording or automatic pronunciation scoring is performed.

### Emotional Music

- Choose Happy, Calm, Sad or Upset and explicitly save the selection.
- Each entry retains its timestamp. History supports 7-day, 30-day and all-time filters, with pagination.
- Three original calming loops support play, pause, seeking, repeat and favorites.
- A 1-, 5- or 15-minute stop timer fades out during its final 3 seconds.

### Free Creation

- Five keys: Do / Re / Mi / So / La, corresponding to C4 / D4 / E4 / G4 / A4.
- Four synthesized timbres: xylophone, hand drum, chimes and piano, with instrument switching, previews and sound preferences.
- Key highlighting, scale feedback and character reactions. The input layer distinguishes touch, keyboard and reserved hardware sources.
- Records note IDs and timestamps, with a limit of 2 minutes or 2,000 notes per take.
- Name a recording, optionally attach a feeling and save it. Search, favorites, detail views, playback pause and seeking are available.
- Playback reconstructs note events over time. It does not export an MP3 or WAV recording.

### Caregiver Space and Settings

- Game lists, mood timelines, creation links and overview statistics are generated from actual saved records.
- Score charts compare Rhythm play / Level 1 sessions, with a fixed 0–100 vertical axis and actual timestamps on the horizontal axis.
- First use displays empty states rather than preset user records.
- Volume, sustain and gentle-attack preferences are saved locally.
- When no hardware is connected, the app does not display a simulated connection or battery level. Toy light control is unavailable.

## Quick Start

### Requirements

- Original project version: **Unity 2022.3.62f3c1**.
- Install the corresponding Editor through Unity Hub. Check compatibility in a project copy before opening it with another version.
- The first import downloads package dependencies and regenerates the Library folder. Import time depends on the device and network.
- Running the offline prototype does not require Arduino, MCP, Codex, a Figma login or Apple Developer Program membership.

### Run in Unity

1. Download or clone the source. In Unity Hub, add the directory containing `Assets`, `Packages` and `ProjectSettings`.
2. Wait for asset import and C# compilation to finish.
3. Open `Assets/Scenes/YinyaOffline.unity`.
4. Click **Play** and interact in the Game view.
5. Alternatively, choose **Yinya > Build Offline App** to build or open the scene. If the offline scene already exists, the menu opens it. The older **Build Prototype** menu also prioritizes the offline version.

The interface uses a **1194×834 landscape** reference resolution, with Canvas scaling, a design frame and safe-area fitting. Choose an iPad-like landscape aspect ratio in the Game view to inspect the layout.

## User Guide

| Action | Instructions |
| --- | --- |
| Start exploring | Choose Music games, Emotional music or Free creation on the home screen. On the connection page, select Try without connecting |
| Play freely | Open Free creation and tap the five colored keys. On desktop, use number keys **1–5** as well |
| Change instruments | Select Change instrument, preview a sound, choose an instrument and return to playing |
| Record a creation | Select Record my music, play at least one note and finish recording |
| Save a creation | Enter a name, optionally choose a feeling and select Save to My music. Empty recordings cannot be saved as creations |
| Review creations | Open My music to search, filter favorites, view details and play back saved notes |
| Practice rhythm | Open Rhythm play, listen to the demonstration and respond after the countdown. Complete four rounds to see results |
| Explore words | Open Word play, play the prompt, sing or speak along, then confirm completion after the prompt ends |
| Record a mood | Open Emotional music, choose a feeling and save it. Review entries in Mood history |
| Listen to calming music | Open Soothing music, choose a track and play it. Set a stop timer if desired |
| Review as a caregiver | Open the profile/caregiver area, change the date range or select an individual record |

Unsaved drafts last only for the current run. Successfully saved records remain on the current device and do not automatically synchronize across devices.

## Technical Implementation

| Technology | Role |
| --- | --- |
| Unity 2022.3 + C# | Scenes, interaction flow, audio scheduling and data management |
| uGUI / EventSystem | Native buttons, sliders, pointer input and dynamic views |
| TextMeshPro 3.0.7 | Text, input fields and local font resources |
| Unity UI 1.0.0 | Canvas, Image and layout foundations |
| AudioSource / AudioClip | Polyphonic playing, spoken prompts, calming tracks and playback |
| AudioSettings.dspTime | Timing reference for recording and rhythm responses |
| JsonUtility + file storage | Serialization of creations, games, moods and preferences |
| Unity Editor extensions | Scene-building/opening menus and a WebGL build entry point |
| WebGL JavaScript plugin | Audio gesture activation, HTML text input and localStorage adaptation |

### Data Flow

```text
Touch via PianoKey / Keyboard 1–5 / Reserved hardware input
                           |
                  ToyInput.KeyChanged
                           |
                   OfflineApp.OnKey
                    /      |      \
             Play note  Record time  Match rhythm
                           |
              OfflineStore → Local records → Caregiver review
```

C# creates native UI objects at runtime. Characters and instrument illustrations use local Sprites. Buttons, text, records and charts have working logic rather than acting as hotspots on a single flattened design image.

### Core Source Files

| File | Responsibility |
| --- | --- |
| [ToyInput.cs](Assets/Scripts/ToyInput.cs) | Unified input, source ownership, hardware message queue and release on focus loss |
| [PianoKey.cs](Assets/Scripts/PianoKey.cs) | Pointer down/up events, key color and scale feedback |
| [OfflineApp.cs](Assets/Scripts/OfflineApp.cs) | Routing, audio, recording, playback and lifecycle handling |
| [OfflineViews.cs](Assets/Scripts/OfflineViews.cs) | Home, free creation, mood, library and settings views |
| [OfflineGames.cs](Assets/Scripts/OfflineGames.cs) | Rhythm and word activity flow, matching and results |
| [OfflineCaregiver.cs](Assets/Scripts/OfflineCaregiver.cs) | Record filtering, statistics and caregiver details |
| [OfflineStore.cs](Assets/Scripts/OfflineStore.cs) | Data models, JSON persistence and backup recovery |
| [OfflineUI.cs](Assets/Scripts/OfflineUI.cs) | Reusable UI construction helpers |
| [YinyaOfflineBuilder.cs](Assets/Editor/YinyaOfflineBuilder.cs) | Editor scene-building menu |
| [BrowserPlatform.cs](Assets/Scripts/BrowserPlatform.cs) | WebGL input and audio entry points |
| [YinyaBrowser.jslib](Assets/Plugins/WebGL/YinyaBrowser.jslib) | Browser storage and HTML input bridge |
| [YinyaWebBuild.cs](Assets/Editor/YinyaWebBuild.cs) | iPad browser demo build entry point |

### Unified Input and Stuck-Key Prevention

The following excerpt comes from `ToyInput.SetKey`. Each key tracks the input sources currently holding it. An event is emitted only when the overall pressed state changes. Input is released when the app loses focus or pauses.

```csharp
        public void SetKey(int key, bool down, string source)
        {
            if (key < 0 || key >= owners.Length || string.IsNullOrEmpty(source)) return;
            if (down && !AcceptInput) return;
            bool before = owners[key].Count > 0;
            if (down) owners[key].Add(source); else owners[key].Remove(source);
            bool after = owners[key].Count > 0;
            if (before != after) KeyChanged?.Invoke(key, after, AudioSettings.dspTime);
        }
```

### Note Event Recording

`OfflineApp.OnKey` plays a sound on press and records its relative timestamp while recording is active. The saved content consists of replayable note events, not microphone audio.

```csharp
        private void OnKey(int key,bool down,double time)
        {
            if(!down)return;
            PlayNote(key);
            if(liveNote!=null)liveNote.text=noteNames[key];
            if(Recording && recorded.Count<2000)recorded.Add(new PlayedNote(key,(float)(time-recordStart)));
            if(ScreenId=="rhythm"&&GamePhase=="respond")RhythmInput(key,time);
            if(hero!=null)hero.localScale=new Vector3(1.035f,1.035f,1);
        }
```

### Persistence and Recovery

The data models include `Creation`, `PlayedNote`, `MoodEntry`, `GameSession` and `OfflineData`.

- Editor and native platforms use `Application.persistentDataPath/yinya-offline-v1.json`.
- Saving writes a `.tmp` file first, replaces the main file and retains a `.bak` backup. Loading falls back to the backup if necessary.
- The WebGL branch uses browser localStorage through the `.jslib` bridge. Browser validation remains pending.
- There is no cloud synchronization, account login or family-sharing permission system. Browser records belong to the current site; clearing site storage can remove them.

### Future Arduino / BLE Integration

The input layer exposes a thread-safe entry point for complete message lines:

```csharp
toyInput.EnqueueHardwareLine("K,1,1,42"); // Press key 1
toyInput.EnqueueHardwareLine("K,1,0,83"); // Release key 1
```

The format is `K,<key 1–5>,<state 0|1>,<unsigned number>`. The parser validates the fourth field, but timestamps the event using Unity's DSP clock when the message is processed. Hardware-to-app clock synchronization has not been implemented.

A future BLE transport must buffer and reassemble fragmented packets before passing complete lines to this method. Call `ReleaseSource("hardware")` on disconnection. Device scanning, pairing and HM-10 characteristic subscription are not currently implemented.

## Project Structure

```text
Assets/
├── Art/                     Local illustrations and icons
├── Audio/                   Synthesized notes, calming loops and word prompts
├── Fonts/                   Font resources
├── Editor/                  Scene-building and build menus
├── Plugins/WebGL/           JavaScript plugin
├── Scenes/
│   ├── YinyaOffline.unity    Main scene and build entry
│   └── YinyaPrototype.unity  Earlier prototype
├── Scripts/                 C# interaction and storage
└── WebGLTemplates/          Browser demo loading template
Packages/                    Dependency manifest and lockfile
ProjectSettings/             Unity project settings
Documentation/               Interface overview and documentation
```

## Builds and Platform Scope

**Desktop:** Select the target platform in Unity Build Settings, confirm that `YinyaOffline.unity` is enabled and install the required platform module before building. Existing validation primarily covers the Editor; this repository does not claim that every platform package has been tested.

**Browser / iPad demo:** Install WebGL Build Support for the same Editor version, then select `Yinya > Build iPad Browser Demo`. The default output is `Releases/WebGL-iPad`; set `YINYA_WEB_OUTPUT` to choose another directory. Serve the output over HTTP. Browser adaptations and the build entry point are written, but WebGL building and iPad Safari device validation remain pending.

**Native iPad:** Further work requires an iOS build, Xcode compilation, signing and device testing. Repository source cannot be installed directly on an iPad.

## Validation and Limitations

Existing Unity Editor validation records cover:

- Navigation across 15 pages, four timbres and five-note recording.
- Naming, favorites, search, creation playback and JSON save/reload.
- Rhythm test cases scoring 100 for correct responses and 0 for incorrect responses.
- Word activity completion, with confirmation that no pronunciation score is generated.
- Mood and preference persistence, plus the calming music stop timer.
- Rendered screenshots of 18 page states for visual inspection.

These checks used isolated test data and do not establish iPad compatibility. Multitouch, perceived audio quality and latency, safe areas, background behavior, and Safari audio and storage still require device testing.

## Design and Assets

- [Figma design file](https://www.figma.com/design/tQnUQ4QBIdNiwSnfuFBHbC?node-id=0-1)
- [Unity implementation board — English](https://www.figma.com/design/tQnUQ4QBIdNiwSnfuFBHbC?node-id=1622-331)
- [Unity implementation board — Chinese](https://www.figma.com/design/tQnUQ4QBIdNiwSnfuFBHbC?node-id=1607-1291)

Character, expression, instrument and listening illustrations come from the Figma design supplied for this project. Audio includes project-generated synthesized notes, original calming loops and offline spoken prompts. Font license files are retained with the resources.

No open-source license has been assigned to this repository. Confirm the relevant usage and redistribution rights before public release.

## Publishing on GitHub

Upload the contents of this project directory as the repository root. Preserve all `.meta` files, `.gitignore` and `.gitattributes`. In GitHub Desktop, add this directory, commit the files and publish it to your repository.

The source package excludes Unity caches, local user records, isolated test data, the MCP bridge and machine-specific connection scripts. Do not commit `Library`, `Temp`, build outputs, signing certificates or the delivery ZIP itself.
