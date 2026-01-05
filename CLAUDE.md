# Eddie - Browser-based Guitar Tab Editor

A single-file HTML application for composing and playing guitar tablature with two guitar tracks, audio synthesis, and URL-based state persistence.

## File Structure

- `index.html` - The entire application (HTML, CSS, JavaScript in one file)
- `songs.json` - Sample songs with compressed hashes

## Core Architecture

### Grid System
- 2 guitar staffs (Guitar 1 for chords, Guitar 2 for melody)
- 6 strings per staff (standard tuning: e B G D A E)
- 32 positions (columns) per staff
- Each cell holds a fret number (0-24) or empty (en-dash)

### State Storage
State is persisted in the URL hash using custom binary compression:

1. **PackChord encoder** (`PackChord` object, ~line 459-710): Chord-based compression with predefined dictionary
   - Format prefix: `D` = dictionary format, `C` = legacy chord format
   - Predefined chords (24 common voicings) use 7 bits
   - Single notes use 10 bits
   - Custom chord shapes use variable bits
   - Achieves ~85% compression vs raw encoding

2. **Legacy Pack encoder** (`Pack` object, ~line 359-457): Note-by-note binary encoding
   - 15 bits per note (staff:1 + string:3 + position:6 + fret:5)

### Audio System
- Web Audio API with oscillator-based synthesis
- Waveform options: sine, triangle, square, sawtooth
- Per-string frequency calculation from standard tuning
- Sustain system: notes ring until next chord on same staff
- Arpeggio/strum mode: 20ms delay between strings for guitar-like sound

## Key Features

### Playback
- `playPosition()` (~line 1580): Main playback loop, handles looping and soundtrack mode
- `startPlayback()` / `stopPlayback()`: Control functions
- BPM control (40-240)

### Soundtrack Mode
When enabled, plays current song 2-5 times, then auto-generates a new random song:
- State variables: `soundtrackEnabled`, `soundtrackPlayCount`, `soundtrackTargetCount`
- Toggle: `toggleSoundtrack()` (~line 1444)
- Loop detection in `playPosition()` triggers new song generation

### Random Song Generator
`generateRandomSong()` (~line 2082): Uses music theory to create songs:
- Circle of fifths chord progressions
- Melody notes that fit chord tones on Guitar 2
- Chromatic/7ths mode for more harmonic variety
- Variable sustain patterns and chord rhythms

### Chord Mode
Mobile-friendly chord entry system:
- Toggle with "Chords" button
- Checkboxes above sustain row to select multiple positions
- Chord palette buttons place voicings at all selected positions
- State: `chordModeEnabled`, `chordSelectState` array

### Export
- MIDI export: Creates standard MIDI file
- WAV export: Renders audio offline
- Clipboard: Copies tab as ASCII art

## State Variables (Key Ones)

```javascript
let isPlaying = false;
let loopEnabled = true;
let soundtrackEnabled = false;
let soundtrackPlayCount = 0;
let soundtrackTargetCount = 0;  // Random 2-5
let arpeggioEnabled = false;
let chromaticEnabled = false;
let chordModeEnabled = false;
let guitar2Enabled = true;
```

## CSS Notes

- Dark theme: body `#1a1a1a`, container `#0d0d0d`
- Green accent: `#00ff00`
- Chord checkboxes use `color: #666` (was `#333`, too dark to see)
- `.chord-mode-active` class on `.container` shows chord UI elements

## Common Modifications

### Adding a new toggle button
1. Add button HTML in `.controls` or `.sample-songs-container` div (~line 416-430)
2. Add state variable near other toggles (~line 826 or 1395)
3. Add toggle function near `toggleChromatic()` (~line 1439)

### Adding a predefined chord
Add to `PackChord.PREDEFINED` array (~line 465) and duplicate in `generateRandomSong()` CHORDS object (~line 2091)

### Modifying playback behavior
Edit `playPosition()` function (~line 1580), particularly the loop detection block at the start
