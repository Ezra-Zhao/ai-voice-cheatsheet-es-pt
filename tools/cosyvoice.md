# CosyVoice (FunAudioLLM)

- **ES:** TTS multilingüe con clonación zero-shot, modo cross-lingual y control por instrucciones.
- **PT:** TTS multilíngue com clonagem zero-shot, modo cross-lingual e controle por instruções.
- **Instalación:** no está en PyPI → `git clone --recursive https://github.com/FunAudioLLM/CosyVoice.git` + `pip install -r requirements.txt`
- **Modelo verificado (Hugging Face):** `FunAudioLLM/CosyVoice2-0.5B`
- **API:** `from cosyvoice.cli.cosyvoice import CosyVoice`; `CosyVoice('pretrained_models/CosyVoice2-0.5B')` → `inference_zero_shot(tts_text, prompt_text, prompt_wav)`
- **Web:** `python webui.py --port 50000 --model_dir pretrained_models/CosyVoice2-0.5B`
- **Licencia:** Apache-2.0 (código)
