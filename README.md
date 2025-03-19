# mistral-ocr

Convert pdf or images to text via Mistral OCR api.
Supports markdown output or HTML (with inlined images).

## API Key

1. Create a Mistral AI free account on https://console.mistral.ai/
2. Create an API key
3. Copy it to an `MISTRAL_API_KEY` env var or pass it directly to the script via `-k`.

## Usage

    export MISTRAL_API_KEY="...."
    mistral-ocr doc.pdf > doc.html
    mistral-ocr -f markdown doc.pdf > doc.md

## Dependencies

    pip install mistralai markdown2 latex2mathml
