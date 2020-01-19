# depup - Action which updates dependencies automatically

[![Test](https://github.com/haya14busa/action-depup/workflows/Test/badge.svg)](https://github.com/haya14busa/action-depup/actions?query=workflow%3ATest)
[![reviewdog](https://github.com/haya14busa/action-depup/workflows/reviewdog/badge.svg)](https://github.com/haya14busa/action-depup/actions?query=workflow%3Areviewdog)

## Inputs

```yaml
inputs:
  github_token:
    description: 'GITHUB_TOKEN to get latest version with GitHub Release API'
    default: '${{ github.token }}'
  file:
    description: 'target file'
    required: true
  version_name:
    description: 'target version name. e.g. REVIEWDOG_VERSION'
    required: true
  repo:
    description: 'target GitHub repository. e.g. reviewdog/reviewdog'
    required: true
```

## Example usage

### [.github/workflows/depup.yml](.github/workflows/depup.yml)

```yml
name: depup
on:
  schedule:
    - cron:  '14 14 * * *'

jobs:
  depup:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - uses: haya14busa/action-depup@v1
        id: depup
        with:
          file: testdata/testfile
          version_name: REVIEWDOG_VERSION
          repo: reviewdog/reviewdog

      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v2
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          title: "chore(deps): update reviewdog to ${{ steps.depup.outputs.latest }}"
          commit-message: "chore(deps): update reviewdog to ${{ steps.depup.outputs.latest }}"
          body: |
            Update reviewdog to [${{ steps.depup.outputs.latest }}](https://github.com/reviewdog/reviewdog/releases/tag/v${{ steps.depup.outputs.latest }})

            This PR is auto generated by [depup workflow](https://github.com/${{ github.repository }}/actions?query=workflow%3Adepup).
          branch: depup/reviewdog
          base: master
```