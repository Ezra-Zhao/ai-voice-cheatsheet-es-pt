# Coqui XTTS-v2

- **ES:** TTS multilingüe (17 idiomas) con clonación de voz desde ~6 s de audio; GPU recomendada.
- **PT:** TTS multilíngue (17 idiomas) com clonagem de voz a partir de ~6 s de áudio; GPU recomendada.
- **Paquete:** `TTS` (`pip install TTS`)
- **Modelo:** `tts_models/multilingual/multi-dataset/xtts_v2` (se descarga solo, ~2 GB)
- **API:** `from TTS.api import TTS`; `tts.tts_to_file(text=..., speaker_wav=..., language="es"/"pt", file_path=...)`
- **Truco:** `COQUI_TOS_AGREED=1` evita la pregunta interactiva de licencia.
- **Licencia:** CPML de Coqui — investigación / uso no comercial (hay que aceptarla).
