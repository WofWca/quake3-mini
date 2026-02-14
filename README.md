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
   On GitHub press "Code" ->
   "[Download ZIP](https://github.com/WofWca/quake3-mini/archive/refs/heads/master.zip)".
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

## How it was made

First, unpack all `.pk3`s of
[Open Arena 0.8.8 mod](https://web.archive.org/web/20240716135128/http://download.tuxfamily.org/openarena/rel/088/openarena-0.8.8.zip)
into one directory.
Then do the following:

- Remove all maps except one
- Remove all models except one
- Remove sounds except those that are actually used:

  the `\s_list` command will help.
  To get the result of running the command in text form,
  utilize `\logfile 1`.

- Compress the sounds with

  ```bash
  set -e

  while read f; do
    # Create dir recursively
    mkdir -p out/$f;
    rm --dir out/$f;
    # https://ffmpeg.org/ffmpeg-codecs.html#toc-libopus-1
    ffmpeg -i "openarena-0.8.8/$f.wav" -c:a libopus -compression_level:a 10 -b:a 32k -cutoff:a 20000 "out/$f.opus"
  done <s_list.txt

  wait
  ```

- Install <https://imagemagick.org>,
  then from `\imagelist` command, get `imagelist.txt`,
  then

  ```bash
  set -e

  while read f; do
      # Create dir recursively
      mkdir -p out/$f;
      rm --dir out/$f;

      # Convert .tga files and re-compress .jpg files.
      # magick -quality 50 "openarena-0.8.8/$f" "out/${f%.(tga|jpg)}.jpg"
      magick "openarena-0.8.8/$f.tga" -resize 50% -quality 100 "out/$f.png" &
      magick "openarena-0.8.8/$f.jpg" -quality 50 "out/$f.jpg" &
  done <imagelist.txt

  wait
  ```

  Note that some `.tga` files utilize transparency,
  so `.png` must be used (because `.jpg` doesn't support transparency).
  Some engines' `\imagelist` also prints wherther RGB or RGBA
  (with transparency) is used.

  I personally hand-picked the ones that need transparency
  and converted those to `.png`, whereas others to `.jpg`.

  Also some things, like the cursor, don't seem to like to be `.png`s either,
  so I kept them as `.tga`s. Though IIRC they can be resized, with `magick`.

  Also note that some big `.tga`s, such as `gfx/2d/crosshaira.tga`,
  will get ~95% smaller when zipped,
  so there is no need to convert them.

- Remove a bunch of other stuff

For some more info, see git commit history.
The initial commit is the full Open Arena 0.8.8 mod.

TODO:

- Re-compress models, especially player models,
  which are currently responsible for ~800 KB of the final `.pk3` file.
- Re-compress the map.
- Remove shaders.

## Where this project is used

- <https://github.com/WofWca/quake3.xdc>
  where it is packed, together with the Emscripten build of ioquake3,
  in a zip file that should fit in an email.
  That makes it a fully playable Quake III Arena -based game
  that's under 6 MB.

  That project is also the original reason for making this project.
