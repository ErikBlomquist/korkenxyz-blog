---
title: "Create A Simple Discord Bot"
date: 2019-08-01T15:38:40-07:00
draft: false
tags: ["discord", "bot"]
---

This guide will show you how to create a simple bot for discord on a ubuntu 16 server. Our bot will be very basic and will reply with a message saying **pong** when we a user types **ping**.


- [Install nodejs and npm](#install-nodejs-and-npm)
- [Create the bot](#create-the-bot)
- [Generate token](#generate-token)
- [Invite the bot](#invite-the-bot)

## Install node.js and npm
We need to install node.js and npm and we can do so by typing the following commands in your command line.

```
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt-get update -y
sudo apt-get install -y nodejs
sudo apt-get install -y npm
```

Once you have installed nodejs and npm successfully, you can check to see that you have the correct version. Note that you need at least nodejs 8.0.0 or higher.
```
nodejs -v
npm -v
```
## Create the bot
We now need to create all of the files we need.

Create a new folder and navigate to it.
```
mkdir ping-pong-bot && cd ping-pong-bot
```

Next step is to initialize our project. A file called package.json will be created for us to easier manage and install dependencies.
```
npm init -y
```

It is now time to install the discord.js dependency. 
```
npm install --save discord.js
```

We now need to create a file that can hold our discord token so that we can connect the bot to discord. We also need a file to hold the code for our bot.
```
touch auth.json && bot.js
```

Copy the code below and paste it onto your auth.json file. I personally like the vi editor so here is how to do it:
```
vi auth.json
```

Copy and paste this into auth.json
```
{
   "token": "YOUR-BOT-TOKEN"
}
```

We also need to edit our bot.js
```
const Discord = require('discord.js');
const client = new Discord.Client();
const auth = require('./auth.json');

client.on('ready', () => {
    console.log(`Logged in as ${client.user.tag}!`);
});

client.on('message', msg => {
  if (msg.content === 'ping') {
    msg.reply('pong');
  }
});

client.login(auth.token);
```

## Generate token 
The token needed for our auth.json file can be generated from Discord's Developer Portal.

1. Head over to
https://discordapp.com/developers/applications/ and click **New Application**, enter a name for your bot and then **Create**.

2. Click on the **Bot** tab on the left hand side and then click **Add Bot** followed by **Yes, do it!**.

3. Next, Click on **Click to Reveal Token** and copy the token. Navigate to your auth.json file and replace the text **YOUR-BOT-TOKEN** with your token.

## Invite the bot
We have created all of the files needed, generated a token and we have written the code for the bot. We now need to invite the bot to our discord server.

1. Navigate back to the discord developer portal. https://discordapp.com/developers/applications
2. Click OAuth2.
3. Scroll down to scopes and bot permission.
4. Select **Send Messages** and **Read Message History** and then copy the URL generated in the **Scopes** section.
5. Paste the url into your browser and select your discord server from the dropdown menu and then click **Authorize**.
6. Voila! Your bot should now have joined your server and should respond with a message saying **pong** when you type **ping**.
