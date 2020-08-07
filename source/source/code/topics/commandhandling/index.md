---
title: Command Handling
date: 2020-08-07 15:41:25
---

## Prerequisites

In order to follow this tutorial, you should:
- know the basics of javascript and [node.js](https://nodejs.org/), and;
- have done atleast the [basic bot guide](/source/code/topics/basicbot), and;
- a bot that successfully logs in and listens to events.

## Code you should already have

Your bot should look **similar**, most likely ***not the same***, to this little snippet:

```js
const Discord = require("discord.js")
const Client = new Discord.Client({
    fetchAllMembers: false,
    disableEveryone: true, 
    disabledEvents: [],
    http: { api: 'https://discordapp.com/api', version: 7 }, 
    shardCount: 1 
})
Client.prefix = "!"
const ballResponses = ["Yes.", "No.", "Outlook not so good.", "Ask again later."]
Client.on("message", async msg => { 
    if(!msg.content.startsWith(prefix) || msg.channel.type == "dm" || msg.author.bot || !msg.channel.permissionsFor(msg.guild.me).has("SEND_MESSAGES")) return // Stops the bot from replying to messages that don't start with the prefix, are in a dm, are from a bot, or in channels the bot can't speak in.
    let args = msg.content.split(" ")
    let cmd = args.shift().slice(prefix.length)
    if(cmd == "help"){
      return await msg.channel.send("Commands are: `help`, `ping` and `8ball`")
    }else if(cmd == "ping"){
      return await msg.channel.send("Pong!")
    }else if(cmd == "8ball"){
      if(args.length == 0) return await msg.channel.send("I can't predict nothing!")
      return await msg.channel.send(ballResponses[Math.floor(Math.random() * ballResponses.length)])
    }
})
Client.login("TOKEN_HERE")
```

This is a very basic bot with 2 commands and a help command. However, these commands are in our main file which can lead to very messy code. Today we'll look at how to split these commands into categories and into files to make the bot's code much cleaner.

## New stuff

This will work for any number of categories and is generalized so you can make your own commands and I encourage you to play around with it.

This will involve looking into our bot's file structure. Normally the above code is in a file called `index.js`, however it can be whatever you want it to be. Right now, our file structure looks simple, and probably a bit like this:

![File structure](https://oliy.is-just-a.dev/1ncvo_5463.png)

Although this works, as mentioned earlier, this can get messy and inefficient. It's not modular or generalized, so adding new commands would be a hassle. Splitting your commands into seperate files (often called modules) can solve these problems.

I have 3 commands, so I'll be making 3 modules, and 2 categories (general and fun). You can make whatever you wish, however, which is the beauty of generalization and modulation.

In your project's root folder (the folder where you run your start command `node index.js` most of the time from) make a new folder and call it `commands`. Inside of this folder, will be other folders for categories and inside those will be js files which are our commands:

![File creation](https://oliy.is-just-a.dev/gpt1jo_5464.gif)

It's recommended to leave these lowercase, along with all your command's file names as well to make them case-insenitive later.

After you make the folders, we'll be jumping into our `index.js` file and modifying some code. Open your main file with your favorite code editor, and if you don't already have one, make a `ready` event listener:

```js
Client.on("ready", () => {
    console.log("I'm ready!")
})
```

This is a very basic listener and although it doesn't matter where the code we'll be putting in goes inside this listener, putting it at the top is usually the best. To do what we want, we'll need some way to read folders, to achieve this we use node's built in [File System](https://nodejs.org/api/fs.html) module. You should add this to the top of your main file:

```js
const fs = require("fs")
```

This imports File System's functionality so we can use it. Something we should also do now is make our `commands` and `aliases` collection. After you require discord.js and make your client, add these lines:

```js
const fs = require("fs")
const Discord = require("discord.js")
const Client = new Discord.Client({
    fetchAllMembers: false,
    disableEveryone: true, 
    disabledEvents: [],
    http: { api: 'https://discordapp.com/api', version: 7 }, 
    shardCount: 1 
}) 
// The above lines (or similar) should already exist in your code and you shouldn't need them.
Client.commands = new Map()
Client.aliases = new Map()
```

We JavaScript's built in [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) class to store our commands and aliases. We also put them on to our Client to have access to them everywhere. Now we'll begin to actually use the new code. In our **ready event listener** we'll read the commands directory, then we'll read the directories within and load them into our bot. However first we need commands, in order to gain access to other file's functions in node, we need to use `module.exports` this allows us to export functions and other values to be `require`d in other files (it's basically an object that when you use `require` on the file returns what you export). This is actually very simple and not hard to do. Open one of your category folders and make a new file, it can be called whatever you want, but the name of it will be the command name. I recommend keeping it lowercase for case-insensitivity. Earlier, I made a commands folder and 2 category folders, fun and general. Inside of the general folder, I'll be putting my help command and ping, inside of the fun folder will be our small 8ball command.

Let's get started by creating a file in our `general` category folder called `ping`. Inside of this we will be using `module.exports`. I like to make 2 exports: `run` and `help` however, these are just variables and can be called whatever you wish, but the structure must be the same in all commands.

In my ping.js file I'll add this:

```js
module.exports.run = async (client, message, args) => {

}

module.exports.aliases = {

}

module.exports.help = {

}
```

Here I created a `run` arrow function on our exports, an `aliases` object, and a `help` object. However, they are empty and don't do anything, so this is where we actually add back the code we already had. Our ping command is a really simple one-line:

```js
module.exports.run = async (client, msg, args) => {
    return await msg.channel.send("Pong!")
}
```

Now, if we manually `require` the file we can execute the run function: `require("commands/general/ping.js").run(Client, msg, args)`. This does need to be ran in the `message` event listener though. But this isn't actually how we want to load commands, that will come after we have added all of our commands. Now we'll edit the `help` object; this allows us to generalize our help command and allows making new commands easy and automatically add to the help page.

```js
module.exports.run = async (client, msg, args) => {
    return await msg.channel.send("Pong!")
}

module.exports.aliases = {

}

module.exports.help = {
    description: "Ping!",
    usage: "ping",
    category: "general"
}
```

As you can see, the help object is really simple, the only real thing you need to be sure of is the `category` should be the same as the folder it's in, and `usage` should start with the name of the file (in my case, the fine is `ping.js` so my usage starts with `ping`). When it comes to aliases, there are plenty of ways you can do it, the most simple, I believe is exporting it as its own object. There are usually 2 things we add to it `aliases`, an array of aliases, and `command`, the name of the file:

```js
module.exports.run = async (client, msg, args) => {
    return await msg.channel.send("Pong!")
}

module.exports.aliases = {
    aliases: ["p"],
    command: "ping"
}

module.exports.help = {
    description: "Ping!",
    usage: "ping",
    category: "general"
}
```

It's important that `command` is the name of the file without the extension (we handle that later) so the bot can get the right command.

Now we'll add our other command really quick with less explaination because it's generally the same:

```js
const ballResponses = ["Yes.", "No.", "Outlook not so good.", "Ask again later."]
module.exports.run = async (client, msg, args) => {
    if(args.length == 0) return await msg.channel.send("I can't predict nothing!")
    return await msg.channel.send(ballResponses[Math.floor(Math.random() * ballResponses.length)])
}

module.exports.aliases = {
    aliases: ["8b"],
    command: "8ball"
}

module.exports.help = {
    description: "Shake the magic 8ball for insight.",
    usage: "8ball [question]",
    category: "fun"
}
```

It's basically all the same, but this file is in the `fun` category folder and does different things. Now we can actually get to loading our commands in the `ready` event listener. As stated earlier, we'll be using node's `fs` to read the directories. I recommend you learn what the function we'll be using does.


```js
Client.on("ready", () => {
    fs.readdir("commands", (err, categories) => {
        if(err) return console.error(err) // Log error to console
        if(categories.length == 0) return console.info("No categories to load.")
        // Because categories is an array of strings of the names of files in the folder, we will iterate over them and load their contents
        for(let category of categories) { // A simple for ... of loop to loop over an array
            fs.readdir(`commands/${category}`, (err2, commands) => { // We know that these folders are in the commands directory, so we can easily read them too.
                if(err2) return console.error(err2)
  
                let jsfiles = commands.filter(f => f.split(".").pop() === "js") // ensure we're only getting js files
            
                if(jsfiles.length <= 0){
                  return console.info(`Nothing loaded from ${category}.`)
                }
            
                jsfiles.forEach((f, i) => {
                  try{
                    let props = require(`./commands/${category}/${f}`) // Require the file to get access to its exports
                    Client.commands.set(f, props) // Map the contents of the exports to the file name
                    Client.aliases.set(f, props.aliases) // Same with the aliases
                  }catch(e){console.error(e)}
                })
            })
        }
    })
    console.log("I'm ready!")
})
```

Now our commands will successfully load into our bot! Hurray! But now what? This isn't it, we still need to get the commands in our `message` event listener and execute them:

```js
Client.on("message", async msg => { 
    if(!msg.content.startsWith(Client.prefix) || msg.channel.type == "dm" || msg.author.bot || !msg.channel.permissionsFor(msg.guild.me).has("SEND_MESSAGES")) return
    let args = msg.content.split(" ")
    let cmdString = args.shift().slice(Client.prefix.length).toLowerCase() // Get our command string
    let cmd = Client.commands.get(`${cmdString}.js`) // Try to get the command, if it exists
    
    if(cmd){
        try{
            cmd.run(Client, msg, args) // Run the command if it exists
        }catch(e){console.error(e)}
    }else{
        let alias = Client.aliases.find(a => a.aliases.includes(cmdString)) // Try to get the command with the alias provided
        if(alias){
            let command = Client.commands.get(`${alias.command}.js`) // Get the command from the aliases command
            if(command){
                command.run(Client, msg, args) // Run the command if it exists
            }
        }
    }
})
```

This will work for any commands you make as long as they have a `module.exports.run`.

## Making a generalized help command

Now that all of your commands are modular and in separate files, making a help command work for any current and future commands is a piece of cake. You can format these however you want, but most people use embeds. Today I'll show you how to make one that looks like this: 

![Help command example](https://oliy.is-just-a.dev/311ljd_5481.png)

You'll also be able to get help on specific commands:

![8ball help example](https://oliy.is-just-a.dev/ifvn2g_5483.png)

and categories:

![General help example](https://oliy.is-just-a.dev/mfzd8k_5482.png)

Let's get started. This command belongs in the general category, so we'll create the file in the general folder. First, we want to make the basic command structure:

```js
const Discord = require("discord.js")
module.exports.run = async (client, msg, args) => { 

}

module.exports.aliases = {
    aliases: [],
    command: "help"
}

module.exports.help = {
    description: "Get the help page.",
    usage: "help (command OR category)",
    category: "general"
}
```

You can see I also required `discord.js` this is because we'll be using an embed. We want to get our categories at runtime because you may reload commands and make new categories, that's a topic for a different day though.

```js 
module.exports.run = async (client, msg, args) => { 
    let categories = []
    for (let command of client.commands) { // loop over our commands
        if (!command.help) continue // skip it if it doesnt have a help
        if (!categories.includes(command.help.category)) categories.push(command.help.category) // don't add categories that already exist
    }
}
```

This is required if you want your users to be able to get help based on category, but that comes after we handle no arguments given, like so:

```js 
module.exports.run = async (client, msg, args) => { 
    let categories = []
    for (let [file, command] of client.commands) { // loop over our commands
        if (!command.help) continue // Skip it if it doesnt have a help
        if (!categories.includes(command.help.category)) categories.push(command.help.category) // Don't add categories that already exist
    }
    if (args.length < 1) { // If no arguments are given
    let commands = {} // Initialize command object
    for (let [file, command] of client.commands) { // Loop over commands
        if (!command.help) continue // Skip if no help object
        if (!commands[command.help.category]) commands[command.help.category] = [] // We sort by category, so if we havent gotten a command with this category yet, create it
        commands[command.help.category].push(file.split(".").slice(0, -1).join(".")) // Push the command's name
    }
    let embed = new Discord.MessageEmbed() // Make a new embed
    embed.setTitle("Command List")
    embed.setDescription(`A list of commands.\nGet help for a specific command or category by using ${client.prefix}help [command/category]`)
    for (let category in commands) {
        embed.addField(`**${category}**`, "`" + commands[category].join("` `") + "`") // Add the field, you may need to split yours up if you have a lot of commands
    }
    embed.setColor("#ff3e09")
    return await msg.channel.send(embed) // Send it
  }
}
```

This will make a command list by category, but you can't get help on specific categories or commands, so we'll implement categories first.

```js
module.exports.run = async (client, msg, args) => { 
    let categories = []
    for (let [file, command] of client.commands) { // loop over our commands
        if (!command.help) continue // Skip it if it doesnt have a help
        if (!categories.includes(command.help.category)) categories.push(command.help.category) // Don't add categories that already exist
    }
    if (args.length < 1) { // If no arguments are given
        let commands = {} // Initialize command object
        for (let [file, command] of client.commands) { // Loop over commands
            if (!command.help) continue // Skip if no help object
            if (!commands[command.help.category]) commands[command.help.category] = [] // We sort by category, so if we havent gotten a command with this category yet, create it
            commands[command.help.category].push(file.split(".").slice(0, -1).join(".")) // Push the command's name
        }
        let embed = new Discord.MessageEmbed() // Make a new embed
        embed.setTitle("Command List")
        embed.setDescription(`A list of commands.\nGet help for a specific command or category by using ${client.prefix}help [command/category]`)
        for (let category in commands) {
            embed.addField(`**${category}**`, "`" + commands[category].join("` `") + "`") // Add the field, you may need to split yours up if you have a lot of commands
        }
        embed.setColor("#ff3e09")
        return await msg.channel.send(embed) // Send it
    }else if(categories.includes(args.join(" "))) {
        let commands = [] // Create a commands array
        let category = args.join(" ") // Saves a bit of memory at run time so we don't join every time we loop
        for (let [file, command] of client.commands) { // Loop over commands
            if (!command.help) continue // Skip if no help
            if (command.help.category != category) continue // Skip if the category isn't the same
            commands.push(file.split(".").slice(0, -1).join(".")) // Add the command's name to the array
        }
        let embed = new Discord.MessageEmbed()
        embed.setTitle("Command List")
        embed.setDescription(`A list of ${category} commands.\nGet help for a specific command or category by using ${client.prefix}help [command/category]`)
        embed.addField(`**${category}**`, "`" + commands.join("` `") + "`")
        embed.setColor("#ff3e09")
        return await msg.channel.send(embed)
    }
}
```

And now you should be able to get help on any category. Now we want to do specific commands, this one is a tiny bit different:

```js
module.exports.run = async (client, msg, args) => { 
    let categories = []
    for (let [file, command] of client.commands) { // loop over our commands
        if (!command.help) continue // Skip it if it doesnt have a help
        if (!categories.includes(command.help.category)) categories.push(command.help.category) // Don't add categories that already exist
    }
    if (args.length < 1) { // If no arguments are given
        let commands = {} // Initialize command object
        for (let [file, command] of client.commands) { // Loop over commands
            if (!command.help) continue // Skip if no help object
            if (!commands[command.help.category]) commands[command.help.category] = [] // We sort by category, so if we havent gotten a command with this category yet, create it
            commands[command.help.category].push(file.split(".").slice(0, -1).join(".")) // Push the command's name
        }
        let embed = new Discord.MessageEmbed() // Make a new embed
        embed.setTitle("Command List")
        embed.setDescription(`A list of commands.\nGet help for a specific command or category by using ${client.prefix}help [command/category]`)
        for (let category in commands) {
            embed.addField(`**${category}**`, "`" + commands[category].join("` `") + "`") // Add the field, you may need to split yours up if you have a lot of commands
        }
        embed.setColor("#ff3e09")
        return await msg.channel.send(embed) // Send it
    }else if(categories.includes(args.join(" ").toLowerCase())) {
        let commands = [] // Create a commands array
        let category = args.join(" ").toLowerCase() // Saves a bit of memory at run time so we don't join every time we loop
        for (let [file, command] of client.commands) { // Loop over commands
            if (!command.help) continue // Skip if no help
            if (command.help.category != category) continue // Skip if the category isn't the same
            commands.push(file.split(".").slice(0, -1).join(".")) // Add the command's name to the array
        }
        let embed = new Discord.MessageEmbed()
        embed.setTitle("Command List")
        embed.setDescription(`A list of ${category} commands.\nGet help for a specific command or category by using ${client.prefix}help [command/category]`)
        embed.addField(`**${category}**`, "`" + commands.join("` `") + "`")
        embed.setColor("#ff3e09")
        return await msg.channel.send(embed)
    }else { // This is if there are args still
        let command = client.commands.get(args.join(" ").toLowerCase() + ".js") // Get a command
        command = command ? command.help : null // Check if help exists, if it does set command to command.help or null
        let command = client.commands.get(args.join(" ").toLowerCase() + ".js") // Get a command
      if (command.help) {
          let embed = new Discord.MessageEmbed() // Create the embed
          .setTitle(args.join(" ").toLowerCase())
          .addField("Description", command.help.description) // Set the description
          .addField("Usage", `${client.prefix}${command.help.usage}`) // Set the usage
          .addField("Aliases", command.aliases && command.aliases.aliases.length > 0 ? client.prefix + command.aliases.aliases.join(`\n${client.prefix}`) : "None") // Set the aliases
          .addField("Category", command.help.category) // Set the category
          .setColor("#ff3e09")
          return await msg.channel.send(embed) // Send the embed
        } else {
            return await msg.channel.send("Command not found") // If there is nothing else, and there are args, tell them that the command wasn't found.
        }
    }
}
```

With these finishing touches, you now have a help command that will always update with your new commands and categories.

## Final words

This guide is here to help with a basic command handler. It is made generic on purpose, it is up to you to modify how commands look and make new commands and categories for your bot and keep the code clean. What should you not do? **You should not copy this letter for letter**. This guide isn't made for copying, it's made for generalization to help with anyone's bots. The commands are basic because I want you to make your own and expand on the concept of having a command handler.