# pyvideotrans

- **ES:** Traduce videos completos entre idiomas y genera doblaje + subtítulos (GUI, CLI, WebUI).
- **PT:** Traduz vídeos inteiros entre idiomas e gera dublagem + legendas (GUI, CLI, WebUI).
- **Instalación:** `git clone https://github.com/jianchang512/pyvideotrans.git` → `uv sync` (requiere `uv`)
- **GUI:** `uv run sp.py` · **Web:** `uv run webui.py`
- **CLI:** `uv run cli.py --task vtv --name "./video.mp4" --source_language_code en --target_language_code es`
- **Otras tareas:** `--task stt` (audio→subtítulo), `--task sts` (traducir subtítulo), `--task tts` (texto→voz)
- **Licencia:** open-source (ver repo)
