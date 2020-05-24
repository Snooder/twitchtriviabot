# Twitch Trivia Bot

Twitch Trivia Bot (TTB) is a Python script to run a trivia session in Twitch chat. The bot will load a quiz set, run a session of trivia, assign a winner and save scores dynamically. 

![img](https://i.imgur.com/YYznPaN.png)

# Features:
+ Loads csv files for trivia questions, and answers, and handles any amount of questions (limited only by csv constraints)
+ Dynamically selects a random set of questions from the pool of questions
+ Automatically asks questions, generates dynamic hints, and skips questions based on configurable time delays
+ BONUS mode, allowing for extra points to be rewarded
+ Stores trivia performance over multiple sessions, allowing participants to retrieve scores at any time
+ Trivia sessions cut short by bot disconnect can be reloaded via actively created backups

# Setup
The latest Python 3 install is required for this bot. You will need to install Python 3 to be able to run. One can either use standard Python 3 distribution with IDLE, or use something like the Anaconda distribution with Spyder (what I use). 

You will likely need to install a few dependencies, including `PyYaml` and `PyQt5`. You can do this via command line `pip install [x]` or `pip3 install [x]` (e.g. "pip install PyYaml"). On your first few runs if the program/GUI does not load, make sure to check `config/output_log.log` to read the error codes to identify what needs to be installed. 

Please use the [latest Release](/releases/latest/) download for initial install. 

+ “twitchtriviabot.py” does not need to be reconfigured. This will be loaded via Python console, and will connect to Twitch chat.
A trivia set file (csv) needs to be set up (more on this below). Default with this package is ‘triviaset.csv’ with the appropriate columns pre-identified. 
+ “auth_config.yml” and “trivia_config.yml” need to be configured. All inputs should be **without** brackets or number signs. Open these files in a text editor.
  + Under “trivia_config.yml”, the first two filename & filetype should match the trivia set file (default is ‘triviaset.csv’) All numeric fields represent seconds:
    + question_count = # of questions for the trivia session to include. This will be pulled randomly for the trivia list from csv. To be clear, this number isn't the number of questions in your trivia set, it's the number of questions you want a trivia session to hold
    + hint_time1 / hint_time2 = Time delay before hints 1 and 2 are supplied. Hints are described below
    + skip_time = Time delay before question is skipped. This is also used during 'poll' modes for how long to wait before polling
    + question_delay = Time delay after question is answered, before next question is asked
    + question_bonusvalue = Value assigned to BONUS round questions. Bonus round described below
    + mode = There are three modes:
        + 'single' - Standard trivia, first to answer wins the point
        + 'poll' - In this mode, during the entirety of the skip_time, questions are polled for answers from players. At the end of each question, the first and second player to respond earn more points, then all others get some less amount of point
        + 'poll2' - This is the same as above, but every question has a separate answer pool for "answer" and "answer2". This means, for example, one question can have two separate answers, and players can score on each answer separately
    + admins - separate by commas
        + E.g., admins = player
        + E.g., admins = player,respondent
  + “auth_config.yml” :
    + host = default irc.twitch.tv
    + port = default 6667 (for twitch)
    + nick = username for the bot
    + pass = password in “oath:xxxxxx... “ format. Retrieve from https://twitchapps.com/tmi/ for the bot
    + chan = twitch channel to connect the bot to, where trivia will take place. This has changed in version 2, where no number sign (#) is needed (i.e. cleartonic)

To set up triviaset.csv properly, consider the following:
5 headers in this release are specified: ‘category, ‘question’, ‘answer’, ‘answer2’, ‘creator’. Keep them in this order.
Fill out row by row each question, filling in topic/game, and at least 1 Answer column. Creator is not required. 
When saving the file:
+ If saving as a csv, the file format must be formatted as `CSV UTF-8` 

A log will be saved per run to `config/output_log.log`. This will accumulate over time, and is useful for debugging problems. At any point, you can delete this file if it becomes too large. 

# Running the bot

With a command line/terminal open:
`python twitchtriviabot.py` or `python3 twitchtriviabot.py`

From here, you can monitor the bot’s activity. All user responses in the Twitch channel will appear in the console. Each question’s answer will appear in the console. 

If you need to close the GUI and stop the program, usually holding Ctrl+C will kill the program in the console/terminal. 

# Misc. Functionalities

### Hints and question timing 

All timings can be manually adjusted in config.txt. By default, after a question is asked, automatically generated hints will trigger (specified by trivia_config.yml). Later, the question will be skipped. After the specified session question limit is hit, the game will end, and a winner will be assigned. 
+ The first hint replaces 2 out of 3 characters in the answer with “_”
+ The second hint replaces all vowels with “_”

### BONUS

Bonus can be activated by an admin via !bonus command. Bonus mode makes questions worth more points, default 3 points (configurable in config.txt). Can be toggled on/off at any time. 

# Commands
The GUI and these commands below will yield the same results. Some commands are available only during active trivia sessions.

#### Admin only:
+ !triviastart - Begins a new trivia round with conditions specified in ‘config.txt’
+ !triviaend - Ends the trivia round & assigns a win. 
+ !next/!skip - Skips to the next question. All questions will automatically lapse after enough time specified in ‘config.txt’, but this allows for manually skipping.
+ !bonus - Switches questions to bonus mode (toggle on/off)
+ !stopbot - Severs the bot connection. 

#### All users:
+ !score - Reports user’s score (reports session score, total score for all trivia, and total wins for all trivia)

# Credits
Thanks to cormac-obrien’s publicly available instructions to navigate Twitch's IRC API via Python & socket, which was used in various forms within this release (http://www.instructables.com/id/Twitchtv-Moderator-Bot/)