---
title: discordgo 库
date: 2024-08-11
author: 付辉

---


代码仓库：https://github.com/bwmarrin/discordgo

> Construct a new Discord client which can be used to access the variety of Discord API functions and to set callback functions for Discord events.

```go
// 模式
s, err = discordgo.New("Bot " + *BotToken)

s.AddHandler(func(s *discordgo.Session, i *discordgo.InteractionCreate) {
	if h, ok := commandHandlers[i.ApplicationCommandData().Name]; ok {
		h(s, i)
	}
})
```

![[Pasted image 20240811221149.png]]