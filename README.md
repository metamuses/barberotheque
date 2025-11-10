# Barberotheque

Group project for the Semantic Digital Libraries 2025/26 course.

A digital library of Alessandro Barbero's lectures in audio and text form.

## Process

### Manual search
We searched on YouTube  videos of Alessandro Barbero's lectures and compiled
the [`barbero.csv`](metadata/barbero.csv) file with basic metadata of each
chosen source.

### Audio download
We downloaded the audio-only version of each video using `yt-dlp` into the
[`audio`](audio/) folder as M4A files.

```shell
# extract all youtube URLs into a batch file
tail -n +2 metadata/barbero.csv | cut -d',' -f1 > .yt-dlp/barbero.lst

# download m4a audio files with yt-dlp
yt-dlp \
  --format bestaudio[ext=m4a] \
  --extract-audio --audio-format m4a \
  --sleep-interval 30 --limit-rate 5M \
  --batch-file .yt-dlp/barbero.lst --download-archive .yt-dlp/barbero.log \
  --output "audio/barbero-%(extractor)s-%(id)s.%(ext)s"
```

We decided to use M4A format with AAC compression, as it provides better audio
fidelity at smaller file sizes, which is beneficial for accurate transcription
with Whisper, compared to MP3 which introduces more compression artifacts that
could hurt transcription accuracy.

### Semantic renaming
We compiled the [`barbero.csv`](metadata/barbero.csv) with the reasoned semantic
filenames, adding the column `semantic_filename` and filling it manually.  
Then we mass-renamed all files in the [`audio`](audio/) folder to their semantic
version using the script [`rename_files.py`](scripts/rename_files.py).

```shell
python scripts/rename_files.py
```

### Whisper transcription
We transcribed each audio file using OpenAI Whisper in Italian language using
the `turbo` model. When it failed on some files, we retried with the `small` model.

```shell
for file in audio/*.m4a; do
  whisper "$file" \
    --model turbo \
    --language it \
    --task transcribe \
    --output_format all \
    --output_dir transcripts/
done
```

### Keywords/entities extraction
We extracted keywords and named entities from each transcript txt file using
SpaCy NLP model for italian (`it_core_news_lg`), saving the results as CSV files
in the `keywords/` and `entities/` folders.

```shell
python scripts/keywords.py
```

### Manual compilation of keywords
We choose manually the list of reasoned keywords for each lecture and we added
them to [`barbero.csv`](metadata/barbero.csv) file in the columns `keywords` and
`entities`.

### Audio compression
We compressed the audio files to reduce their size for easier storage and
handling after the higher fidelity version, used for transcription, was no longer
needed.  
We chose to compress to AAC format with 48kbps bitrate, mono channel, and 22050Hz
sample rate, which provides a good balance between audio quality and file size
for spoken word content like lectures.

```bash
for file in audio/*.m4a; do
  ffmpeg -i "$file" \
    -c:a aac -b:a 48k \
    -ac 1 -ar 22050 \
    "compressed/$(basename "$f")"
done
```

## Disclaimer
This repository and all files contained within are used solely for educational
purposes as part of a university project. No copyright infringement is intended.
All media, texts, and materials remain the property of their respective owners
and are included or referenced here only for academic, non-commercial use.

## Team members
- [Tommaso Barbato](https://github.com/epistrephein)
- [Martina Uccheddu](https://github.com/martinaucch)
