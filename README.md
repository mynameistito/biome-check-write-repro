# biome check --write HTML repro

Minimal reproduction for a Biome bug where `biome check --write` always reports
"Fixed 1 file" on any HTML file with a `<style>` tag, even though the file
doesn't actually change.

## Reproduce

```bash
npx @biomejs/biome@2.4.9 check --write index.html
# "Checked 1 file in 2ms. Fixed 1 file."

npx @biomejs/biome@2.4.9 check --write index.html
# "Checked 1 file in 2ms. Fixed 1 file."  (same result, forever)

npx @biomejs/biome@2.4.9 check index.html
# "Checked 1 file in 2ms. No fixes applied."  (disagrees)

npx @biomejs/biome@2.4.9 format --write index.html
# "Formatted 1 file in 2ms. No fixes applied."  (also disagrees)
```

## Config

The only config needed is enabling the HTML formatter:

```json
{
  "html": {
    "formatter": {
      "enabled": true
    }
  }
}
```

## Details

- Biome 2.4.9
- Any HTML file with a `<style>` tag triggers this
- `git diff` shows no changes after `check --write`
- `biome check` and `biome format --write` both say the file is clean
- Debug log shows `Format output is different from input: true` at `format.rs:106`
