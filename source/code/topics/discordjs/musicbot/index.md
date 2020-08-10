---
title: Advanced Bot Setup
date: 2020-08-06 15:15:43
---

## Welcome!
In this tutorial we take the code from the [Advanced Bot Tutorial](/code/topics/discordjs/advancedbot) and add onto it to create a Music feature set. 

It is entirely possible to integrate this code into a bot that doesn't follow the tutorial, in fact- it's been written to explain very clearly where the files and code should go - however it may still be helpful to view the Advanced Bot Tutorial to ensure your bot is setup in a proper way, or so that you understand where the tutorial is referencing.

**Without** further ado, let's get into it!
___
## Prerequisites
There are a few modules we need to install with NPM first, we need [FFmpeg](https://www.npmjs.com/package/ffmpeg)\*, [Fluent-ffmpeg](https://www.npmjs.com/package/fluent-ffmpeg), [@discord.js/opus](https://www.npmjs.com/package/@discordjs/opus), and lastly [ytdl-core](https://www.npmjs.com/package/ytdl-core)

You can install them all with 
```bash
npm install ffmpeg fluent-ffmpeg @discordjs/opus ytdl-core --save
```

Now we must ensure FFmpeg is installed on the OS the bot is running on. For Debian based Linux Distros it is as easy as doing `sudo apt install ffmpeg` but for Windows users it is a bit more complicated.

For windows users you must first download FFmpeg from [the official downloads page](https://ffmpeg.zeranoe.com/builds/).
From here you must do the following to ensure FFmpeg is registered
- Extract the download into a location of your choice
- Rename the extracted folder to `FFmpeg` for clarity
- Go to `Control Panel > System and Security > System > Advanced System Settings`
- From here, click on `Environment Variables` in the Advanced Settings window
- Double click on the `Path` value in the table shown
- Click `new` to add a new variable to the path
- Add your FFmpeg folder path into the table and click `ok`
- Restart your command line/terminal, and FFmpeg should be installed!

Once FFmpeg is installed, you should be all set to move on!
___
## Bot Setup
We will need to add some new properties to the Client. To do this in a clean and future proof way, we will be storing the properties in a separate file, and requiring them in the `index.js` file we created in the Advanced Bot. For those not following from that tutorial, this should be the launching point for your bot, or where you store the Client creation and login.

To do this we should create a new folder under `/bot/connectors`, in other words, create a folder called `connectors` under the main folder for your bot. Make sure it is outside the commands and events folder!

Once you have done that, we need to create a `music.js` file to store the properties in. For this tutorial, we will use the following code in the `music.js` file
```js
module.exports = (Client) => {
    Client.music = {} // base object
    Client.music.queue = new Map() // a Map to store server queues
    Client.music.servers = new Map() // a Map to store server data/settings
}
```
This code ensures we can use `require` to attach the properties to the Client. We will have a queue Map, and a servers Map to hold data.
Once you have the `music.js` file setup, we should go ahead and require it to "connect" it to the Client. Inside your `index.js` file place the following code
```js
require("./connectors/music.js")(Client)
```
This passes in the Client to the `music.js` file! Now your properties exist on the bot and we can continue!

For reference, your folder structure should look something like this in the end.
![File Tree](../../../../assets/musicbotstructure.PNG)
___
## Play Command
So now it's time to get to playing music! Lets go ahead and make a `music` commands folder and category

WIP - Will be finished later