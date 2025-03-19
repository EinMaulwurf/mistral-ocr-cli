#!/usr/bin/env python3
#
# mistral-ocr
#
# Dependencies: pip install mistralai markdown2 latex2mathml
# License: GPLv3
#
# Copyright (c) 2025 Aurelien Aptel <aurelien.aptel@gmail.com>
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program. If not, see <https://www.gnu.org/licenses/>. 
#

from mistralai import Mistral
import os
import json
import sys
from pathlib import Path
import argparse
import markdown2
import re
import html as Html

TEMPLATE = """<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{title}</title>
</head>
<body>
    {html}
</body>
</html>
"""

def warn(s):
    print(s, file=sys.stderr)

def main():
    ap = argparse.ArgumentParser(description="convert images/pdf to text via Mistral AI. Output is printed to stdout.")
    ap.add_argument("-k", "--api-key", help="Mistral API key")
    ap.add_argument("-f", "--format", choices=["html", "markdown"], default="html", help="output format, default is html")
    ap.add_argument("--no-images", action="store_true", help="dont include images in html output")    
    ap.add_argument("filename")
    args = ap.parse_args()

    key = os.environ.get("MISTRAL_API_KEY", "")
    if args.api_key:
        key = args.api_key

    if not key:
        warn("No API key given. Use -k or set MISTRAL_API_KEY env var")
        exit(1)

    client = Mistral(api_key=key)

    ocr_file = Path(args.filename)
    if not ocr_file.is_file():
        warn(f"<{ocr_file}> is not a file")
        exit(1)

    warn("uploading...")

    uploaded_file = client.files.upload(
            file={
                "file_name": ocr_file.stem,
                "content": ocr_file.read_bytes(),
            },
            purpose="ocr",
    )
    
    warn("ocr request...")
    
    signed_url = client.files.get_signed_url(file_id=uploaded_file.id, expiry=1)
    
    pdf_response = client.ocr.process(document={
        "document_url": signed_url.url,
        "type": "document_url",
        "document_name": ocr_file.stem,
    }, model="mistral-ocr-latest", include_image_base64=not args.no_images)
    
    warn("dumping...")
    
    rsp = json.loads(pdf_response.model_dump_json())
    if args.format == "markdown":
        for p in rsp["pages"]:
            print(p["markdown"])
    elif args.format == "html":
        html = ""
        
        for p in rsp["pages"]:
            imgs = {}
            for img in p.get("images", []):
                imgs[img["id"]] = img.get("image_base64", "")
        
            phtml = markdown2.markdown(p["markdown"], extras=["tables", "footnotes", "latex"])
            phtml = re.sub('src="(.+?)"', lambda m: 'src="%s"'%imgs.get(m.group(1), ""), phtml)
            html += phtml + "\n<hr/>\n"
            
        print(TEMPLATE.format(html=html, title=Html.escape(ocr_file.stem)))
    
    warn("done")

if __name__ == '__main__':
    main()
