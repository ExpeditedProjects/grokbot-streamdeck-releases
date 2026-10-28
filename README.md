# Voice Chat for Grok Bot

Start a voice chat with a Grok Bot bot from a Stream Deck key. Tap the key and the call starts; tap it again to hang up. While the call is up, the key shows the bot's avatar, animated.

Each key is tied to one bot, so you can put your most-used bots on their own keys.

Grok Bot has no way to start a voice chat from outside the app, so this plugin includes a small helper app, **Grok Voice**, that presses Grok Bot's "Start voice chat" button for you through macOS Accessibility. [How it works](#how-it-works) has the details, including what that permission allows.

This project isn't affiliated with Elgato or with the makers of Grok Bot.

## Requirements

| | Minimum | Notes |
| --- | --- | --- |
| macOS | 13 Ventura | Apple silicon or Intel. The plugin is macOS only. |
| [Stream Deck](https://www.elgato.com/downloads) app | 7.1 | Any Stream Deck device with keys. |
| Grok Bot for macOS | 0.75.5 | Signed in, with at least one bot. Tested through 0.75.8. |

Grok Voice's status window (see [Troubleshooting](#troubleshooting)) shows the versions you have installed.

## Install

1. Download [**com.expeditedprojects.grokbot.streamDeckPlugin**](https://github.com/ExpeditedProjects/grokbot-streamdeck-releases/releases/latest/download/com.expeditedprojects.grokbot.streamDeckPlugin) from the [latest release](https://github.com/ExpeditedProjects/grokbot-streamdeck-releases/releases/latest).
2. Double-click it. Stream Deck asks to install the plugin; say yes, and **Voice Chat for Grok Bot** appears in Stream Deck's action list.

Grok Voice is signed with a Developer ID and notarized by Apple. If macOS asks whether to open it the first time because it was downloaded from the internet, click **Open**.

## Set up a key

1. In Stream Deck, drag **Voice Chat for Grok Bot → Talk to Grok Bot** onto a key.
2. Pick a bot in the **Bot** dropdown. A new key starts on the first bot in the list, and your choice saves as soon as you pick it.
3. The first time, the settings panel says Grok Voice needs Accessibility. Click **Allow Accessibility…**, then turn on **Grok Voice** in System Settings → Privacy & Security → Accessibility. Within a few seconds the panel says "Grok Voice is ready". You only do this once, not per key.
4. Tap the key to start a voice chat. Tap again to hang up.

Grok Bot comes to the front on the bot you picked and the call starts.

## Using the keys

- **What the key shows.** The bot's avatar when idle, a slow pulse while a call starts or ends, and the avatar animating while the call is up. The avatar is drawn the first time a key uses that bot, so a brand-new key may show a plain chat bubble for a moment.
- **Tapping during a call** hangs up, whichever key you tap. One call can be up at a time, as in Grok Bot itself.
- **Titles are yours.** Type any title in Stream Deck's Title field, or leave it empty. The plugin never changes it, and the key works the same either way. Stream Deck draws the title over the avatar, at the bottom by default; change its position and font in Stream Deck.
- **Don't set a custom image on the key.** Stream Deck shows a custom image in place of the plugin's, so the avatar and the call animation disappear. Use "Reset to default" on the key's icon to get them back.
- **Calls you start inside Grok Bot** don't animate any key. The plugin only tracks calls it started or hung up.
- **Errors** show as a warning triangle on the key, for example when the bot has a draft in its message box (the voice button only appears while the box is empty).

## Update

Download the latest release and double-click it, as when installing. Your keys, their bots and the Accessibility permission are kept. If Grok Voice is running (its window is open, or a call is up), quit it first so the new version is the one that runs.

## Troubleshooting

Opening Grok Voice shows a status window: whether it has Accessibility, your Stream Deck version against the minimum the plugin needs, and the installed versions of the plugin and Grok Bot. There's nothing to set in it. To open it:

```bash
open "$HOME/Library/Application Support/com.elgato.StreamDeck/Plugins/com.expeditedprojects.grokbot.sdPlugin/bin/Grok Voice.app"
```

- **The key shows a warning triangle.** Check that the bot's message box in Grok Bot is empty, and that Grok Voice still has Accessibility. The plugin log has the exact error.
- **"Grok Voice needs Accessibility" won't go away.** In System Settings → Privacy & Security → Accessibility, turn Grok Voice off and on again, or remove it with **–** and click **Allow Accessibility…** again.
- **The key shows a plain bubble, or doesn't animate.** It has a custom image (use "Reset to default" on its icon), or drawing the avatar failed (look for `Grok icon load failed` in the plugin log).
- **Tapping the key opens Grok Voice's window instead of starting a call.** The key has no bot saved yet (open its settings to pick one), or an older Grok Voice is still running (quit it and tap again).
- **The bot list is empty.** Open Grok Bot and make sure you're signed in; the list comes from Grok Bot's data on your Mac.
- **The voice button isn't found after a Grok Bot update.** Grok Voice finds the button by its English label, so a renamed button or a non-English interface stops it. Please [open an issue](https://github.com/ExpeditedProjects/grokbot-streamdeck-releases/issues) with your Grok Bot version.

Logs:

- Plugin: `~/Library/Application Support/com.elgato.StreamDeck/Plugins/com.expeditedprojects.grokbot.sdPlugin/logs/`
- Stream Deck: `~/Library/Logs/ElgatoStreamDeck/StreamDeck.log`

## Uninstall

1. In Stream Deck, open Settings → Plugins, select **Voice Chat for Grok Bot**, and uninstall it.
2. In System Settings → Privacy & Security → Accessibility, select **Grok Voice** and remove it with **–**.
3. Delete what's left in your Library:

   ```bash
   rm -rf "$HOME/Library/Application Support/Grok Voice" \
          "$HOME/Library/Application Support/com.expeditedprojects.grokbot"
   ```

## How it works

The plugin never touches Grok Bot itself. When you tap a key, it asks Grok Voice to toggle a call with the key's bot. Grok Voice opens the bot in Grok Bot with a `grokbot://` link, waits for that bot's chat to appear, and clicks its "Start voice chat" button (or "Hang up" during a call). It then reports the call's state in a small file that the plugin watches to animate the keys, and quits when the call ends.

### Privacy and security

- **No network access.** The plugin and Grok Voice make no network requests. The bot list and avatars come from Grok Bot's data on your Mac.
- **Accessibility goes to Grok Voice only.** Accessibility lets an app read and press controls in other apps. Grok Voice uses it only to find and press Grok Bot's voice and hang-up buttons. Keeping that in a separate app means you don't grant it to Stream Deck, which would extend it to every Stream Deck plugin.
- **Other apps can't drive it.** Grok Voice doesn't register a link type, so web pages and other apps can't start or end calls; only the plugin reaches it, by its location inside the plugin. It's built with the hardened runtime, so other code can't be injected into it, and it's signed and notarized, so macOS can check it hasn't been altered.
- **What it stores:** the call's state and the Accessibility check result in `~/Library/Application Support/Grok Voice/`, and avatar images in `~/Library/Application Support/com.expeditedprojects.grokbot/icons/`.

## Feedback

Bug reports and feature requests are welcome in [Issues](https://github.com/ExpeditedProjects/grokbot-streamdeck-releases/issues). Please include your macOS, Stream Deck and Grok Bot versions (Grok Voice's status window shows them).

## License

[MIT](LICENSE)
