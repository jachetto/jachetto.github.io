---
layout: post
title: "Applescript Viscosity and the lazy ssh connections"
date: 2013-05-23 00:48
comments: true
categories: [osx]
tags: [script, vpn, viscosity]

---

Well , 1st of all: i'm lazy, i'm really a lazy man, so lazy that i'm a developer. And i'm a developer because i can automate most of my jobs.

Now, the picture is i've to manage a couple of servers using ssh. Till now nothing of really hard, yes i've to use my keyboard and write something like "ssh bla bla bla". It's an hard job but i can do it.

Problem: my servers work behind a vpn. So i have to open my vpn client and i've to connect to the vpn server, after this i can start to type "ssh bla bla bla".

Ok i've viscosity, it's a really nice vpn client, and it need just a couple of clicks to connect my vpn server, but it's too much for me!!!
I can't survive to the effort.

So i have wrote this little applescript code:

```applescript
# bin/gotoserver.scpt
tell application "Viscosity"
	set connectionState to "dunno"
	repeat until (connectionState = "Connected")
		if connectionState = "Disconnected" then
			connect "nameofyourviscosityconnection"
		end if
		set connectionState to state of connections where name is equal to "nameofyourviscosityconnection"
		set connectionState to connectionState as string
	end repeat
	tell application "Terminal"
		activate
		if (count of windows) is 0 then
			do script "ssh bla bla vla"
		else
			-- tell application "System Events" to tell process "Terminal" to keystroke "t" using command down
			do script "ssh bla bla bla" in window 1
		end if
	end tell
end tell
```

Looks like easy enough :-) yes you have to substitute "nameofyourviscosityconnection" with the name of your viscosity connection and "bla bla bla"
 with your vpn ip, but i'm sure that you can do it.

The script is self explanatory, it checks if your vpn is connected, if no it open a connection. If a terminal window is open the script use it to run an ssh command, if not, it open a terminal window and run the ssh command.

now, you can open your ~/.bash_profile and add an alias 

```bash
# ~/.bash_profile lang:bash
	alias goserver="osascript ~/bin/gotoserver.scpt"
```

and this is all.

Next time, you have to write in your terminal "goservergo" and you will be connected. 


