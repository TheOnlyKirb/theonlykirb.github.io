---
title: Basic Mod Commands
date: 2020-08-06 14:56:32
---
Simple, utilizing just the Discord.JS library, nothing fancy. Good for small bots, or beginners.

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
- Replace `.roles.add` with `.roles.remove` to unmute
- Replace `MUTED_ROLE_ID` with the Role ID of the Muted Role

```js
if (!args || !args[0]) return message.channel.send("❌ **Error** \`|\` Please specify a Member to <TYPE>!") 
var member = message.guild.members.cache.get(args[0].match(/\d{17,18}/)[0])
if (!member) return message.channel.send("❌ **Error** \`|\` That Member doesn't exist!") 
member.roles.add("MUTED_ROLE_ID").catch(error => { // add the role to the member
if (error) return message.channel.send("❌ **Error** \`|\` Permissions Error, unable to proceed. Am I **higher** than the Muted role?") 
    }).then(result => {
if (!result.channel) return message.channel.send(`✅ **Success** \`|\` Successfully <TYPE> **${result.user.username}#${result.user.discriminator}**`)
})
```
#### [5-6] Addrole / Removerole
- Replace `<TYPE>` with "Added" or "Removed"
- Replace `.roles.add` with `.roles.remove` to remove the role
```js
if (!args || !args[0] || !args[1]) return message.channel.send("❌ **Error** \`|\` Please specify a Member and then a Role Name!") //addrole @Member Test
var member = message.guild.members.cache.get(args[0].match(/\d{17,18}/)[0])
var role = message.guild.roles.cache.find(role => role.name.toLowerCase() === args[1].toLowerCase()) // sorts through the role names
if (!member) return message.channel.send("❌ **Error** \`|\` That Member doesn't exist!")
if (!role) return message.channel.send("❌ **Error** \`|\` That Role couldn't be found!")
member.roles.add(role.id).catch(error => {
    if (error) return message.channel.send(`❌ **Error** \`|\` Permissions Error, unable to proceed. Am I higher than the \`${role.name}\` Role?`)
}).then(result => {
    if (!result.channel) return message.channel.send(`✅ **Success** \`|\` Successfully <TYPE> the role \`${role.name}\` to **${result.user.username}#${result.user.discriminator}**`)
})
```