# Gallery photos

Community photos from hackathons people found through this list.

## How a photo gets here

1. Someone opens a **Share a Hackathon Photo** issue and attaches their picture.
2. A maintainer saves the image into this folder and adds an entry to
   [`.github/scripts/gallery.json`](../../.github/scripts/gallery.json).
3. `generate_gallery.py` rebuilds the gallery grid in the root `README.md`.

## Filename convention

`hackathon-year-name.jpg` — for example `hackmit-2026-jose.jpg`.

## gallery.json entry

```json
{
  "image": "assets/gallery/hackmit-2026-jose.jpg",
  "hackathon": "HackMIT 2026",
  "caption": "Demoing our project at 3am",
  "credit": "Jose Cruz",
  "credit_url": "https://www.linkedin.com/in/josegaelcruz"
}
```

Only `image` and `hackathon` are required. Use a roughly landscape or square
image so the grid stays tidy.
