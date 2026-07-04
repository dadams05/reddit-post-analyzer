# reddit-post-analyzer

This is a simple Python script that will analyze a Reddit post, its comments, and any attached images using Ollama.

* Analyzes the text of the post
* Analyzes any pictures attached in the post
* Analyzes all comments on the post
* Uses Reddit's official API via `PRAW`
* Filters out comments with blacklisted words

## Requirements

* Developed and tested with **Python 3.14.2**. Earlier Python 3.x versions may work but are not actively tested.
* A Reddit API Client account. To set this up:
  1. Go to your [Reddit App Preferences](https://www.reddit.com/prefs/apps).
  2. Scroll to the bottom and click **"create another app..."**.
  3. Provide a name, select **script** as the application type, and add a redirect URI (e.g., `http://localhost:8080`).
  4. Note your `Client ID` (visible under the application name) and your secret `Client Secret` token.
* [Ollama](https://ollama.com/) will need to be installed and running. This script uses the following models, so unless changed to others, these models also need to be downloaded using `ollama pull <model_name>`:
  * `llama3:8b` for overall analysis/summary of the post
  * `moondream` for image analysis and text extraction
* When installing the dependencies, it is important to note that you may need a specific version of `pytorch` to match your system's CUDA version. You can check your CUDA version with `nvidia-smi` in a terminal and reference [PyTorch](https://pytorch.org/get-started/locally/) for the correct setup.

## Model Configuration

The script's local AI behavior is controlled entirely by globals defined at the top of `main.py`. You can adjust these to use smaller, faster models if you are resource-constrained, or larger models if you have high-end hardware.

#### Default Models
*   **Text Analyzer (`ANALYZER_MODEL`):** `llama3:8b` (Handles post summarizing, comment structuring, and overall synthesis)
*   **Vision Extractor (`VISION_MODEL`):** `moondream` (Handles image text extraction and visual data parsing)
*   **Sentiment Classifier (`MODEL`):** `cardiffnlp/twitter-roberta-base-sentiment-latest` (Downloaded via Hugging Face/Transformers to process comment sentiment batches)

#### Swapping Models
To switch a model, update its corresponding global string variable in `main.py`:

```python
# Change these values at the top of main.py
VISION_MODEL = "llava:7b"      # Alternative vision model
ANALYZER_MODEL = "mistral:7b"  # Alternative text model

```

> ⚠️ **Important:** If you change `VISION_MODEL` or `ANALYZER_MODEL`, make sure you pull the new models via Ollama first (`ollama pull <model_name>`) before running the script.

#### Alternative Recommendations

Depending on your available VRAM and hardware setups, consider swapping to these alternatives:

| Use Case | Vision Model (`VISION_MODEL`) | Text Model (`ANALYZER_MODEL`) | Notes |
| --- | --- | --- | --- |
| **Lightweight / Speed** | `moondream` *(~1.8B)* | `llama3.2:3b` or `gemma2:2b` | Best for laptops or systems with less than 8GB VRAM. Fast execution times. |
| **Default balanced** | `qwen3-vl:4b` | `llama3:8b` | Excellent balance of precision, chart parsing, and text generation. |
| **Maximum Accuracy** | `llama3.2-vision:11b` | `llama3.1:8b` or `mistral-nemo` | Best for setups with 12GB+ VRAM. Noticeably superior chart reading and data extraction. |

## Usage

#### 1. Installation

Clone this repository and move directly into the project directory:

```bash
git clone https://github.com/dadams05/reddit-comment-scraper.git
cd reddit-comment-scraper
```

#### 2. Environment Setup

Create and activate an isolated Python virtual environment:

```bash
# Windows
py -m venv .venv
.\.venv\Scripts\activate

# Linux / macOS
python3 -m venv .venv
source .venv/bin/activate
```

With your environment active, install the script dependencies:

```bash
pip install -r requirements.txt
```

#### 3. Install Dependencies

Install dependencies (make sure your venv is activated). If you need GPU acceleration on Windows/Linux with CUDA 13+, install PyTorch explicitly first, then run requirements:
```bash
pip3 install torch torchvision --index-url https://download.pytorch.org/whl/cu132
pip install -r requirements.txt
```

#### 4. API Configuration

Rename `.env.example` to `.env`. Use your credentials from the Reddit App panel to fill out the keys.

Alternatively, create a file named `.env` in the root of the project directory. Use your credentials from the Reddit App panel to fill out the keys matching the layout below:

```ini
CLIENT_ID="your_client_id_here"
CLIENT_SECRET="your_client_secret_here"
USER_AGENT="script:reddit-comment-scraper:v1.0 (by /u/your_reddit_username)"
```

## Running

Run `main.py` while in your virtual environment. It will analyze the Reddit post linked at `REDDIT_LINK` and output the results into a `.json` file.

## Example Run

This is example console output of when the script is ran.

```bash
(.venv) py .\main.py
Warning: You are sending unauthenticated requests to the HF Hub. Please set a HF_TOKEN to enable higher rate limits and faster downloads.
Loading weights: 100%|████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 201/201 [00:00<00:00, 57044.12it/s]
RobertaForSequenceClassification LOAD REPORT from: cardiffnlp/twitter-roberta-base-sentiment-latest
Key                             | Status     |  | 
--------------------------------+------------+--+-
roberta.embeddings.position_ids | UNEXPECTED |  | 
roberta.pooler.dense.bias       | UNEXPECTED |  | 
roberta.pooler.dense.weight      | UNEXPECTED |  | 

Notes:
- UNEXPECTED    :can be ignored when loading from different task/architecture; not ok if you expect identical arch.
>> Starting
>> Setting up directories. Output will be saved in "out\1rpg0mb"
>> Getting initial post data
>> Gallery post: downloading images
https://preview.redd.it/9lfh63kvp3og1.jpg?width=1080&format=pjpg&auto=webp&s=81b5597d296c99e09caa485e139768ee9b17750c downloaded successfully to out\1rpg0mb\0.jpg
https://preview.redd.it/w760zomvp3og1.jpg?width=1080&format=pjpg&auto=webp&s=8da42a3f05e41fa18ad47d14eed0c5c9b5035bb9 downloaded successfully to out\1rpg0mb\1.jpg
https://preview.redd.it/loswuxovp3og1.jpg?width=1080&format=pjpg&auto=webp&s=ce53a4bb57b961db05c08169209cf58ea538abc2 downloaded successfully to out\1rpg0mb\2.jpg
https://preview.redd.it/a3i99mqvp3og1.jpg?width=1080&format=pjpg&auto=webp&s=771e0ae09cdbfa58663e2d09875ee5d69ae41816 downloaded successfully to out\1rpg0mb\3.jpg
https://preview.redd.it/zt36c4tvp3og1.jpg?width=1080&format=pjpg&auto=webp&s=69b1701c223664fa3fb5854b39d65fc1d25da7a7 downloaded successfully to out\1rpg0mb\4.jpg
https://preview.redd.it/dkw6u2vvp3og1.jpg?width=1080&format=pjpg&auto=webp&s=fe1e7c8f0d113ee497831d1a3c1587676100823d downloaded successfully to out\1rpg0mb\5.jpg
https://preview.redd.it/gib6adxvp3og1.jpg?width=1080&format=pjpg&auto=webp&s=280dedda44c3fe13c51b7a579ebd87ec3a1e3e97 downloaded successfully to out\1rpg0mb\6.jpg
>> Analyzing images
>> Injecting image analysis back into post text
>> Downloading and filtering comments
>> Analyzing comment sentiment
You seem to be using the pipelines sequentially on GPU. In order to maximize efficiency please use a dataset
>> Analyzing post and comments
>> Saving data out to "out\1rpg0mb\analysis_2026-03-11_01-07-25.json"
```

This is an example of the `.json` file that is outputted.

```json
{
    "title": "Welp. Back to square one.",
    "created": "2026-03-09 18:08:50",
    "selftext": "I think I'm done. Between me and my wife's account, overall loss several years of income playing options. At one point I was up nearly +$100k on my personal account. That was mostly with SPY, Nvda, and Tsla.  Then I started to bleed.  And tried to win the losses back. And then doubled down. And then tripled down. Sometime along the way, my account got permanently closed by RH (Can't even view my chart on that account anymore). Started playing on wife's after that.\n\nOriginally found out about options thanks to some news article mentioning this Sub and someone making tons on options. \nIn 2022 I sold 407 shares of Nvidia. That was before the 10-1 split. That's worth ~$750k now. Instead ... I'm now deep in the negative.\n\nHad some good plays, pretty spikes, went down to $200 within a week $200 -> $28k, only to lose $27k the next day. Did a juicy SPY 0dte play last week, and then blew entire account next day on the 5th with yolo on spy calls, and then it tanked. Took 15 mins to wipe out the account. Got a little desperate and a little extra dumb.\n\nGot a decent job now, working 70-80hr weeks for OT. Opened a Webull (RH is too tempting to play options cuz that green/red gives a dopamine rush or something). Now I'll just be throwing a few grand a month on a SPY/Nvidia ect, and just let it do its thing. Some day/decade, I'll get that $160k+ back via slow growth I guess. It's been a fun run. Got addicted. Going full broke fixed that addiction I think. Time to give up on that \"get rich quick\" dream. Got ~$90k debt to pay off and be debt free finally.\n\nSeeing SPY today shoot up and watching a $0.04 0dte go  to $5.68 this morning made me sick..... After I blew the entire account just last week making that same dumb bet.... Woulda recovered all my losses and some today had I waited.\n\nPics 5-6 is the 0dte today that makes me wanna kick myself. 4 was last week's play. 3 is the shares I had once upon a time. 2 is original account closed. 1 is me today.\n\nLast pic is my original accounts 2022 statement.  Adding my loss writeoffs + wife's overall = ~$160k loss.  Now sure on how the wash sale number gets added up so didn't count that towards by ~$160k loss. I don't want to know my actual total loss.\n* * * * * *\nSlow and steady now. Back to collecting shares.\nEnjoy the photo dumps.\nJust thinking about this again makes me feel depressed. At 28yo, honestly thought I'd be further in life.",
    "id": "1rpg0mb",
    "score": 1688,
    "url": "https://www.reddit.com/gallery/1rpg0mb",
    "permalink": "/r/wallstreetbets/comments/1rpg0mb/welp_back_to_square_one/",
    "num_comments": 470,
    "filtered_num_comments": 322,
    "pos_count": 51,
    "neg_count": 165,
    "neu_count": 106,
    "comments": [
        {
            "id": "o9kszlc",
            "created": "2026-03-09 18:25:25",
            "score": 1616,
            "body": "> Going full broke fixed that addiction I think. > Woulda recovered all my losses and some today had I waited Hate to be the one to tell you… you’re still addicted. You’re just a broke addict now.",
            "sentiment": {
                "label": "negative",
                "score": 0.7321282029151917
            }
        },
        {
            "id": "o9ku6p5",
            "created": "2026-03-09 18:32:13",
            "score": 253,
            "body": "Lmfao you lost so badly that RH didn’t even let you respawn",
            "sentiment": {
                "label": "negative",
                "score": 0.9102975726127625
            }
        },
        {
            "id": "o9kst3q",
            "created": "2026-03-09 18:24:24",
            "score": 443,
            "body": "You belong to the hall of fame. Sold Nvidia before the AI run-up and proceed to lose it all lol.",
            "sentiment": {
                "label": "neutral",
                "score": 0.43785223364830017
            }
        }
    ],
    "prompt": "...",
    "analysis": "..."
}
```

## Future Todo

* Start up a local server on a port to give a web ui
* Display the `.json` results in a prettier way on the web ui
* Add optional Dockerfile for Ollama instead of having to download it and all the models
