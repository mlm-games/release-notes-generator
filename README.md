# Multi-Release Notes Action

A GitHub Action that generates and manages release notes across multiple platforms and formats - CHANGELOG.md, version files, GitHub releases, Flathub metainfo.xml, and more.

## Features

- 📝 Auto-generates release notes from git commits
- 📋 Updates CHANGELOG.md with proper versioning
- 📱 Creates Android/Fastlane changelog files
- 🐧 Updates Flathub metainfo.xml release entries
- 🔄 Supports custom commit formats and exclusion patterns
- 💾 Auto-commits changes (optional)

## Quick Start

```yaml
- uses: your-username/multi-release-notes@v1.0.2
  with:
    version: '1.2.3'
    changelog-path: 'CHANGELOG.md'
    version-file-path: './fastlane/metadata/android/en-US/changelogs/123.txt'
    version-code: '123'
    metainfo-path: './com.yourapp.metainfo.xml'
```

## Inputs

| Input | Required | Default | Description |
|-------|----------|---------|-------------|
| `version` | ✅ | - | Version number (e.g., 1.2.3) |
| `changelog-path` | ❌ | `CHANGELOG.md` | Path to CHANGELOG.md |
| `version-file-path` | ❌ | - | Path to version/changelog file |
| `version-code` | ❌ | - | Version code for Android/Fastlane |
| `metainfo-path` | ❌ | - | Path to Flathub metainfo.xml |
| `head-ref` | ❌ | `HEAD` | Git ref to generate notes from |
| `format` | ❌ | `- {{subject}} by @{{author}}` | Commit format template |
| `exclude-patterns` | ❌ | `Update version to,Merge ,Auto-generate changelog` | Comma-separated patterns to exclude |
| `auto-commit` | ❌ | `true` | Auto-commit changes |
| `commit-message` | ❌ | `Update release notes for v{{version}}` | Commit message template |

## Outputs

| Output | Description |
|--------|-------------|
| `release-notes` | Generated release notes text |
| `release-name` | Latest commit subject (for release title) |
| `changelog-updated` | Whether CHANGELOG.md was updated |
| `files-changed` | Comma-separated list of changed files |

## Usage Examples

### Basic Usage
```yaml
- uses: your-username/multi-release-notes@v1.0.2
  with:
    version: ${{ github.event.inputs.version }}
```

### Full Integration
```yaml
- name: Generate Release Notes
  uses: your-username/multi-release-notes@v1.0.2
  id: release-notes
  with:
    version: ${{ github.event.inputs.version }}
    changelog-path: 'CHANGELOG.md'
    version-file-path: './fastlane/metadata/android/en-US/changelogs/${{ steps.version.outputs.code }}.txt'
    version-code: '${{ steps.version.outputs.code }}'
    metainfo-path: './data/com.myapp.metainfo.xml'
    format: '- {{subject}} ({{author}})'
    exclude-patterns: 'chore:,ci:,Update version'
    auto-commit: 'true'

- name: Create GitHub Release
  uses: softprops/action-gh-release@v2
  with:
    tag_name: v${{ github.event.inputs.version }}
    body: ${{ steps.release-notes.outputs.release-notes }}
    name: ${{ steps.release-notes.outputs.release-name }}
```

### Android/Fastlane Only
```yaml
- uses: your-username/multi-release-notes@v1.0.2
  with:
    version: '2.0.0'
    version-file-path: './fastlane/metadata/android/en-US/changelogs/200.txt'
    version-code: '200'
    auto-commit: 'false'  # Handle commit yourself
```

### Flathub Release
```yaml
- uses: your-username/multi-release-notes@v1.0.2
  with:
    version: '1.5.0'
    metainfo-path: './data/io.github.myapp.metainfo.xml'
    changelog-path: ''  # Skip CHANGELOG.md update
```

## Format Templates

Use these placeholders in the `format` input:
- `{{subject}}` - Commit subject line
- `{{author}}` - Commit author username
- `{{committer}}` - Committer username
- `{{message}}` - Full commit message

Supports fallbacks with pipes: `{{author|committer}}`

## File Formats

### CHANGELOG.md
```markdown
## v1.2.3

- Added new feature by @user1
- Fixed bug by @user2
```

### Fastlane Changelog
```
- Added new feature by @user1
- Fixed bug by @user2
```

### Flathub metainfo.xml
```xml
<releases>
  <release version="1.2.3" date="2024-01-15">
    <description>
      <ul>
        <li>Added new feature by @user1</li>
        <li>Fixed bug by @user2</li>
      </ul>
    </description>
  </release>
</releases>
```

## Common issues

If you get an error related to labels (no permmision for labels), then just add this line in the permissions section of the workflow

```issues: write```

## License

MIT
