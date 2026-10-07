# faster-whisper

- **ES:** Whisper reimplementado con CTranslate2 (hasta 4× más rápido, menos memoria); ideal en CPU.
- **PT:** Whisper reimplementado com CTranslate2 (até 4× mais rápido, menos memória); ideal em CPU.
- **Paquete:** `faster-whisper` (`pip install faster-whisper`)
- **Modelo:** `large-v3` (también `tiny`, `base`, `small`, `medium`, `large-v2`)
- **API:** `from faster_whisper import WhisperModel`; `WhisperModel("large-v3", device="cpu", compute_type="int8")` → `model.transcribe("audio.mp3", language="es"/"pt")`
- **Licencia:** MIT
