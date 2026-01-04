# Eddie

A browser-based guitar tab editor and player. No installation required—just open `index.html` and start composing.

## Demo

Open `index.html` in your browser and select a sample song from the dropdown to hear Eddie in action.

## Features

### Editing
- **Click** any position to select it
- **Type 0-9** to enter fret numbers (supports two-digit frets up to 24)
- **Tap again** on a selected note to increment the fret (mobile-friendly)
- **Arrow keys** to navigate (up/down moves between strings and guitars)
- **Space** or **Right Arrow** to advance without overwriting
- **Backspace** or **-** to delete a note
- **Enter** or **Escape** to exit edit mode

### Playback
- **Play/Stop** button for playback control
- **Loop** toggle for continuous playback (enabled by default)
- **BPM control** (40-240) to adjust tempo
- **Sustain toggles** (~) above each beat to let notes ring until the next chord

### Two Guitars
- Two independent guitar tracks that play simultaneously
- **Guitar 2** toggle to show/hide the second track
- Each guitar has its own **waveform selector** (sine, triangle, square, sawtooth)
- Create drones, harmonies, or layer melodies

### Export
- **Export MIDI** - Standard MIDI file compatible with any DAW
- **Export WAV** - 44.1kHz 16-bit stereo audio file

### Save & Share
- Your composition is automatically saved in the URL
- Copy the URL to save your work or share it with others
- Sample songs included in the dropdown menu

## Keyboard Shortcuts

| Key | Action |
|-----|--------|
| `0-9` | Enter fret number |
| `Arrow Keys` | Navigate |
| `Space` | Move right |
| `Backspace` / `-` | Delete note |
| `Enter` / `Escape` | Exit edit mode |

## Mobile Support

Eddie works on mobile devices with a responsive layout:
- Horizontal scrolling for the tab area
- Larger touch targets
- Tap to select, tap again to increment fret number
- Long press to delete a note

## Technical Details

Eddie is a single HTML file with no dependencies. It uses:
- **Web Audio API** for sound synthesis and playback
- **OfflineAudioContext** for WAV export
- **URL hash** for state persistence (LZ-compressed for shorter URLs)

## Built With Claude

This entire project was built by [Claude](https://claude.ai) (Anthropic's AI assistant) via [Claude Code](https://claude.ai/claude-code). The human's contribution was describing what they wanted and composing the sample songs. Claude wrote all the code, including the Web Audio synthesis, MIDI/WAV export, responsive CSS, and this README.

## License

MIT
