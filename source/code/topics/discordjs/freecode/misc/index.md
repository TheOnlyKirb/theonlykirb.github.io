---
title: Misc Commands
date: 2020-08-06 14:56:32
---
Misc commands you may find useful. Some require a `get` or `post` request which I find the module `node-fetch` to be useful for.

#### [1] Hastebin
- Replace `<TYPE>` with "Kicked", "Banned", or "Unbanned"
- Replace `.kick` with `.ban` for bans, and `.unban` for unbans

```js
const http = require("node-fetch")
if (!args || !args[0]) return message.channel.send("❌ **Error** \`|\` Please specify a text to post to hastebin!") // check if the user provides an argument
http("https://hastebin.com/documents", {method: "POST", body: `${args.join(" ")}`}).then(res => res.json()).then(json => message.channel.send(`https://hastebin.com/${json.key}`))
```
#### [2] Password Generator
- Do **not** store user generated passwords, **ever** with this command
- Acceptable times to store it? When it is being salted **and** hashed. 

```js
message.author.send(`✅ **Generated** \`|\` ${Array(15).fill("0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz$%&*^()!@=+-_?").map(function(mapped) { return mapped[Math.floor(Math.random() * mapped.length)] }).join('')}`).catch(error => { // try to dm the author
    if (error) return message.channel.send("❌ **Error** \`|\` Unable to send a DM to you.")
})
message.delete() // delete the users command
```
#### [3] Reverse Text
```js
if(!args || !args[0]) return message.channel.send("❌ **Error** \`|\` Please specify text to reverse!")
return message.channel.send(args.join(" ").split("").reverse().join(""))
```
#### [4] Mock
- Doesn't require user input! Grabs the last message sent and mocks it!
- Beware, this works weirdly on embed content because of the DiscordAPI

```js
message.channel.messages.fetch({ limit: 2 }).then(found => {
    var obtainedMsgs = Array.from(found)
    if(!obtainedMsgs[1] || !obtainedMsgs[1][1]) return message.channel.send("❌ **Error** \`|\` No message to mock available!")
    else {
        message.delete()
        return message.channel.send(`😔 ${obtainedMsgs[1][1].content.split('').map(char => Math.random() > 0.5 ? char.toUpperCase() : char.toLowerCase()).join('')}`)
    }
})
```
#### [5] New Subreddit List
- For more results, change `?limit=5`
- NSFW subreddits will only show in a NSFW channel

```js
var http = require("node-fetch")
http(`https://reddit.com/subreddits/new/.json?limit=5`).then(res => res.json()).then(json => {
    let subs = ""
    json.data.children.forEach(sub => {
        if(sub.data.over18 && !message.channel.nsfw) `➤ Omitted [18+]\n`
        else subs += `➤ **<https://reddit.com/${sub.data.display_name_prefixed}>**\n`
    })
    return message.channel.send(`📝 **New Subreddits**\n\n${subs}\n_Provided by the Reddit API_`)
})
```
#### [5] r/AskReddit
- Only retrieves 1 random post

```js
var http = require("node-fetch")
http(`https://reddit.com/r/AskReddit/random/.json?limit=1`).then(res => res.json()).then(json => {
    return message.channel.send(`📣 **r/AskReddit** \`|\` ${json[0].data.children[0].data.title}`)
})
```
#### [6] r/Memes
- Retrieves 1 Random meme

```js
var http = require("node-fetch")
http(`https://reddit.com/r/Memes/random/.json?limit=1`).then(res => res.json()).then(json => {
    return message.channel.send(`😆 **r/Meme**\n**Title:** ${json[0].data.children[0].data.title}\n${json[0].data.children[0].data.url}`)
})
```
