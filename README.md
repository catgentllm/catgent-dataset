# CATGENT cat sound dataset

Cat vocalisations collected live by CATGENT agents: each agent watches cat videos in its own browser, finds cat
sounds, looks at the frames around each sound and labels the situation the cat is in. Accepted clips passed quality
checks (sound confidence, situation confidence, a cat on screen, not a duplicate). Everything here comes from real
agent work, nothing is synthetic.

Honest scope: cats do not have words like people. The translator predicts which situation a sound is associated with
(8 situations) and reports its confidence.

## Numbers (updated on every push)

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

* `clips.jsonl`: one accepted clip per line. Source url, site, title, licence, time span, sound type and confidence,
  situation, confidence and the labeller's reason, fingerprint, paths to the files below.
* `spectrograms/<localId>.png`: mel spectrogram of the clip.
* `features/<localId>.npy`: the model input, log mel 64 x 200 (2 s, sr 32000, n_fft 1024, hop 320, 50 to 8000 Hz, dB), float16.
* `features/<localId>.emb.npy`: PANNs Cnn14 embedding (2048, float16), used for dedup and the lexicon.
* `audio/<localId>.ogg`: raw audio, **only** for sources with an open licence (CC0, CC BY, CC BY SA, public domain).
* `words.json`: the lexicon. Sound clusters ("words") with their situation distribution.
* `models/vX/model.pt` + `metrics.json`: weights, accuracy, baseline, per class numbers, confusion matrix.

## Licences

Raw audio is published only when the source licence allows it; the licence of each clip is in `clips.jsonl`. For
YouTube, Reddit, Dailymotion and items without a licence we publish only the link, time span, derived features and
labels. Labelling tools: YOLO11n (cats on frames), PANNs Cnn14 (cat sound detection), Claude Haiku 4.5 (situation).
