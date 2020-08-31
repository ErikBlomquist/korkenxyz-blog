---
title: "Create a Discord Bot in Python"
date: 2020-08-30T12:45:07-07:00
draft: false
tags: ["discord", "python", "redis", ]
---

![Intro Discord + Python](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/intro-img.png)

- [What are we building?](#what-are-we-building?)
- [Pre-requisites](#pre-requisites)
- [Create Discord Bot](#create-discord-bot)
- [Create a Discord Server](#create-a-discord-server)
- [Invite the Bot](#invite-the-bot)
- [Code the Bot](#code-the-bot)

## What are we building?
I will build a relatively simple bot for discord that can respond to specific text command within a discord text channel. The bot will be written in Python 3.7 and it will connect to discord through the API wrapper discord.py. Part 2 will go over how to host the bot on AWS using Terraform to create resources and redis to safely handle our discord bot token.

## Pre-requisites
I expect you to have the following installed before moving forward.
- Python 3.7+ (https://www.python.org/downloads/)
- AWS account (https://portal.aws.amazon.com/billing/signup#/start)
- Discord Developer account (https://discord.com/developers/)

## Create Discord bot
We are going to start by creating a new bot application on discord.
1. Log in using your discord account at https://discord.com/developers/
2. Click **New Application** in the top right corner

    ![discord-step-1](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/discord-new-app.png)

3. Enter a suitable name for your bit and click **Create**.

    ![discord-step-2](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/discord-name-app.png)

4. Click **Bot** in the menu on the left side.

    ![discord-step-3](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/discord-bot-app.png)

    and then **Add Bot** on the right side

    ![discord-step-4](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/discord-addbot.png)

    and of course click **Yes, do it!**

    ![discord-step-5](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/discord-yesadd.png)

5. Click **Copy** to get your secret token and save it in a safe place, we will need it soon.

    ![discord-step-6](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/discord-copy-token.png)


## Create a Discord Server
1. Login to your discord account either via the browser or the client. Scroll down to the botton left and click the green plus sign.

    ![discord-step-7](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/discord-add-srv.png)

2. Click **Create Server**

    ![discord-step-7](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/discord-create-srv.png)

3. Enter a suitable name and click **Create**.

    ![discord-step-7](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/discord-srv.png)

## Invite the Bot
1. Click **OAuth2** when logged in to the developer portal.

    ![discord-inv-1](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/discord-inv-oauth.png)

2. Tick the **bot** box under scopes and then copy and paste the URL at the bottom into your browser.

    ![discord-inv-2](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/discord-inv-url.png)

3. Select the Discord server you created recently and then click **Authorize**.

    ![discord-inv-3](https://raw.githubusercontent.com/ErikBlomquist/korkenxyz-blog/master/content/posts/img/discord-inv-3.png)

## Code the Bot
We are going to create our bot in Python 3.7 and connect it to Discord through Discord.py. We will also use Redis to safely handle our authetication token. Make sure that you have Python 3.7+ installed and pip.

1. Install and configure Discord.py, Redis, Redis-server.
```terminal
$ python3 -m pip install -U discord.py redis redis-server
```

2. Start the redis server
```terminal
$ redis-server
```

3. Configure redis
```terminal
$ redis-cli
```

4. Set Auth Token Variable (Insert the secret token we saved earlier and then Exit with **Ctrl + C**)
```terminal
set 'AUTH_TOKEN' 'insert-secret-token-from-discord-here'
```

5. Create folder and files
```terminal
$ mkdir Discord-Bot && cd Discord-Bot && touch bot.py
```

6. Open bot.py with your favorite editor, I personally use VSCode.
```terminal
$ code .
```

7. Open **bot.py** and import discord and redis
```python
## bot.py
import discord
import redis
```

8. Create a redis connection by adding the following:
```python
redis_server = redis.Redis()
```

9. Next step is to start the Discord client
```python
client = discord.Client()
```

10. Create a variable to hold our secret authentication token from discord
```python
AUTH_TOKEN = str(redis_server.get(‘AUTH_TOKEN’).decode(‘utf-8’))
```

11. We now need to create events so that the bot can determine what to do when messages are sent in the text channel. Add the following events:

```python
# on_ready event will be triggered when the bot is connected.
@client.event 
async def on_ready():
    print(f’{client.user} is now online!’)

```

```python
# This event be triggered every time a message is sent.
@client.event
async def on_message(message):
    print(f”{message.channel}: {message.author}:
    {message.author.name}: {message.content}”)

```

```python
# This event will be triggered for specific messages
@client.event
async def on_message(message):
    # Prevent the bot from responding to its own commands.
    if message.author == client.user:
        return
    # The response the bot should send when detecting the words meow in text chat.
    if message.content.upper() == ‘meow’: 
        await message.channel.send(‘I am a cat 😻’)

```
12. Now when we have the events ready, we need to get the **AUTH_TOKEN** from Redis.
```python
# Get discord token from redis
client.run(AUTH_TOKEN)
```

13. Save the file and exit. It is now time to test it. To run the bot:
```
$ python3 bot.py
```

14. Your bot should now be online on discord. If it is online, type **meow** in the chat and the bot should respond.


## What's next?
Part 2 & 3 will go over the following:
- Install Terraform + AWS CLI
- Bot hosted on AWS
- Resources created with Terraform

- Use Ansible to remotely manage the EC2 instance.
- Create Lambda to start and stop the instance on a schedule.
- Monitor the bot
