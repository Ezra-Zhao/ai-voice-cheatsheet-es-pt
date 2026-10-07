# 🎙️ Guía rápida: voces e IA de voz locales (ES · PT)

Una sola página con comandos listos para usar: **síntesis de voz, clonación, transcripción y doblaje de video** — todo 100 % local y gratuito.

**Autor:** Ezra-Zhao · Octubre 2026

---

## 🇪🇸 Español

### 0. Preparación

```bash
python -m venv .venv && source .venv/bin/activate   # Linux / macOS
pip install --upgrade pip
```

- CPU basta para: Piper, Kokoro, faster-whisper (int8), whisper.cpp.
- GPU recomendada para: XTTS-v2, F5-TTS, Qwen3-TTS, CosyVoice.

### 1. Síntesis de voz (TTS local)

#### Piper TTS

| | |
|---|---|
| **Para qué** | Motor neuronal rapidísimo que corre en CPU; decenas de idiomas con voces `.onnx` ligeras (español y portugués incluidos). |
| **Instalación** | `pip install piper-tts` |

```bash
python -m piper.download_voices es_MX-claude-high
echo "Hola, esto es una prueba de voz local." | \
  piper --model es_MX-claude-high --output_file hola.wav
```

> `python -m piper.download_voices` sin argumentos lista todas las voces disponibles.

#### Kokoro

| | |
|---|---|
| **Para qué** | Modelo TTS compacto y de alta calidad, con CLI sencilla y varias voces. |
| **Instalación** | `pip install kokoro-tts` |

```bash
wget https://github.com/nazdridoy/kokoro-tts/releases/download/v1.0.0/kokoro-v1.0.onnx
wget https://github.com/nazdridoy/kokoro-tts/releases/download/v1.0.0/voices-v1.0.bin
kokoro-tts "Hola mundo, esto es Kokoro." --output hola.mp3
```

#### Qwen3-TTS (Alibaba)

| | |
|---|---|
| **Para qué** | TTS multilingüe (10 idiomas, incl. español y portugués) con clonación de voz en 3 segundos y control de emoción. Licencia Apache-2.0. |
| **Instalación** | `pip install -U qwen-tts` |

```python
import torch
import soundfile as sf
from qwen_tts import Qwen3TTSModel

model = Qwen3TTSModel.from_pretrained(
    "Qwen/Qwen3-TTS-12Hz-0.6B-Base",
    device_map="cuda:0", dtype=torch.bfloat16)

wavs, sr = model.generate_voice_clone(
    text="Hola, esta es una prueba de clonación de voz.",
    language="Spanish",
    ref_audio="mi_voz.wav",
    ref_text="Transcripción del audio de referencia.")
sf.write("salida.wav", wavs[0], sr)
```

> Con voces predefinidas: modelo `Qwen/Qwen3-TTS-12Hz-0.6B-CustomVoice` y método `generate_custom_voice()`.

#### Coqui XTTS-v2

| | |
|---|---|
| **Para qué** | TTS multilingüe (17 idiomas) con clonación de voz a partir de ~6 segundos de audio. |
| **Instalación** | `pip install TTS` |

```python
import os
os.environ["COQUI_TOS_AGREED"] = "1"   # acepta la licencia sin pregunta interactiva
from TTS.api import TTS

tts = TTS("tts_models/multilingual/multi-dataset/xtts_v2", gpu=False)
tts.tts_to_file(
    text="Hola, hablo con mi voz clonada.",
    speaker_wav="mi_voz.wav",
    language="es",
    file_path="salida.wav")
```

> ⚠️ Requiere aceptar la licencia CPML de Coqui (investigación / uso no comercial). El modelo (~2 GB) se descarga solo la primera vez.

### 2. Clonación de voz

#### F5-TTS

| | |
|---|---|
| **Para qué** | Clonación de voz de alta fidelidad con muy poco audio de referencia; incluye CLI y app web (Gradio). |
| **Instalación** | `pip install f5-tts` |

```bash
f5-tts_infer-cli --model "F5-TTS" \
  --ref_audio "mi_voz.wav" \
  --ref_text "Texto que se dice en el audio de referencia." \
  --gen_text "Hola, ahora hablo con la voz clonada."
```

> Interfaz web opcional: `f5-tts_infer-gradio`

#### CosyVoice (FunAudioLLM)

| | |
|---|---|
| **Para qué** | TTS multilingüe con clonación zero-shot, modo cross-lingual (voz de un idioma, texto de otro) y control por instrucciones. |
| **Instalación** | No está en PyPI; se clona el repositorio. |

```bash
git clone --recursive https://github.com/FunAudioLLM/CosyVoice.git
cd CosyVoice
pip install -r requirements.txt
python -c "from huggingface_hub import snapshot_download; \
  snapshot_download('FunAudioLLM/CosyVoice2-0.5B', \
  local_dir='pretrained_models/CosyVoice2-0.5B')"
```

```python
import torchaudio
from cosyvoice.cli.cosyvoice import CosyVoice

cv = CosyVoice('pretrained_models/CosyVoice2-0.5B')
for r in cv.inference_zero_shot(
        "Hola, esta es una prueba de CosyVoice.",
        "Texto del audio de referencia",
        "mi_voz.wav"):
    torchaudio.save("salida.wav", r['tts_speech'], cv.sample_rate)
```

> Interfaz web opcional: `python webui.py --port 50000 --model_dir pretrained_models/CosyVoice2-0.5B`

### 3. Voz a texto (ASR)

#### faster-whisper

| | |
|---|---|
| **Para qué** | Reimplementación de Whisper hasta 4× más rápida y con menos memoria; ideal en CPU. |
| **Instalación** | `pip install faster-whisper` |

```python
from faster_whisper import WhisperModel

model = WhisperModel("large-v3", device="cpu", compute_type="int8")
segments, info = model.transcribe("audio.mp3", language="es")
for s in segments:
    print(f"[{s.start:.1f}s -> {s.end:.1f}s] {s.text}")
```

> En GPU: `device="cuda", compute_type="float16"`. Para portugués: `language="pt"`.

#### whisper.cpp

| | |
|---|---|
| **Para qué** | Whisper en C/C++ puro: rapidísimo en CPU, sin dependencias pesadas de Python. |
| **Instalación** | Se compila desde el código fuente. |

```bash
git clone https://github.com/ggml-org/whisper.cpp.git
cd whisper.cpp
cmake -B build && cmake --build build --config Release -j
sh ./models/download-ggml-model.sh large-v3
./build/bin/whisper-cli -m models/ggml-large-v3.bin -l es -f ../audio.wav
```

> `-l es` / `-l pt` fija el idioma. Para GPU NVIDIA, añade `-DGGML_CUDA=1` al primer comando `cmake`.

### 4. Doblaje de video y subtítulos

#### pyvideotrans

| | |
|---|---|
| **Para qué** | Traduce un video completo de un idioma a otro y genera doblaje + subtítulos (GUI, CLI y WebUI). |
| **Instalación** | Requiere `uv`. |

```bash
git clone https://github.com/jianchang512/pyvideotrans.git
cd pyvideotrans
uv sync
uv run sp.py                     # interfaz gráfica
uv run cli.py --task vtv --name "./video.mp4" \
  --source_language_code en --target_language_code es   # doblaje por CLI
```

#### VoiceStudio

| | |
|---|---|
| **Para qué** | App de escritorio open-source (AGPL-3.0): clonación de voz, doblaje de video, dictado y audiolibros, todo local. |
| **Instalación** | Descarga el paquete de tu plataforma en la página de releases, o compila desde el código: |

```bash
git clone https://github.com/debpalash/VoiceStudio.git
cd VoiceStudio
bun install
bun run desktop
```

> Flujo en la app: abre VoiceStudio → Voice Cloning → sigue el asistente de doblaje de video.

### Notas

- Los modelos grandes (XTTS-v2, F5-TTS, Qwen3-TTS, CosyVoice) rinden mucho mejor con GPU NVIDIA.
- XTTS-v2 exige aceptar la licencia CPML de Coqui (investigación / uso no comercial).
- Los comandos se verificaron contra la documentación pública de cada proyecto (octubre 2026).

---

## 🇵🇹 Português

### 0. Preparação

```bash
python -m venv .venv && source .venv/bin/activate   # Linux / macOS
pip install --upgrade pip
```

- CPU é suficiente para: Piper, Kokoro, faster-whisper (int8), whisper.cpp.
- GPU recomendada para: XTTS-v2, F5-TTS, Qwen3-TTS, CosyVoice.

### 1. Síntese de voz (TTS local)

#### Piper TTS

| | |
|---|---|
| **Para quê** | Motor neural rapidíssimo que roda em CPU; dezenas de idiomas com vozes `.onnx` leves (espanhol e português incluídos). |
| **Instalação** | `pip install piper-tts` |

```bash
python -m piper.download_voices pt_BR-faber-medium
echo "Olá, isto é um teste de voz local." | \
  piper --model pt_BR-faber-medium --output_file ola.wav
```

> `python -m piper.download_voices` sem argumentos lista todas as vozes disponíveis.

#### Kokoro

| | |
|---|---|
| **Para quê** | Modelo TTS compacto e de alta qualidade, com CLI simples e várias vozes. |
| **Instalação** | `pip install kokoro-tts` |

```bash
wget https://github.com/nazdridoy/kokoro-tts/releases/download/v1.0.0/kokoro-v1.0.onnx
wget https://github.com/nazdridoy/kokoro-tts/releases/download/v1.0.0/voices-v1.0.bin
kokoro-tts "Olá mundo, isto é o Kokoro." --output ola.mp3
```

#### Qwen3-TTS (Alibaba)

| | |
|---|---|
| **Para quê** | TTS multilíngue (10 idiomas, incl. espanhol e português) com clonagem de voz em 3 segundos e controle de emoção. Licença Apache-2.0. |
| **Instalação** | `pip install -U qwen-tts` |

```python
import torch
import soundfile as sf
from qwen_tts import Qwen3TTSModel

model = Qwen3TTSModel.from_pretrained(
    "Qwen/Qwen3-TTS-12Hz-0.6B-Base",
    device_map="cuda:0", dtype=torch.bfloat16)

wavs, sr = model.generate_voice_clone(
    text="Olá, este é um teste de clonagem de voz.",
    language="Portuguese",
    ref_audio="minha_voz.wav",
    ref_text="Transcrição do áudio de referência.")
sf.write("saida.wav", wavs[0], sr)
```

> Com vozes predefinidas: modelo `Qwen/Qwen3-TTS-12Hz-0.6B-CustomVoice` e método `generate_custom_voice()`.

#### Coqui XTTS-v2

| | |
|---|---|
| **Para quê** | TTS multilíngue (17 idiomas) com clonagem de voz a partir de ~6 segundos de áudio. |
| **Instalação** | `pip install TTS` |

```python
import os
os.environ["COQUI_TOS_AGREED"] = "1"   # aceita a licença sem pergunta interativa
from TTS.api import TTS

tts = TTS("tts_models/multilingual/multi-dataset/xtts_v2", gpu=False)
tts.tts_to_file(
    text="Olá, falo com a minha voz clonada.",
    speaker_wav="minha_voz.wav",
    language="pt",
    file_path="saida.wav")
```

> ⚠️ Exige aceitar a licença CPML da Coqui (pesquisa / uso não comercial). O modelo (~2 GB) baixa sozinho na primeira execução.

### 2. Clonagem de voz

#### F5-TTS

| | |
|---|---|
| **Para quê** | Clonagem de voz de alta fidelidade com pouquíssimo áudio de referência; inclui CLI e app web (Gradio). |
| **Instalação** | `pip install f5-tts` |

```bash
f5-tts_infer-cli --model "F5-TTS" \
  --ref_audio "minha_voz.wav" \
  --ref_text "Texto dito no áudio de referência." \
  --gen_text "Olá, agora falo com a voz clonada."
```

> Interface web opcional: `f5-tts_infer-gradio`

#### CosyVoice (FunAudioLLM)

| | |
|---|---|
| **Para quê** | TTS multilíngue com clonagem zero-shot, modo cross-lingual (voz de um idioma, texto de outro) e controle por instruções. |
| **Instalação** | Não está no PyPI; clona-se o repositório. |

```bash
git clone --recursive https://github.com/FunAudioLLM/CosyVoice.git
cd CosyVoice
pip install -r requirements.txt
python -c "from huggingface_hub import snapshot_download; \
  snapshot_download('FunAudioLLM/CosyVoice2-0.5B', \
  local_dir='pretrained_models/CosyVoice2-0.5B')"
```

```python
import torchaudio
from cosyvoice.cli.cosyvoice import CosyVoice

cv = CosyVoice('pretrained_models/CosyVoice2-0.5B')
for r in cv.inference_zero_shot(
        "Olá, este é um teste do CosyVoice.",
        "Texto do áudio de referência",
        "minha_voz.wav"):
    torchaudio.save("saida.wav", r['tts_speech'], cv.sample_rate)
```

> Interface web opcional: `python webui.py --port 50000 --model_dir pretrained_models/CosyVoice2-0.5B`

### 3. Voz para texto (ASR)

#### faster-whisper

| | |
|---|---|
| **Para quê** | Reimplementação do Whisper até 4× mais rápida e com menos memória; ideal em CPU. |
| **Instalação** | `pip install faster-whisper` |

```python
from faster_whisper import WhisperModel

model = WhisperModel("large-v3", device="cpu", compute_type="int8")
segments, info = model.transcribe("audio.mp3", language="pt")
for s in segments:
    print(f"[{s.start:.1f}s -> {s.end:.1f}s] {s.text}")
```

> Em GPU: `device="cuda", compute_type="float16"`. Para espanhol: `language="es"`.

#### whisper.cpp

| | |
|---|---|
| **Para quê** | Whisper em C/C++ puro: rapidíssimo em CPU, sem dependências pesadas de Python. |
| **Instalação** | Compila-se a partir do código-fonte. |

```bash
git clone https://github.com/ggml-org/whisper.cpp.git
cd whisper.cpp
cmake -B build && cmake --build build --config Release -j
sh ./models/download-ggml-model.sh large-v3
./build/bin/whisper-cli -m models/ggml-large-v3.bin -l pt -f ../audio.wav
```

> `-l pt` / `-l es` fixa o idioma. Para GPU NVIDIA, adicione `-DGGML_CUDA=1` ao primeiro comando `cmake`.

### 4. Dublagem de vídeo e legendas

#### pyvideotrans

| | |
|---|---|
| **Para quê** | Traduz um vídeo inteiro de um idioma para outro e gera dublagem + legendas (GUI, CLI e WebUI). |
| **Instalação** | Requer `uv`. |

```bash
git clone https://github.com/jianchang512/pyvideotrans.git
cd pyvideotrans
uv sync
uv run sp.py                     # interface gráfica
uv run cli.py --task vtv --name "./video.mp4" \
  --source_language_code en --target_language_code pt   # dublagem via CLI
```

#### VoiceStudio

| | |
|---|---|
| **Para quê** | App de desktop open-source (AGPL-3.0): clonagem de voz, dublagem de vídeo, ditado e audiolivros, tudo local. |
| **Instalação** | Baixe o pacote da sua plataforma na página de releases, ou compile a partir do código: |

```bash
git clone https://github.com/debpalash/VoiceStudio.git
cd VoiceStudio
bun install
bun run desktop
```

> Fluxo no app: abra o VoiceStudio → Voice Cloning → siga o assistente de dublagem de vídeo.

### Notas

- Os modelos grandes (XTTS-v2, F5-TTS, Qwen3-TTS, CosyVoice) rendem muito melhor com GPU NVIDIA.
- O XTTS-v2 exige aceitar a licença CPML da Coqui (pesquisa / uso não comercial).
- Os comandos foram verificados contra a documentação pública de cada projeto (outubro 2026).

---

## 🇬🇧 English summary

One-page cheat sheet of **free, local** voice/AI tools, fully in Spanish and Portuguese above. Covered:

| Category | Tools |
|---|---|
| Local TTS | Piper TTS (`piper-tts`), Kokoro (`kokoro-tts`), Qwen3-TTS (`qwen-tts`, models `Qwen/Qwen3-TTS-12Hz-0.6B-Base` / `-CustomVoice`), Coqui XTTS-v2 (`TTS`, `tts_models/multilingual/multi-dataset/xtts_v2`) |
| Voice cloning | F5-TTS (`f5-tts`), CosyVoice (repo clone, model `FunAudioLLM/CosyVoice2-0.5B`) |
| Speech-to-text | faster-whisper (`faster-whisper`, `large-v3`), whisper.cpp (build from source, `ggml-large-v3.bin`) |
| Video dubbing / subtitles | pyvideotrans (repo clone, `uv run cli.py --task vtv`), VoiceStudio (AGPL-3.0 desktop app) |

Each entry: one-line purpose, install command, minimal runnable example. GPU recommended for the large models; Piper, Kokoro, faster-whisper and whisper.cpp run fine on CPU. Note: Coqui XTTS-v2 requires accepting the CPML license (non-commercial use).

**Author:** Ezra-Zhao · October 2026 · MIT License
