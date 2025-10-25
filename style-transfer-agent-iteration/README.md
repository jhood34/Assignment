# Style Transfer Agent

A minimal pipeline that extracts a colour "fingerprint" from reference imagery and applies it to other images.

## Features

- Uses OpenCLIP (ViT-B/32) to embed reference images and record colour statistics.
- Filmulator-inspired processing engine with tone controls (shadows, highlights, contrast), clarity, grain, colour temperature, and geometry transforms.
- Simple CLI for batch processing or interactive refinement backed by a local LLM.
- Optional faster-whisper transcription so you can dictate adjustments and have them converted into feedback in real time.

## Setup

```bash
python -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
```

If you only need the voice transcription helper, install `faster-whisper` directly (the GUI voice mode also relies on `sounddevice` for microphone access and `webrtcvad` for end-of-speech detection):

```bash
pip install faster-whisper
pip install sounddevice
pip install webrtcvad
```

## Usage

1. Place reference images in `data/references/`.
2. Place input images to be stylised in `data/inputs/`.
3. Run the agent:
   ```bash
   source venv/bin/activate
   python scripts/run_agent.py
   ```
4. Styled outputs appear in `outputs/styled/`.

Enable human-in-the-loop refinement with natural-language feedback and a local LLM:

```bash
python scripts/run_agent.py --interactive
```
After the first pass you can reply with phrases such as “make it more like the reference”, “less saturated”, “give it a sunset vibe”, or “add some grain” to iteratively tune the outputs. The feedback is sent to a local Ollama model first, then backed up by keyword hints.

When `faster-whisper` is installed you can also dictate feedback by recording a short clip and pointing the prompt at it:

```text
:voice data/snippets/adjustments.m4a
```

The PyQt interface includes a **Voice Mode** toggle. When enabled it keeps a lightweight listener running in the background; as soon as you speak a command (e.g. “add grain and cool it down”) the GUI applies the detected feedback to the selected photo automatically.

Prefer a graphical workflow? Launch the PyQt interface:

```bash
python scripts/run_qt.py
```

The GUI lists your input images, shows original vs. stylised previews, and lets you submit the same free-form feedback directly from the window.

### Optional: Local LLM for richer feedback

For open-ended instructions (e.g. “make this one a little brighter and rotate it 90 degrees”), install [Ollama](https://ollama.com/) and pull a compact model such as Llama 3.1 8B:

```bash
brew install ollama  # or follow Ollama’s installation guide
ollama pull llama3.1:8b
```

With Ollama running, the interactive loop will automatically invoke the local model whenever keywords are not recognised, translating the feedback into structured editing instructions.

You can specify alternative directories:

```bash
python scripts/run_agent.py --references path/to/refs --inputs path/to/photos --outputs path/to/output
```

## Testing

```bash
source venv/bin/activate
python -m pytest
```
