---
title: Discord.JS Command / Feature Directory
date: 2020-08-06 14:56:32
---
# What Is This?
When I was first learning about NodeJS, viewing others code **and** _learning_/_playing_ with it was a great help in growing my skills. I know I was not alone in this, and as such I'd like to give back in my own way to the same community that taught me what I know today. Here I publish random commands and for developers to easily integrate into their bot applications.

### Terms
Don't use the commands/code here to be stupid, be mindful of how you use code and how you modify it. Don't abuse the DiscordAPI for example. Other than that, feel free to either learn from the code if it's new to you, **or** pop it into your own application. Totally up to you!

### Assumptions
All code listed here assumes you have a basic setup that allows for the following to be passed in:
- Bot/Client
- Message Event Data / Varying Event Data
- User Arguments
- User Argument assumption example: `args[0]` would represent the **first** user argument

If this is unknown to you, please reference the Advanced Bot Creation tutorial (not available yet) that I have listed within the main directory for this topic.
___
# Basic Mod Commands

#### [1-2] Kick / Ban
- Replace `<TYPE>` with "Kicked" or "Banned"
- Replace `.kick` with `.ban` for bans

```js
if (!args || !args[0]) return message.channel.send("❌ **Error** \`|\` Please specify a Member to <TYPE>!") // check if the user provides an argument
var member = message.guild.members.cache.get(args[0].match(/\d{17,18}/)[0]) // checks if the argument is a member id, this works with pings or pasting a member id
if (!member) return message.channel.send("❌ **Error** \`|\` That Member doesn't exist!") // if the member doesn't exist, say so
member.kick().catch(error => { // attempt to kick the member
if (error) return message.channel.send("❌ **Error** \`|\` Permissions Error, unable to proceed.") // if it fails, say so
    }).then(result => { // take the result of the catch - if it contains channel data, it failed
if (!result.channel) return message.channel.send(`✅ **Success** \`|\` Successfully <TYPE> **${result.user.username}#${result.user.discriminator}**`) // if it didn't fail, let them know it was successful
})
```
#### [3-4] Mute / Unmute
- Replace `<TYPE>` with "Muted" or "Unmuted"
- Replace `.addrole` with `.removerole` to unmute
- Replace `MUTED_ROLE_ID` with the Role ID of the Muted Role

```js
if (!args || !args[0]) return message.channel.send("❌ **Error** \`|\` Please specify a Member to <TYPE>!") 
var member = message.guild.members.cache.get(args[0].match(/\d{17,18}/)[0])
if (!member) return message.channel.send("❌ **Error** \`|\` That Member doesn't exist!") 
member.addrole("MUTED_ROLE_ID").catch(error => { // add the role to the member
if (error) return message.channel.send("❌ **Error** \`|\` Permissions Error, unable to proceed. Am I **higher** than the Muted role?") 
    }).then(result => {
if (!result.channel) return message.channel.send(`✅ **Success** \`|\` Successfully <TYPE> **${result.user.username}#${result.user.discriminator}**`)
})
```