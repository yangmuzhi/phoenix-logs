Tested with **Python 3.5+**

# Logs downloader

Tools to download phoenix replays from tenhou.net. 
For example these logs can be useful for machine learning exercises.

This repo contains two main scripts:

- Download and store log ids. 
We can both obtain game ids from year archive (e.g., http://tenhou.net/sc/raw/scraw2009.zip) 
or from latest phoenix games page (http://tenhou.net/sc/raw/list.cgi)
- Download logs content to already collected log ids.

# Installation

Just install requirements with command `pip install -r requirements.txt`

# Download historical log ids

For example we want to download game ids for 2009 year (keep in mind that phoenix games started to appear only from 2009 year).

Input command:
`python main.py -a id -y 2009`

If script is doing download really slow, you can download archive with `wget` or your browser and put it in the `temp` folder.
Example: Download http://tenhou.net/sc/raw/scraw2009.zip and put it to the `temp/scraw2009.zip`. 
In that case script will skip downloading step.

Output:
```
Set up new database /path/to/db/2009.db
Downloading... scraw2009.zip
[==================================================] 50822/50822
Downloaded
Extracting archive...
Extracted
Preparing the list of games...
Found 80156 games
Temp folder was removed
Inserting new ids to the database...
Done
```

# Download latest log ids
 
To download games from 1 January (current year) until (current day - 7 days) specify `-s` flag:

`python main.py -a id -s`

To download just log ids from latest 7 days:

`python main.py -a id`

You can add this command to the cron (for example to run each one hour) and it will add new log ids to the DB.

# Download log content

To download log content for already downloaded ids use this command:

`python main.py -a content -y 2009 -l 50 -t 3`

Where is `-l` is how much items to download and `-t` is count of threads to use.

It will create N threads and parallel downloads. 
You can choose that `-l` and `-t` numbers to download logs that will take ~one minute and add this command to a cron job. 
I used `-l 180 -t 5` for my downloads.

# Data consistency checking

Sometimes log content can't be downloaded because of different reasons (e.g., internet connection issues, tenhou server responsibility).

And sometimes tenhou return for log A content from log B and it causes same log content for different log ids in our db.

For example for 2009 year (with total 80156 logs) I had ~1500 not downloaded logs and ~800 logs with double content.
So, ~2.9% of records had issues in the end of downloading process.

To fix these issues run this command:

`python debug.py -y 2009`

It will detect and add all troubled records to the download queue again and you can redownload them as usual.
