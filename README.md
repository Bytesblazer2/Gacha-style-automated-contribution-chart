# Gacha-style-automated-contribution-chart
Randomly automate your contributions chart
🎲 Gacha Daily Committer
An easy-to-use GitHub tool that automatically updates your contribution graph every day with random green shades—just like opening a mystery box in a game!
💡 How It Works
Every night at midnight, the project rolls a 100-sided die to decide your "loot drop" for the day:
 * 🟢 Common Drop (50% chance): 1 commit → Light Green
 * 🟩 Rare Drop (30% chance): 3 commits → Medium Green
 * 🌲 Epic Drop (15% chance): 6 commits → Dark Green
 * ✨ Legendary Drop (5% chance): 12 commits → Deepest Green (Happens ~once every 3 weeks!)
🚀 How to Set It Up (Step-by-Step)
Step 1: Create the Automation File
 * In your GitHub repository, click Add file > Create new file.
 * Name the file .github/workflows/daily-commit.yml
 * Paste the code below inside:
name: Daily Auto Commit

on:
  schedule:
    - cron: '0 0 * * *' # Runs every day at midnight UTC
  workflow_dispatch: # Lets you start it manually anytime

permissions:
  contents: write # Allows GitHub to write to your project

jobs:
  auto-commit:
    runs-on: ubuntu-latest
    steps:
      - name: Get latest code
        uses: actions/checkout@v4

      - name: Roll die and make commits
        run: |
          git config --global user.name "github-actions[bot]"
          git config --global user.email "41898282+github-actions[bot]@users.noreply.github.com"
          
          # Roll a random number from 1 to 100
          ROLL=$(( ( RANDOM % 100 ) + 1 ))
          
          # Pick tier based on roll number
          if [ $ROLL -le 50 ]; then
            COMMITS=1
            TIER="Common"
          elif [ $ROLL -le 80 ]; then
            COMMITS=3
            TIER="Rare"
          elif [ $ROLL -le 95 ]; then
            COMMITS=6
            TIER="Epic"
          else
            COMMITS=12
            TIER="Legendary"
          fi

          echo "Rolled $ROLL: Got a $TIER drop!"

          # Add dates to a text file to save changes
          for i in $(seq 1 $COMMITS); do
            date --iso-8601=seconds >> activity.txt
            git add activity.txt
            git commit -m "Commit $i of $COMMITS [$TIER Drop] [skip ci]"
          done
          
          # Save back to your GitHub repository
          git push

Step 2: Give Permission to Write Code
 * Click Settings at the top of your repository.
 * On the left side, click Actions > General.
 * Scroll down to Workflow permissions.
 * Choose Read and write permissions.
 * Click Save.
Step 3: Test It Out
 * Click the Actions tab at the top of your repository.
 * Click Daily Auto Commit on the left menu.
 * Click the Run workflow button.
 * Check your project main page after 10 seconds—you will see activity.txt update automatically!
