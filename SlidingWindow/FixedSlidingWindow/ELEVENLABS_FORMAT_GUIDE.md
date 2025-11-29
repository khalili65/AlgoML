# ElevenLabs Format Guide

## Summary

**ElevenLabs web interface does NOT parse SSML tags** - it reads them as literal text. Use plain text instead.

## Format Options

### ✅ Option 1: Plain Text (Recommended for Web Interface)
- **File**: `pattern_1_elevenlabs_web.txt`
- **Use when**: Copy-pasting into ElevenLabs web interface
- **Features**: 
  - Natural pauses using line breaks and ellipses
  - Visual cues converted to natural speech
  - No tags that will be read aloud

### ✅ Option 2: SSML (Only for API)
- **File**: `pattern_1_elevenlabs.ssml`
- **Use when**: Using ElevenLabs API with:
  - Model: `eleven_turbo_v2`, `eleven_flash_v2`, or `eleven_english_v1`
  - Parameter: `enableSsmlParsing: true`
- **Features**:
  - `<break time="1s"/>` for pauses
  - `<prosody>` for emphasis
  - Requires API setup

### ✅ Option 3: Plain Text (No Visual Cues)
- **File**: `pattern_1_elevenlabs_no_visuals.txt`
- **Use when**: You want clean narration without visual descriptions
- **Features**: All visual cues removed

## How to Use SSML with API

If you want to use SSML via API, you need:

```python
import requests

url = "https://api.elevenlabs.io/v1/text-to-speech/{voice_id}"
headers = {
    "Accept": "audio/mpeg",
    "Content-Type": "application/json",
    "xi-api-key": "YOUR_API_KEY"
}
data = {
    "text": "<speak>Your SSML content here</speak>",
    "model_id": "eleven_turbo_v2",  # Must be SSML-compatible model
    "voice_settings": {
        "stability": 0.5,
        "similarity_boost": 0.5
    }
}
# Note: Check API docs for enableSsmlParsing parameter
```

## Recommendation

**For web interface**: Use `pattern_1_elevenlabs_web.txt` - it's optimized for copy-paste and will work perfectly.

**For API with SSML**: Use `pattern_1_elevenlabs.ssml` but ensure you're using a compatible model and have SSML parsing enabled.

