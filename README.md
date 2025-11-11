# bluesky-hashtag-analyzer
Analyzes hashtag usage on Bluesky

To run this Python script, it's helpful to create a virtual environment first. Here's how: 

1. First create a directory on your Mac. I put mine at: Documents/Python-scripts/bsky-like-analysis

2. Here's the specific lines of code to run in Terminal on MacOS. (Replace "YOUR-MACOS-USERNAME" with your username. Do not include the dollar signs. That's just how people indicate that it's the start of a new line in a Terminal command)

```
$ cd /Users/YOUR-MACOS-USERNAME/Documents/Python-scripts/bsky-like-analysis
$ virtualenv env
$ source env/bin/activate
$ pip install atproto
$ python like-analysis.py
```
