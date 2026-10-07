# whisper.cpp

- **ES:** Whisper en C/C++ puro; rapidísimo en CPU, sin dependencias pesadas de Python.
- **PT:** Whisper em C/C++ puro; rapidíssimo em CPU, sem dependências pesadas de Python.
- **Instalación:** `git clone https://github.com/ggml-org/whisper.cpp.git` → `cmake -B build && cmake --build build --config Release -j`
- **Modelo:** `sh ./models/download-ggml-model.sh large-v3` → `models/ggml-large-v3.bin`
- **Comando:** `./build/bin/whisper-cli -m models/ggml-large-v3.bin -l es -f audio.wav`
- **GPU NVIDIA:** añadir `-DGGML_CUDA=1` al primer comando `cmake`.
- **Licencia:** MIT
