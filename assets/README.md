# yml 文件备份

```yml
# 二合一
name: AutoCommit and Snake

on:
  # AutoCommit schedule
  schedule:
    - cron: '0 0 * * *'
  # Snake schedule
  schedule:
    - cron: '0 */24 * * *'
  workflow_dispatch:

jobs:
  commit:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        # https://github.com/actions/checkout
        uses: actions/checkout@v3

      - name: Create empty commit
        run: |
          git config --global user.name "JackieDYH"
          git config --global user.email "1824561218@qq.com"
          git commit --allow-empty -m "Daily Commit"
          git push

  generate:
    runs-on: ubuntu-latest
    timeout-minutes: 10

    steps:
      - name: generate github-contribution-grid-snake.svg
        uses: Platane/snk/svg-only@v3
        with:
          github_user_name: ${{ github.repository_owner }}
          outputs: |
            dist/github-contribution-grid-snake.svg
            dist/github-contribution-grid-snake-dark.svg?palette=github-dark
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - name: Output current time and user information to file
        run: |
          echo -e "# Workflow Information\n" > dist/README.md
          echo -e "Current Time (China): $(TZ='Asia/Shanghai' date)  \n" >> dist/README.md
          echo -e "**RUN_ID**: ${{ github.run_id }}  \n" >> dist/README.md
          echo -e "**Workflow**: ${{ github.workflow }}  \n" >> dist/README.md
          echo -e "**Run Number**: ${{ github.run_number }}  \n" >> dist/README.md
          echo -e "**Actor**: ${{ github.actor }}  \n" >> dist/README.md
          echo -e "**Event Name**: ${{ github.event_name }}  \n" >> dist/README.md
          echo -e "**Repository**: ${{ github.repository }}  \n" >> dist/README.md
          echo -e "**Commit SHA**: ${{ github.sha }}  \n" >> dist/README.md
          echo -e "**Event Path**: ${{ github.event_path }}  \n" >> dist/README.md
          echo -e "**Workspace**: ${{ github.workspace }}  \n" >> dist/README.md
          echo -e "Other information: [https://github.com/JackieDYH](https://github.com/JackieDYH)" >> dist/README.md

      - name: Push github-contribution-grid-snake.svg to the output branch
        uses: crazy-max/ghaction-github-pages@v3.1.0
        with:
          target_branch: output
          build_dir: dist
          keep_history: true
          committer: JackieDYH <1824561218@qq.com>
          author: JackieDYH <1824561218@qq.com>
          commit_message: 'Generate Contribution Snake -ID:${{ github.run_id }} -actor:${{ github.actor }}'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
