# Qwen3-TTS (Alibaba)

- **ES:** TTS multilingüe (10 idiomas, incl. ES/PT); clonación en 3 s, diseño de voces y control de emoción.
- **PT:** TTS multilíngue (10 idiomas, incl. ES/PT); clonagem em 3 s, design de vozes e controle de emoção.
- **Paquete:** `qwen-tts` (`pip install -U qwen-tts`)
- **Modelos verificados (Hugging Face):** `Qwen/Qwen3-TTS-12Hz-0.6B-Base`, `Qwen/Qwen3-TTS-12Hz-0.6B-CustomVoice`, `Qwen/Qwen3-TTS-12Hz-1.7B-CustomVoice`
- **API:** `Qwen3TTSModel.from_pretrained(...)` → `generate_voice_clone()` / `generate_custom_voice()` / `generate_voice_design()`
- **Licencia:** Apache-2.0
