# Arabic Official Documents OCR

Free OCR models for printed Arabic official documents: laws, decrees, circulars, government letters and
regulations. They are fine-tuned on Saudi government documents and are strongest on numbers and dates written in
Arabic-Indic digits (٠١٢٣٤٥٦٧٨٩), where the stock Arabic model is weakest.

| File | Engine | Size |
|---|---|---|
| `ara_official.traineddata` | [Tesseract](https://github.com/tesseract-ocr/tesseract) 4 or 5 (LSTM) | 13 MB |
| `ara_official_kraken.safetensors` | [Kraken](https://kraken.re) 7 | 16 MB |

## Use

**Tesseract.** Put the file in your tessdata folder (or point `TESSDATA_PREFIX` at the folder that holds it):

```
tesseract page.png out -l ara_official --psm 4
```

It works anywhere Tesseract does, for example `ocrmypdf -l ara_official scan.pdf out.pdf`. Scan or render pages at
300 dpi.

**Kraken.**

```
kraken -i page.png out.txt segment -bl ocr -m ara_official_kraken.safetensors --base-dir R
```

The Tesseract model is the main one. The Kraken model reads letters and circulars well and, because it is a
different engine design, makes different mistakes. Running both and checking where they disagree is a good way to
find numbers that need a second look. It does poorly on slide-style pages.

## Results

Correct words and numbers found on held-out pages (documents never used in training), compared with Tesseract's
stock `ara` model from `tessdata_best`:

| Test set | Stock words | Our words | Stock numbers | Our numbers |
|---|---|---|---|---|
| Saudi National Archives scans (old typewriter and print) | 47,164 | 51,175 | 120 | 629 |
| Saudi Bureau of Experts law scans | 90,646 | 97,529 | 376 | 1,389 |

Non-Saudi documents (a small check: 49 pages; "scan-like" = the same pages blurred, noised and slightly rotated):

| Source | Pages | Stock words | Our words | Stock numbers | Our numbers | In the original |
|---|---|---|---|---|---|---|
| Central Bank of Kuwait circulars, clean | 13 | 2,305 | 2,313 | 38 | 169 | 2,388 words, 207 numbers |
| Central Bank of Kuwait circulars, scan-like | 13 | 2,220 | 2,234 | 44 | 135 | 2,388 words, 207 numbers |
| UN Arabic resolutions, clean | 22 | 7,445 | 7,547 | 171 | 185 | 7,632 words, 219 numbers |
| UN Arabic resolutions, scan-like | 22 | 6,744 | 7,246 | 175 | 164 | 7,632 words, 219 numbers |
| Central Bank of Oman presentation, clean | 14 | 492 | 502 | 10 | 9 | 598 words, 14 numbers |

## Limits

- Printed text only: no handwriting, and decorative or calligraphic fonts are not covered.
- Tables come out as plain lines of text.
- On old or noisy scans some numbers are still misread (٣ and ٢ are the most common confusion). For anything where
  a number matters, check it against the original page.
- Arabic only: English text on a page is read poorly.

## How they were made

- `ara_official.traineddata`: fine-tuned from Tesseract's `tessdata_best` `ara` model on text lines cut from
  Saudi official documents (publicly published laws, regulations and circulars, with their published text as the
  labels) plus synthetic pages of numbers and dates.
- `ara_official_kraken.safetensors`: fine-tuned from the
  [OpenITI printed Arabic base model](https://zenodo.org/records/7050296) (CC0) on the same kind of lines.

## License

Apache License 2.0 (see `LICENSE`). Free to use, including commercially; keep the license notice.

SHA-256:
```
d8a2a43201ce85213c3855b1801bba4a8da95747d40c2fa6f22f4fb07273ad5d  ara_official.traineddata
853224c53dc20c861a3cc8a5bc1f59076050bcedf4fcb66195043fc38c5949aa  ara_official_kraken.safetensors
```
