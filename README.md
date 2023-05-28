# GitHub Action: nikto

This action runs `nikto` to scan a URL with the given parameters. Recommended use is with a @vX tag to ensure a stable version is used instead of @master. Versions can be found here [tags](https://github.com/thereisnotime/Action-nikto/tags) and nikto documentation for all parameters [here](https://github.com/sullo/nikto/wiki).

NOTE: Due to weird versioning of the author the version is locked to 2.5.0 for now.

## Examples

### Start a scan

```yaml
- name: Scan with nikto
  uses: thereisnotime/action-nikto@master
  with:
    url: "https://blackfox-security.com"
```

### Weekly scheduled scans

```yaml
on:
  schedule:
    - cron: 0 11 * * 1 # Monday at 11 UTC

jobs:
  scan_nikto:
    runs-on: ubuntu-latest
    name: SCAN | nikto DAST
    steps:
      - name: Scan with nikto
        uses: thereisnotime/action-nikto@master
        with:
          url: "https://blackfox-security.com"
          additional_args: "-ssl"
```

### Use latest version in a job

```yaml
scan_nikto:
  runs-on: ubuntu-latest
  name: SCAN | nikto DAST
  steps:
    - name: Scan with nikto
      uses: thereisnotime/action-nikto@master
      with:
        url: "https://blackfox-security.com"
        additional_args: "-ssl"
```

### Use latest version in a job and upload the results back to the repository

```yaml
scan_nikto:
  runs-on: ubuntu-latest
  name: SCAN | nikto DAST
  steps:
    - name: Prepare environment
      run: |
        echo "START_DATE=$(date +'%d-%m-%YT%H-%M-%S-%Z')" >> $GITHUB_ENV
        echo "START_TIMESTAMP=$(date +'%s')" >> $GITHUB_ENV
        TMP_TARGET="https://blackfox-security.com"
        echo "TARGET=$TMP_TARGET" >> $GITHUB_ENV
    - name: Checkout
      uses: actions/checkout@v3
    - name: Scan with nikto
      uses: thereisnotime/action-nikto@master
      with:
        url: ${{ env.TARGET }}
        additional_args: "-ssl"
    - name: Commit and push changes
      uses: EndBug/add-and-commit@v9
      with:
        author_name: GitHub Actions
        author_email: 41898282+github-actions[bot]@users.noreply.github.com
        message: "👷 chore(nikto): add report for ${{ env.TARGET }}-${{ env.START_DATE }}-${{ env.START_TIMESTAMP }}"
        add: "./reports/web/*"
        pull: "--rebase --autostash"
```

## Inputs

### `url`

**Required**. Target URL (e.g. <http://blackfox-security.com>)

### `additional_args`

Additional arguments to pass to nikto.

## Outputs

### `result`

JSON scan results.

### `resultb64`

JSON scan results, base64 encoded.

## Roadmap

If there is enough time or interest, I will add the following features (feel free to open PRs):

- [ ] Finish support for outputs (e.g. `result` and `resultb64`).
- [ ] Add support for notification via Slack.
- [ ] Add support for notification via Telegram.
- [ ] Add support for notification via email.
- [ ] Add support for notification via custom webhooks.
- [ ] Improve argument handling.
