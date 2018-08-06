# 3 million Russian troll tweets—by author!

All data in this repository is directly derived from [fivethirtyeight/russian-troll-tweets](https://github.com/fivethirtyeight/russian-troll-tweets/). If that’s not how you got here, please start there first.

This version of the dataset incorporates a variety of minor enhancements and changes, but its primary focus is the separation of **author data** from **tweet data**.

## One author, many tweets
In the original dataset, every row represents a tweet. Most of the columns contain data unique to each tweet, but five columns pertain to the author: `external_author_id`, `author`, `account_type`, `account_category`, and `new_june_2018`. With some [rare exceptions for `external_author_id`](https://github.com/fivethirtyeight/russian-troll-tweets/issues/16), these columns remain constant for every tweet sent by a particular author.

This leads to a lot of duplication. For example, the data captured 8,499 tweets from the handle “AtlantaOnline”. That author has an `external_author_id` of 2944766250, is categorized as a “NewsFeed” `account_category` with a “local” `account_type`, and is not marked as `new_june_2018`. This data really only needs to be stored once—but instead, it’s replicated for each of AtlantaOnline’s 8,499 tweets. All that duplication leads to larger file sizes.

Depending on what you’re doing, having the author data embedded within the tweet rows might not be ideal for your workflow.

In this repo, tweet data has been separated from author data. An [author info CSV](https://github.com/bet4a/russian-troll-tweets-by-author/blob/master/authors.csv) lists all the author data in one file, while a different CSV of tweets exists for each author. Here’s a quick rundown of the columns you’ll find in each kind of file. A lot of them are unchanged from the [FiveThirtyEight dataset](https://github.com/fivethirtyeight/russian-troll-tweets/).

### Author columns
|Column|Definition|
|----|----|
|`author_id`|A unique identifier for an author. In the vast majority of cases this is the same as the `author` name, but in a handful of cases, there are tweets by the same `author` but their `external_author_id` differs. Out of an abundance of caution, this dataset lists them as separate authors. For these cases, the `author_id` is the concatenation of the `author` name and their `external_author_id`.
|`author`| The handle sending the tweet. In the original dataset, these are ALLCAPS. I’ve matched up authors with the list from the [Nov 2018 House Intelligence Committee dataset](https://democrats-intelligence.house.gov/uploadedfiles/ira_handles_june_2018.pdf), which retains the original capitalization for users’ handles.
|`external_author_id`|Same as the original dataset, but floating-point formatting like `9.06000000000e+17` have been converted to regular forms (`906000000000000000`).
|`account_type`|No change.
|`account_category`| No change.
|`new_june_2018`| No change.
|`congress_2017_id`| The [Jun 2017 Intelligence Committee data](https://democrats-intelligence.house.gov/uploadedfiles/exhibit_b.pdf) included user_ids in addition to handle names, which is included here.
|`in_congress_2017`| Boolean indicator whether the user was listed in the November 2017 House Intelligence Committee dataset.
|`in_congress_2018`| Boolean indicator whether the user was listed in the June 2018 House Intelligence Committee dataset.

### Tweet columns
|Column|Definition|
|----|----|
|`author_id`|Corresponds to the `author_id` from the authors.csv table. (It’s also the same as the filename.)
|`content`| No change.
|`region`| No change.
|`language`| No change.
|`publish_date`| There is a single space separating the date and the time rather than a `T`. I’ve also ensured that all the tweet CSV files are sorted by this column in ascending order (earlier tweets appear first).
|`harvested_date`| There is a single space separating the date and the time rather than a `T`.
|`following`|No change.
|`followers`|No change.
|`updates`|No change.
|`post_type`|In the original, this was either blank, `RETWEET` or `QUOTE_TWEET`. In this data, it’s blank, `R` or `Q`.
|`retweet`|No change.

### Other minor enhancements
In the original data, there are a handful of users having multiple floating-point `external_author_id`s, with the variation seeming to be due to rounding. For example, there are some tweet entries from the user “blackunitymarch” showing an `external_author_id` of `7.69000000000e+17`, and others having an `external_author_id` of `7.69363000000e+17`.

For these cases, I’ve taken the more precise number, disgarding the other one, and treated it as a single author. So there is a [single entry in the authors.csv](https://github.com/bet4a/russian-troll-tweets-by-author/blob/master/authors.csv#L415) file for “blackunitymarch”, and a [single file](https://github.com/bet4a/russian-troll-tweets-by-author/blob/master/B/blackunitymarch.csv) for that author’s tweets.

## About this dataset
### How it was made
I wish I had documented my process, but I didn’t. It was largely hacking things together, full of trial-and-error and lots of intermediate steps that probably weren’t necessary. The main tools I used were:

* [csvkit](https://github.com/wireservice/csvkit): “A suite of utilities for converting to and working with CSV, the king of tabular file formats.” Built atop Python, these command-line tools were invaluable. Their documentation has a [remarkably well-written tutorial](https://csvkit.readthedocs.io/en/1.0.3/tutorial.html) for getting started.
* [SQLite3](https://www.sqlite.org/index.html): csvkit includes a `csvsql` command for generating a SQL table and exporting CVS data into it. But some of the column data types it auto-infers when creating tables aren’t optimal. I found it necessary to create tables and indices manually first, then use `csvsql` only for exporting data (not auto-generating tables).

### Final thoughts
This project is a slice-and-dice reorganization of the data from the [fivethirtyeight/russian-troll-tweets](https://github.com/fivethirtyeight/russian-troll-tweets/) project. **It is not a replacement.** If you’re not already familiar with this data, you should begin by [read about where it came from and how it was derived](https://fivethirtyeight.com/features/why-were-sharing-3-million-russian-troll-tweets/). Then you should look at the [official data repo from FiveThirtyEight](https://github.com/fivethirtyeight/russian-troll-tweets/); its README file is chock-full of valuable information.

There is a very good chance you’ll be perfectly satisfied with how the data is structured over there. If so, hooray! But if you find yourself wishing that the tweets were separated by author, or that having tweet-related data separated from author-related data would make your life a bit easier… then this is the repo for you.

Problems, questions, suggestions, angry criticisms about how I’m doing this all wrong? Please don’t hestitate to [file an issue](https://github.com/bet4a/russian-troll-tweets-by-author/issues/new) or message me! And feel free to submit a pull request if you’re extra-generous and have any improvements to contribute.
