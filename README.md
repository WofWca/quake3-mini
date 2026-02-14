# quake3-mini

Small (< 5MB) but playable game for Quake III Arena engines,
based on [Open Arena](https://github.com/OpenArena).

![Screenshot of the wrackdm17 map from Open Arena, with bots running around](https://github.com/user-attachments/assets/5452b7b5-e2a5-4e7c-85ec-eb2ab89dfc6a)

## Usage

1. Install a Quake III Arena -based engine,
   such as [ioquake3](https://ioquake3.org/)

   Note that the engine must support Opus audio decoder
   (which is included in ioquake3, `USE_CODEC_OPUS`),
   otherwise sounds will not work.
   The vanilla Quake III Arena engine does not support Opus.

2. Download this project as a `.zip` file.
   On GitHub press "Code" -> "Download ZIP".
3. Rename the `quake3-mini.zip` file into `quake3-mini.pk3`.

4. Alongside the `quake3.exe` file, create a folder named `quake3-mini`,
   and put the `quake3-mini.pk3` file inside of that, as such:

   ```tree
   Quake 3 Arena
   ├── baseq3
   ├── missionpack
   ├── quake3-mini
   │   └── quake3-mini.pk3
   └── quake3.exe
   ```

5. Launch the game using the command line

   ```bash
   quake3.exe +set fs_game quake3-mini +map wrackdm17 +addbot sarge +addbot sarge +addbot sarge
   ```

Note that the game will look differently
depending on whether you have `baseq3` (original Quake III Arena game data).

## Where this project is used

- <https://github.com/WofWca/quake3.xdc>
  where it is packed, together with the Emscripten build of ioquake3,
  in a zip file that should fit in an email.
  That makes it a fully playable Quake III Arena -based game
  that's under 6 MB.

  That project is also the original reason for making this project.
