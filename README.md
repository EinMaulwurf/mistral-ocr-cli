# mistral-ocr

Convert pdf or images to text via Mistral OCR api.
Supports markdown output or HTML (with inlined images).

## API Key

1. Create a Mistral AI free account on https://console.mistral.ai/
2. Create an API key
3. Copy it to an `MISTRAL_API_KEY` environment variable or pass it directly to the script via `-k`.

## Usage

We recommend using [uv](https://github.com/astral-sh/uv), a modern command line tool for running python scripts.

```bash
# Set environment variable
export MISTRAL_API_KEY="...."
# set -Ux MISTRAL_API_KEY "...." # fish

# Run the script
uv run mistral_ocr doc.pdf > doc.html
uv run mistral_ocr -f markdown doc.pdf > doc.md
```

uv can also act as a replacement for pipx. This way you can run mistral-ocr without installing it globally and having to deal with dependencies.

```bash
# Package the script
uv tool install .
# Run the script everywhere (note the hyphen instead of underscore)
mistral-ocr doc.pdf > doc.html
```

## Options

- `-f` or `--format`: Output format. Can be `html` or `markdown`. Default is `html`.
- `-k` or `--api-key`: Mistral API key. If not set, it will look for the `MISTRAL_API_KEY` environment variable.
- `--no-images`: Do not include images in the HTML output.

## Dependencies

This script depends on `mistralai` as the main interface to the Mistral API, `markdown2` for converting markdown to HTML and `latex2mathml` for rendering equations.
