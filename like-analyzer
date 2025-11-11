# pulls posts_found	unique_accounts	max_likes	min_likes	average_likes	median_likes
# pulls use per hour

from atproto import Client
import statistics
import csv
import time
from datetime import datetime


# --- CONFIG ---
USERNAME = "USERNAME-GOES-HERE" # e.g. username@bsky.social.com
PASSWORD = "APP-PASSWORD-GOES-HERE"

hashtags = [
    "photography",
    "blueskyartshow",
    "photooftheday",
    "photoaday",
    "dailyphoto",
    "photojournal",
    "photodiary",
    "aphotoaday",
    "365project",
    "everydayphotographer",
    "personalphotojournalism",
    "365photochallenge2025",
    "365photodgraphy2025",
    "picoftheday",
]

MAX_POSTS_PER_HASHTAG = 500  # total posts to fetch per hashtag
REQUEST_LIMIT = 100           # max per API call
SLEEP_BETWEEN_CALLS = 0.5     # seconds between API calls (avoid rate limits)

# --- LOGIN ---
client = Client()
client.login(USERNAME, PASSWORD)

# --- FUNCTION TO GET STATS ---
def get_hashtag_stats(tag, max_posts=MAX_POSTS_PER_HASHTAG):
    all_posts = []
    cursor = None

    while len(all_posts) < max_posts:
        params = {"q": f"#{tag}", "limit": REQUEST_LIMIT}
        if cursor:
            params["cursor"] = cursor

        response = client.app.bsky.feed.search_posts(params)
        posts = response.posts
        all_posts.extend(posts)

        cursor = getattr(response, "cursor", None)
        if not cursor or len(posts) < REQUEST_LIMIT:
            break  # no more pages

        time.sleep(SLEEP_BETWEEN_CALLS)

    likes = []
    authors = set()
    timestamps = []

    for p in all_posts:
        if getattr(p, "like_count", None) is not None:
            likes.append(p.like_count)
        if hasattr(p, "author") and hasattr(p.author, "handle"):
            authors.add(p.author.handle)
        if hasattr(p, "indexed_at") and p.indexed_at:
            timestamps.append(datetime.fromisoformat(p.indexed_at.rstrip("Z")))

    # compute posts per hour
    if timestamps:
        time_span_hours = max(timestamps).timestamp() - min(timestamps).timestamp()
        time_span_hours /= 3600  # convert seconds to hours
        posts_per_hour = round(len(all_posts) / time_span_hours, 2) if time_span_hours > 0 else len(all_posts)
    else:
        posts_per_hour = None

    if not likes:
        return {
            "hashtag": tag,
            "posts_found": len(all_posts),
            "unique_accounts": len(authors),
            "max_likes": None,
            "min_likes": None,
            "average_likes": None,
            "median_likes": None,
            "posts_per_hour": posts_per_hour
        }

    return {
        "hashtag": tag,
        "posts_found": len(all_posts),
        "unique_accounts": len(authors),
        "max_likes": max(likes),
        "min_likes": min(likes),
        "average_likes": round(sum(likes) / len(likes), 2),
        "median_likes": statistics.median(likes),
        "posts_per_hour": posts_per_hour
    }

# --- RUN ANALYSIS ---
results = [get_hashtag_stats(tag) for tag in hashtags]

# --- WRITE TO CSV ---
csv_filename = "bluesky_hashtag_stats.csv"
with open(csv_filename, mode="w", newline="", encoding="utf-8") as file:
    writer = csv.DictWriter(file, fieldnames=results[0].keys())
    writer.writeheader()
    writer.writerows(results)

print(f"Saved results to {csv_filename}")
