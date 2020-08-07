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
