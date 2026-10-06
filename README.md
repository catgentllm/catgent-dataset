<p align="center"><img src="assets/banner.png" alt="catgent LLM" width="100%"></p>
<h1 align="center">catgent LLM cat sound dataset</h1>
<p align="center">Little Language Model for cats</p>
<p align="center"><a href="https://catgent.app">catgent.app</a> · <a href="https://catgent.app/translate">Translate your cat</a> · <a href="https://catgent.app/lexicon">Lexicon</a> · <a href="https://catgent.app/dataset">Live numbers</a></p>

Cat vocalisations collected live by catgents, the AI agents of catgent LLM: each agent watches cat videos in its
own browser, finds cat sounds, looks at the frames around each sound and labels the situation the cat is in. Accepted clips passed quality
checks (sound confidence, situation confidence, a cat on screen, not a duplicate). Everything here comes from real
agent work, nothing is synthetic.

Honest scope: cats do not have words like people. The translator predicts which situation a sound is associated with
(8 situations) and reports its confidence. It is a small convolutional network trained from scratch on this data,
not a large language model: LLM here stands for Little Language Model.

## How clips arrive

Every catgent has its own branch `catgent/<name>`. Each accepted clip is one commit on that branch, authored by the
catgent, with trailers that name the catgent, the model that labelled it, the source with its time code and the
situation. Every few minutes the branches are merged into `main` with `git merge --no-ff` after automatic checks
(schema, duplicates against main, clips rejected by an operator are removed with a commit that says why). The network
graph of this repo is the history of who found what.

## Numbers (updated on every merge)

* accepted clips: **0** from **0** videos
* clips with raw audio in this repo (open licences only): **0**
* by situation: none yet
* by sound type: none yet
* by site: none yet

## Models (trained from scratch on this data)

| version | trained | clips | held out test | accuracy | baseline (majority class) |
|---|---|---|---|---|---|
| no model yet | | | | | |

The test set is split **by source video**: no clip from a test video is seen during training. Baseline is the
accuracy of always answering the most common training situation. Small test sets make accuracy noisy.

## Layout

* `clips.jsonl`: every merged clip, one per line, rebuilt on each merge from `clips/<catgent>.jsonl`.
* `clips/<catgent>.jsonl`: the clips of one catgent, written only on its branch.
* each clip line: Source url, site, title, licence, time span, sound type and confidence,
  situation, confidence, the labelling model and its reason, fingerprint, paths to the files below.
* `spectrograms/<localId>.png`: mel spectrogram of the clip.
* `features/<localId>.npy`: the model input, log mel 64 x 200 (2 s, sr 32000, n_fft 1024, hop 320, 50 to 8000 Hz, dB), float16.
* `features/<localId>.emb.npy`: PANNs Cnn14 embedding (2048, float16), used for dedup and the lexicon.
* `audio/<localId>.ogg`: raw audio, **only** for sources with an open licence (CC0, CC BY, CC BY SA, public domain).
* `words.json`: the lexicon. Sound clusters ("words") with their situation distribution.
* `models/vX/model.pt` + `metrics.json`: weights, accuracy, baseline, per class numbers, confusion matrix.
* `assets/`: banner and logo.

## Licences

Raw audio is published only when the source licence allows it; the licence of each clip is in `clips.jsonl`. For
YouTube, Reddit, Dailymotion and items without a licence we publish only the link, time span, derived features and
labels. Shared tools: YOLO11n (cats on frames), PANNs Cnn14 (cat sound detection). The situation is labelled by the
catgent's own model (Claude, GPT, Gemini, Grok, Qwen or DeepSeek), recorded per clip as `labelerModel`.
