# Barberotheque

Group project for the Semantic Digital Libraries 2025/26 course.

A digital library of Alessandro Barbero's lectures in audio and text form.

## Process

### Manual search
Searched on YouTube some videos of Alessandro Barbero's lectures and compiled a
csv with basic metadata of each video.

### Audio download
Downloaded the audio of each video using `yt-dlp` into M4A format.

```bash
tail -n +2 data/barbero.csv | cut -d';' -f1 > barbero.txt

yt-dlp --format bestaudio[ext=m4a] --sleep-interval 30 --limit-rate 5M --extract-audio --audio-format m4a --download-archive barbero.log --batch-file barbero.txt --output "audio/barbero-%(extractor)s-%(id)s.%(ext)s"
```

### Semantic renaming
Compiled the `data/barbero.csv` with the reasoned semantic filenames, adding the
column `semantic_filename` and filling it manually.
Then renamed all files in the `compressed/` folder to their semantic filenames
using the script `scripts/rename_files.py`.

### Whisper transcription
Transcribed each audio file using OpenAI Whisper in Italian language using the
`turbo` model.

```bash
for f in audio/*.m4a; do
  whisper "$f" --model turbo --language it --task transcribe --output_format all --output_dir transcripts
done
```
## Disclaimer

This repository and all files contained within are used solely for educational
purposes as part of a university project.  
No copyright infringement is intended. All media, texts, and materials remain
the property of their respective owners and are included or referenced here only
for academic, non-commercial use.

## Team members
- [Tommaso Barbato](https://github.com/epistrephein)
- [Martina Uccheddu](https://github.com/martinaucch)
