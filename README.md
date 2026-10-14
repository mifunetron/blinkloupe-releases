# Loupe

A live viewer for AI image generation. Point it at your ComfyUI output folder, or any folder of images, and every new image
appears full screen on the monitor you choose the moment it is saved, while you keep generating. Sort them from the
keyboard as they land: pick, skip, rate, bookmark, and with ComfyUI re-roll a frame or finish it in high resolution with one
key.

**Needs:** Windows 10 or 11 (64-bit). ComfyUI is optional; A1111, Forge, SwarmUI or any folder of PNG, JPEG, WebP, GIF or
video files works too.

**Get it:** [the latest release](https://github.com/mifunetron/loupe-releases/releases/latest), then see
[Install](#install).

- **Live, without slowing renders.** Loupe is its own process with no node in your graph. It checks the folders once a second
  (one look per folder; a full listing only when something changed) and shows a file only once it is completely written.
- **Never loses your place.** Step back and Loupe stays on the frame you are studying; new arrivals are counted
  ("3 NEW - press End") instead of stealing the screen.
- **Marks stay beside your images as plain files.** Pick (`PICKS.txt`, one file name per line), re-roll requests
  (`REROLLS.txt`), stars and skips (a hidden `.loupe.json`). Nothing is ever moved, renamed or deleted, and any script can
  read the marks.
- **One-key re-roll with ComfyUI.** A ComfyUI PNG carries the exact graph that made it. Loupe gives every sampler a new seed,
  leaves everything else alone (prompt, LoRAs, wildcards, detailers), adds `_reroll` to the save prefix, and queues it. The
  result lands in your output folder and shows up live. Shift+R cancels it while it is still waiting in the queue.
- **Hires on picks.** Shift+P finishes the frame on screen, Ctrl+P every pick in its folder. Loupe uploads the frame to
  ComfyUI and finishes it with the frame's own model, LoRAs, prompts and VAE: an optional upscale model, a resize to the
  target size, then one low-denoise pass (0.30 by default) with tiled VAE. The base pass never re-runs, so the finish
  starts from exactly the pixels you picked. It saves through the frame's own save node with `_hires` on the name.
- **Your own keys.** Settings > Actions binds any free key to a command run on the frame on screen: an upscaler script, an
  editor, another viewer, your pipeline's next step. See [Actions](#actions).
- **Send a frame into your own workflow.** Settings > Send to a workflow binds a key to any ComfyUI workflow with a Load
  Image node (an upscaler, a face fix, img2img, a video): the frame on screen goes in and the workflow is queued, and the
  result arrives beside the frame. See [Send to a workflow](#send-to-a-workflow).
- **Picks can go to a folder too.** Settings > Picks > "Also copy picks to" copies each pick there, for pipelines that don't
  read `PICKS.txt`.
- **Reads the metadata.** Model, LoRA chain with strengths, sampler, scheduler, steps, CFG, seed, denoise and prompts, from
  ComfyUI graphs (traced from the save node back to the sampler that made the image), A1111 / Forge and SwarmUI. ComfyUI
  graphs saved inside videos (ComfyUI's own video savers, Video Helper Suite's Video Combine) are read too, so a video
  re-rolls like a still. Common node packs are understood: Efficiency loaders, samplers and LoRA stacks, Impact's
  wildcard encode and basic pipes, rgthree's Power Lora Loader, Power Prompt and contexts, Comfyroll LoRA stacks and text
  nodes, WAS text and save nodes, and Image Saver.
- **Black-frame alarm.** A frame that comes out black (a NaN render) is flagged in red.

This repository holds Loupe's installers and release notes; the source code is private.

## Install

1. Download `Loupe-<version>-setup.exe` from [the latest release](https://github.com/mifunetron/loupe-releases/releases/latest)
   and run it.
2. Windows may say **"Windows protected your PC"**. Loupe isn't signed with a paid code-signing certificate, so Windows
   doesn't know it yet. Click **More info**, then **Run anyway**.
3. Setup installs for you only, with no administrator rights, into `%LOCALAPPDATA%\Programs\Loupe`. It adds Loupe to the
   Start menu, and to the Desktop if you leave that box ticked.

Loupe draws its window with Microsoft's WebView2, which Windows 11 and up-to-date Windows 10 already have. If yours
doesn't, setup offers Microsoft's download page; install the "Evergreen Runtime" from there.

## Getting started

The first time Loupe starts, a short setup walks through five steps. You can run it again any time from Settings (S) >
**Run the setup again**.

1. **Welcome.**
2. **Your images.** The folders to watch. Loupe offers the output folders it finds: a running ComfyUI's, ComfyUI Desktop's,
   portable ComfyUI's, A1111's, Forge's, SwarmUI's. Subfolders are watched too, four levels down.
3. **ComfyUI (optional).** Loupe finds a ComfyUI on this computer by itself (ports 8188 and 8000). For one elsewhere, type
   its address (see [Servers elsewhere](#servers-elsewhere-https-and-access-tokens)). **Follow its queue** also shows Preview
   Image outputs and images from a ComfyUI on another computer (see [Following ComfyUI](#following-comfyui)).
4. **Screen and keys.** The monitor (automatic picks your largest second monitor), full screen, the keymap, and the daily
   update check.
5. **Ready.** The keys you'll use most. F1 shows all of them, any time.

Then generate as usual: each new image appears as soon as it's completely written. Step back with ← to study one; Loupe
holds it and counts what arrives ("3 NEW - End"); End takes you back to live.

## Updates

Loupe asks github.com once a day whether a newer release is out (Settings > Updates > **Check by itself**; nothing about
you or your images is sent). When there is one, it says so once, and Settings > Updates > **Install** downloads it, checks
its size and SHA-256 checksum against the release, installs it and starts Loupe again. A copy that wasn't installed with
the installer shows a link to the release page instead.

## Uninstall

Windows Settings > Apps > **Loupe** > Uninstall. It asks whether to remove Loupe's settings and log too (default: keep
them). Your picks, ratings and other marks are small files beside your images and are never touched.

## Privacy

- Loupe talks to your ComfyUI (only the address in Settings, or 127.0.0.1) and, once a day if you leave it on, to
  github.com for the update check. Nothing else, and nothing about your images leaves your computer except what you send
  to your own ComfyUI.
- Settings are in `%APPDATA%\Loupe\settings.json`, including any ComfyUI access token, which is never written to the log.
- The log, `%LOCALAPPDATA%\Loupe\loupe.log`, stays on your computer. Settings > Updates > **Open the log folder** finds it if
  someone asks for it to help with a problem.

## Troubleshooting

| What you see | Try |
|---|---|
| "Windows protected your PC" when installing | More info > Run anyway (see [Install](#install)) |
| A blank or white window | Install Microsoft's WebView2 Evergreen Runtime |
| No images appear | Settings (S) > Folders: is the folder there, and are the images deeper than **Subfolder depth**? F5 rescans |
| Images appear on the wrong monitor, or not full screen | Settings > Display > Monitor and Full screen; F11 toggles full screen |
| R does nothing, or says the file has no graph | Re-roll needs a ComfyUI PNG (it carries the graph) and ComfyUI running; the HUD shows "ComfyUI" when it answers |
| "No ComfyUI answered" | Is ComfyUI running? Settings > ComfyUI address, then Test connection; the message says why a server refused |
| Anything else | Settings > Updates > Open the log folder, and send `loupe.log` to whoever gave you Loupe |

## Keys (Loupe keymap)

| Key | Does |
|---|---|
| ← → / wheel | previous / next; stepping back leaves LIVE |
| End | back to LIVE |
| Home / PgUp / PgDn | oldest / 10 back / 10 forward |
| Space | pause / resume LIVE |
| P | pick |
| B | bookmark (B again removes it) |
| Shift+P / Ctrl+P | hires this frame / every pick in this folder not hired yet |
| X | skip (stepping passes over it; K shows skipped frames) |
| 0-5 | stars |
| U | clear pick, skip and stars |
| R / Shift+R | re-roll / undo the last re-roll |
| Ctrl+R | queue every REROLLS.txt mark in this folder |
| I | info: model, LoRAs, sampler, prompts |
| H, F, O | pin the HUD, fit / fill / 1:1, rotate |
| E, C | show in Explorer, copy the path |
| T, F11 | always on top, full screen |
| S | settings |
| L | library: bookmarked and rated frames (newest, oldest, highest rated), and frames by LoRA |
| F1 (or ?) | the key guide - F1 again closes it |
| Esc / Ctrl+Q | quit - asks "Quit Loupe?" first (Esc closes the info panel first) |

The quit question has a **Don't ask again** box; Settings > **Ask before quitting** brings it back.

A **Classic** keymap (X re-roll, R rotate, Q / Esc quit) matches the original Output Viewer.

## Re-roll modes

- **Auto** (default): queue in ComfyUI when it answers, otherwise mark in `REROLLS.txt`.
- **Queue**: always queue in ComfyUI.
- **Mark**: only write `REROLLS.txt`, for pipelines that render the marks themselves. Ctrl+R later queues every mark in the
  folder through ComfyUI and removes the lines it queued.

ComfyUI is found at the address in Settings, or at `127.0.0.1:8188`, then `127.0.0.1:8000`.

## Following ComfyUI

Watching a folder works only when ComfyUI writes to this computer's disk, and it never sees **Preview Image** outputs, which
go to ComfyUI's temp folder. Settings > Following ComfyUI > **Follow the server** (also offered on the first run) adds a
second source: every image ComfyUI finishes, from any workflow, queued from anywhere.

- It works with a ComfyUI on another computer, in Docker or in the cloud. Type its address in Settings (or on the first
  run). See [Servers elsewhere](#servers-elsewhere-https-and-access-tokens) for HTTPS and passwords.
- Loupe listens to ComfyUI's `status` broadcast on `/ws`, which every client receives when the queue changes. When a job
  finishes, it reads that job's outputs from `/history` and fetches each image through `/view`. Where the socket is
  refused, it polls the newest history entry every 2 seconds instead.
- Images are saved to **Save server images to** (default `Pictures\Loupe`) as `<server>\<subfolder>\<file>`, with
  previews under `<server>\previews\`. That folder is watched, so they arrive LIVE, and every mark, re-roll and action
  works on them. Re-rolls and hires go to the same server.
- From a ComfyUI on this computer, an output that already sits in a watched folder is not copied again.
- Names from the server are cleaned before they become paths, so a server can never write outside that folder.
- When it starts following, Loupe shows the server's newest finished job; older history is left alone.

## Servers elsewhere: HTTPS and access tokens

Everything Loupe asks of ComfyUI (following, re-rolls, hires uploads, Test connection) works over `https://` as well as
`http://`. HTTPS goes through Windows' own TLS and certificate store, so a cloud server's ordinary certificate works with
nothing to set up. A self-signed certificate is refused, so on your own network use `http://`.

| What the server wants | What to enter |
|---|---|
| nothing | its address: `http://192.168.1.20:8188`, `https://abc123-8188.proxy.example.net` |
| a token (ComfyUI-Login, a token proxy) | the address, plus Settings > **Access token**. It is sent as `Authorization: Bearer <token>` with every request, the status socket included. |
| a user name and password (an nginx / Caddy password proxy) | `https://user:password@host` in the address, sent as Basic auth. Write `%40` for an `@` in the password. |
| ComfyUI served under a path | `https://host/comfy` |

When a server refuses Loupe, Settings says why: refused token or password (401 / 403), a certificate problem, or
nothing there. The address appears on screen and in the log with any password removed. The token is never logged; it
is stored in Loupe's settings file on your computer.

## Actions

Settings > Actions: each row is a key, a name, a command and **Wait**. Click the key box and press the key: letters with
Shift / Ctrl / Alt, F2-F12 and the like. Settings refuses a key Loupe already uses, and F1 lists your actions under
"Your actions".

The command runs in the frame's folder, with no console window. It is split into arguments first, then these are filled
in, so a path with spaces stays one argument whether or not you quote it:

| Placeholder | Becomes |
|---|---|
| `{file}` | the frame's full path |
| `{folder}` | its folder |
| `{name}` | its file name |
| `{stem}` | the file name without the extension |

With **Wait** on, Loupe waits for the command and shows the last line it prints. Three openings are read specially:
`FINISHED <file> (note)`, `ALREADY <file>` and `ERROR <why>`. A non-zero exit also counts as a failure. With Wait off,
Loupe starts the command and carries on, which suits an editor or another viewer.

```
"C:\Program Files\GIMP 2\bin\gimp-2.10.exe" {file}                      (Wait off)
"C:\Python312\python.exe" "D:\tools\upscale.py" {file}                  (Wait on)
```

## Send to a workflow

Settings > Send to a workflow: each row is a key, a name, a workflow file, **New seed** and **Beside**. The file is what
ComfyUI's **Workflow > Export (API)** writes, or any image that workflow saved (its graph rides inside the PNG). A UI
workflow file (the one with `nodes` and `links`) is refused with that hint. Below each row Settings says what Loupe will do
with the file: which node gets the frame, how many seeds and save nodes it has, which placeholders it fills.

On the key, Loupe uploads the frame to ComfyUI's input folder (`input\loupe`) and puts it into the workflow's loader:

- **Which loader:** the one Load Image node; with several, the ones titled **Loupe** (right-click the node > Title, any
  case, anywhere in the title), then export again. Mask loaders are only filled when titled. A video on screen goes into a
  Load Video node (core Load Video, Video Helper Suite Load Video).
- **Path loaders** (WAS Image Load, Video Helper Suite's path loaders) get the frame's path instead of an upload, which only
  works with a ComfyUI on the same computer.
- **New seed** gives the workflow's samplers fresh seeds each time, like a re-roll. Without it, sending the same frame twice
  is cached by ComfyUI and makes nothing new.
- **Beside** points every save node at `<the frame's folder>/<file name>_<key name>`, relative to ComfyUI's output folder,
  so `set/01_fox_00001_.png` sent with "Upscale 4x" comes back as `set/01_fox_00001__upscale-4x_00001_.png`. For a server
  you follow, the frame's folder in the mirror is used, so the result lands next to it there too. With Beside off, the
  workflow's own save names stand.
- **Placeholders** in any text input take the frame's values: `{positive}` and `{negative}` (its prompts, read as the I
  panel reads them), `{name}`, `{stem}` and `{folder}` (its folder's name). `{positive}, sharp focus` in a CLIPTextEncode
  carries the frame's prompt into an img2img pass; `tests/{stem}_flip` as a save prefix names the result.

The result is an ordinary new image, so it appears live like any other. F1 lists the keys under "Your workflows". Actions and
workflows share one set of keys, and Settings refuses a key Loupe already uses.

## Pipeline hooks

Loupe reads and writes plain files, so any pipeline can work with it:

- **Marks:** `PICKS.txt` (one picked file name per line), `REROLLS.txt` (one line per requested re-roll) and a hidden
  `.loupe.json` (stars, bookmarks, skips) sit beside the images. "Also copy picks to" copies a pick to
  `<folder>\<its set folder>\<file>`. Unpicking removes that copy, unless it has changed since. The folder is never
  watched.
- **Set size, `.loupe-set.json`:** counting files makes the newest frame of a batch that is still rendering read n/n. A
  pipeline that writes this file into the output folder before it queues gets "frame 3/20" instead:

  ```
  {"total": 20}
  ```

  A frame's number is a leading `NN_` in its name (`03_fox_00000.png`). Without one (`ComfyUI_00012_.png`), it is the
  frame's place among the folder's frames that are not re-rolls. A folder of finished versions whose names carry their
  source's numbers counts each kind of file on its own:

  ```
  {"total": 20, "by": "kind", "kinds": ["upscaled", "facefix"]}
  ```

  A file is of a kind when `_<kind>_` or `_<kind>.` appears in its name. `"kinds"` defaults to `["pass", "hires"]`, and the
  HUD reads "upscaled 2/20". Other keys in the file are ignored.
