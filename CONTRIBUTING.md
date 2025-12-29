# Contributing and Workflow

This file contains operational instructions: converting `.docx` to `.md`, a metadata template, and the recommended Git workflow.

## Requirements
- **Pandoc** installed (https://pandoc.org).  
- **Git** configured.  
- (Optional) **Git LFS** if you will store many large binary files.

## Metadata template (place at top of each chapter)
    ---
    title: "Chapter 03 — Title"
    date: 2026-01-15
    version: "0.9-draft"
    license: "All Rights Reserved" # or "CC BY-NC-ND 4.0"
    ---

## Bulk conversion `.docx` → `.md` (recommended flow)
1. Gather all `.docx` files in a working folder.  
2. Run the conversion script below.  
3. Review generated `.md` files and fix formatting, footnotes and image links.  
4. Move each `.md` and its media folder to `/novels/<title>/`.  
5. Commit on a draft branch and push; merge to `main` when publishing.

### Bash script (Linux / macOS / WSL)
Save as `convert-docx-to-md.sh`, run `chmod +x convert-docx-to-md.sh`, execute in the folder with `.docx` files.

    #!/usr/bin/env bash
    set -euo pipefail

    OUT_DIR="converted_md"
    mkdir -p "$OUT_DIR"

    for f in *.docx; do
      [ -e "$f" ] || continue
      base="$(basename "$f" .docx)"
      media_dir="$OUT_DIR/media-$base"
      mkdir -p "$media_dir"
      echo "Converting: $f -> $OUT_DIR/$base.md (media in $media_dir)"
      pandoc --extract-media="$media_dir" -f docx -t gfm -o "$OUT_DIR/$base.md" "$f"
    done

    echo "Conversion finished. Files in: $OUT_DIR"

### PowerShell script (Windows)
Save as `Convert-DocxToMd.ps1` and run in the folder with `.docx` files.

    $OutDir = "converted_md"
    if (-not (Test-Path $OutDir)) { New-Item -ItemType Directory -Path $OutDir | Out-Null }

    Get-ChildItem -Filter *.docx | ForEach-Object {
        $file = $_.FullName
        $base = $_.BaseName
        $mediaDir = Join-Path $OutDir ("media-" + $base)
        if (-not (Test-Path $mediaDir)) { New-Item -ItemType Directory -Path $mediaDir | Out-Null }
        Write-Host "Converting: $file -> $OutDir\$base.md (media in $mediaDir)"
        pandoc --extract-media="$mediaDir" -f docx -t gfm -o (Join-Path $OutDir ($base + ".md")) $file
    }

    Write-Host "Conversion finished. Files in: $OutDir"

## Recommended Git workflow (summary)
- Create a draft branch: `git checkout -b draft/<title>-chapters`.  
- Add files: `git add novels/<title>/*`.  
- Commit: `git commit -m "Add chapters converted from .docx to .md"`.  
- Push: `git push -u origin draft/<title>-chapters`.  
- Merge to `main` when ready and tag a release (`v1.0`) if appropriate.

## Media and large files
- Keep media folders next to each `.md` (e.g., `media-chapter-01/`).  
- If many binaries, set up **Git LFS** before pushing.

## Notes
- Manually check tables, footnotes and complex formatting after conversion.  
- Update this guide if tools or versions change.
