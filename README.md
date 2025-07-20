# Git Setup Guide for Server (Pull-Only)


##  Steps

### 1. Clean Previous Git Configuration

Remove any existing Git data:

```bash
rm -rf .git
```

> Make sure there's no leftover Git config, including .gitignore

---

### 2. Initialize New Repository

```bash
git init
git config user.email "your-email@example.com"
git config user.name "Your Name"
```

> Change email and name as needed.

---

### 3. Add `.gitignore`

Create `.gitignore` file to ignore everything except:

- `.php`, `.js`, `.css`
- `.gitkeep` (to preserve empty folders)

```
**
!*/
!**/*.php
!**/*.js
!**/*.css
!**/.gitkeep
```

---

### 4. Auto Add `.gitkeep` to Non-Code Folders

#### Bash Script (`.sh`)

```bash
#!/bin/bash
contains_code_file() {
  find "$1" -type f \( -iname "*.php" -o -iname "*.js" -o -iname "*.css" \) | grep -q .
}
find . -type d -depth | while read dir; do
  [[ "$dir" == ./.git* ]] && continue
  if ! contains_code_file "$dir"; then
    [ ! -f "$dir/.gitkeep" ] && touch "$dir/.gitkeep" && echo "add .gitkeep to $dir"
  fi
done

echo "Finished adding .gitkeep to non-code folders."
```

#### PowerShell Script (`.ps1`)

```powershell
function Contains-CodeFile {
    param ([string]$Path)
    $codeFiles = Get-ChildItem -Path $Path -Recurse -File -Include *.php,*.js,*.css
    return $null -ne $codeFiles
}

Get-ChildItem -Path . -Directory -Recurse | ForEach-Object {
    $dir = $_.FullName
    if ($dir -notlike "*\.git*") {
        if (-not (Contains-CodeFile -Path $dir)) {
            $gitkeepPath = Join-Path $dir ".gitkeep"
            if (-not (Test-Path $gitkeepPath)) {
                New-Item -Path $gitkeepPath -ItemType File | Out-Null
                Write-Host "add .gitkeep to $dir"
            }
        }
    }
}

Write-Host "Finished adding .gitkeep to non-code folders."
```

---

### 5. Commit and Push to Remote

```bash
git add .
git commit -m "Initial commit with .php, .js, and .css only"
git remote add origin https://github.com/username/repo-name.git
git push origin master
git ls-files -z | xargs -0 git update-index --assume-unchanged
```

---

## 📌 Important

**On the server**, use only:

```bash
git pull
```

> Do not commit or push from the server.

---
