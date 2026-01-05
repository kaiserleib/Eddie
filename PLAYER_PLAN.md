# Eddie Player - Embeddable Music Player Plan

A minimal, self-contained music player for Eddie JSON exports, designed for embedding in single-HTML-file games and applications.

## JSON Export Format

```json
{
  "version": 1,
  "bpm": 120,
  "tuning": [329.63, 246.94, 196.00, 146.83, 110.00, 82.41],
  "tracks": [
    {
      "name": "Guitar 1",
      "waveform": "triangle",
      "notes": [
        [0, 4, 3],
        [0, 2, 0],
        [4, 3, 2]
      ],
      "sustains": [0, 4, 8, 12]
    },
    {
      "name": "Guitar 2",
      "waveform": "square",
      "notes": [[0, 0, 3], [1, 0, 5], [2, 0, 3]],
      "sustains": []
    }
  ]
}
```

### Format Notes
- `notes`: Array of `[position, string, fret]` tuples (compact)
- `sustains`: Array of positions where sustain is active
- `tuning`: Frequencies for open strings (e→E, high to low), defaults to standard tuning if omitted
- `waveform`: One of `sine`, `triangle`, `square`, `sawtooth`

## Player API Design

```javascript
const player = new EddiePlayer();

// Loading
player.load(jsonData);           // Load from parsed JSON
player.loadFromString(jsonStr);  // Load from JSON string

// Playback control
player.play();                   // Start/resume playback
player.stop();                   // Stop and reset to beginning
player.pause();                  // Pause at current position

// Settings
player.setTempo(140);            // Override BPM
player.setLoop(true);            // Enable looping (default: true)
player.setVolume(0.5);           // Master volume 0-1
player.setTrackEnabled(0, true); // Enable/disable track by index
player.setArpeggio(true);        // Strum effect

// State
player.isPlaying();              // Returns boolean
player.getPosition();            // Current position (0-31)
player.getDuration();            // Length in positions

// Events (for game sync)
player.onPosition = (pos) => {}; // Called each beat
player.onLoop = () => {};        // Called when song loops
player.onEnd = () => {};         // Called when song ends (if not looping)
```

## Implementation Outline

```javascript
class EddiePlayer {
  constructor() {
    this.audioCtx = null;
    this.data = null;
    this.position = 0;
    this.isPlaying = false;
    this.loopEnabled = true;
    this.tempo = 120;
    this.volume = 0.3;
    this.arpeggio = false;
    this.activeNotes = [];
    this.timeout = null;

    // Callbacks
    this.onPosition = null;
    this.onLoop = null;
    this.onEnd = null;
  }

  // ... methods
}
```

### Core Methods to Implement

1. **`load(data)`** - Parse and validate JSON, find last position
2. **`play()`** - Init AudioContext, start tick loop
3. **`tick()`** - Play notes at current position, schedule next tick
4. **`playNote(freq, duration, sustain, waveform)`** - Create oscillator with envelope
5. **`stop()`** - Clear timeouts, stop oscillators, reset position

## Size Budget

Target: < 3KB minified (< 1.5KB gzipped)

| Component | Est. Size |
|-----------|-----------|
| Class shell + state | 300B |
| Audio context + oscillator | 500B |
| Note scheduling + sustain | 600B |
| Playback loop + timing | 400B |
| Event callbacks | 200B |
| Load/parse | 300B |
| **Total** | ~2.3KB |

## Usage Example in Game

```html
<!DOCTYPE html>
<html>
<head>
  <title>My Game</title>
  <script>
  // EddiePlayer code pasted here (~2KB)
  class EddiePlayer { ... }

  // Game music data (exported from Eddie)
  const MUSIC = {"bpm":120,"tracks":[...]};

  // Game code
  const music = new EddiePlayer();
  music.load(MUSIC);
  music.setLoop(true);

  // Sync game events to music
  music.onPosition = (pos) => {
    if (pos % 4 === 0) flashScreen(); // Flash on downbeat
  };

  function startGame() {
    music.play();
  }
  </script>
</head>
<body>
  <button onclick="startGame()">Start</button>
</body>
</html>
```

## Export Button in Eddie

Add "Export JSON" button that:
1. Collects all note data from grid
2. Collects sustain states
3. Includes tempo and waveform settings
4. Downloads as `.eddie.json` file

## Future Enhancements (v2)

- Multiple sections/patterns with arrangement
- Per-track volume
- Effects (vibrato, pitch bend)
- Compressed binary format option
- Import JSON back into Eddie for editing

## Tasks

1. [ ] Add JSON export to Eddie (`exportJson()` function)
2. [ ] Create standalone `eddie-player.js`
3. [ ] Test player in isolation
4. [ ] Create minified version
5. [ ] Add example game using the player
6. [ ] Document integration guide
