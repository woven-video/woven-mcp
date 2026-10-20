# Woven MCP connector

[Woven Video](https://www.woven.video) is an AI video editor for Mac. You import your footage, ask for the edit in chat, and fine-tune it on a real timeline. It is built for short-form video such as Reels, TikToks and YouTube Shorts.

This connector lets AI apps on your Mac, such as Claude Code and Codex, make and edit videos in Woven. Through it, the app can:

- find clips on your Mac and bring them into a workspace
- look at the footage
- build and edit reels, and check frames
- add captions
- make images, video and audio
- export

Woven does the editing and rendering, using your Woven account and credits.

It is a local MCP server: your AI app starts it on your Mac, and it talks to the Woven app there. Woven has no public web API, so the connector doesn't work in chat apps or in cloud and web sessions.

## Requirements

- macOS 15 or later.
- Woven installed in `/Applications` or `~/Applications`.
- A Woven account with a plan or the 3-day free trial (card required). See [pricing](https://www.woven.video/pricing).
- An AI app that runs local MCP servers on your Mac.

## Set up

### Recommended: paste the setup prompt

Copy the prompt for your app from [woven.video/setup.md](https://www.woven.video/setup.md) and paste it into that app on your Mac, once. The same prompts are in Woven under **Settings → AI apps**.

The prompt:

1. checks that it is running on a Mac;
2. installs Woven with Homebrew, only if you agree;
3. adds Woven's connector to the app;
4. opens Woven so you can sign in.

That route doesn't need this package or Node.js.

### Manual: use this package

This package is a small launcher. It finds Woven on your Mac and starts the connector that ships inside the app. It needs Node.js for `npx`.

1. Install Woven. Either [download it](https://release.woven.video/Woven.dmg) and move it to Applications, or run:

   ```bash
   brew install --cask woven-video/tap/woven
   ```

2. Open Woven once and sign in.
3. Add the connector to your AI app.

**Claude Code**

```bash
claude mcp add --scope user woven -- npx -y @woven-video/mcp
```

**Codex**

```bash
codex mcp add woven -- npx -y @woven-video/mcp
```

**Other apps** that take a JSON MCP config, such as Cursor:

```json
{
  "mcpServers": {
    "woven": {
      "command": "npx",
      "args": ["-y", "@woven-video/mcp"]
    }
  }
}
```

4. Restart the app, or start a new session, so it loads the Woven tools.
5. Ask it to list your Woven workspaces.

You can also skip this package and point your app straight at the connector. That is what the setup prompt does:

- command: `/Applications/Woven.app/Contents/Resources/woven-sidecar`
- argument: `--mcp-stdio`

## Tools

The tools come from the Woven app, so they change when Woven updates. This list is from Woven 0.2.23.

| Area | Tools |
|---|---|
| Workspaces and files | `list_workspaces` (start here), `create_workspace`, `list_files`, `read_text_file`, `import_media`, `download_file`, `download_video` |
| Footage and audio | `look_at_media`, `transcribe_media`, `extract_audio`, `identify_song` |
| Reels | `list_reels`, `read_reel`, `write_reel_spec`, `render_frame`, `export_reel`, `open_in_woven`, `read_woven_guide`, `list_sfx_library` |
| Captions | `generate_captions`, `edit_caption_words` |
| Generation | `list_media_models`, `generate_image`, `generate_video`, `generate_audio`, `get_generation_status`, `cancel_generation` |

Every tool except the guide reader needs a signed-in Woven account with a plan or the free trial.

Five tools spend Woven credits: the three generators, captions and transcription. The generation and transcription tools tell your AI app to get your go-ahead before the first paid use.

## How the launcher works

`bin/woven-mcp` is a short shell script:

1. It looks for `Woven.app` in `/Applications`, then in `~/Applications`.
2. It runs `Woven.app/Contents/Resources/woven-sidecar --mcp-stdio`.

It never writes to stdout, because stdout carries the MCP connection. If Woven isn't installed, it prints where to get it to stderr and exits with code 1. The script itself downloads nothing.

## Troubleshooting

- **"Woven isn't installed"** in your app's MCP log: install Woven into `/Applications` or `~/Applications`, then restart the app.
- **Tools say you need to sign in or start a plan:** open Woven, sign in, and start a plan or the trial.
- **No Woven tools after setup:** restart the app or start a new session. Apps load new MCP servers when they start.
- **An older Woven:** update it from the menu bar with **Woven → Check for Updates…**.

## Links

- [Woven product guide](https://www.woven.video/guide)
- [What is a video editing MCP server?](https://www.woven.video/video-editing-mcp-server)
- [Privacy](https://www.woven.video/privacy) · [Terms](https://www.woven.video/terms) · [Contact](https://www.woven.video/contact)
- MCP Registry name: `io.github.woven-video/woven`

## License

The MIT license in [LICENSE](LICENSE) covers only this launcher and its packaging. The Woven app and its connector are proprietary and covered by [Woven's terms](https://www.woven.video/terms).
