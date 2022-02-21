---
title: Debug Remote Heroku Node Server with Visual Code
date: 2022-01-04 11:58:47 +00:00
modified: 2022-01-04 11:58:47 +00:00
tags: [tech]
description: Step by step guide to debug remote heroku node server using Microsoft Visual Code
image: "/assets/images/vnd.png"
---

<figure>
<img src="/assets/images/vnd.png" alt="">
</figure>


1. SSH into **heroku server** from your local machine.


```
heroku ps:exec -a <APP>
```

2. On the **heroku server**, find the `node` process ID (PID) by running `ps aux` or `ps aux | grep -i node` command. In the below sample output, the `PID` is `46`.


```
~ $ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
...
user        46  0.0  0.0 767320 58160 ?        Sl   21:35   0:00 node ./bin/www
...
```

3. On the **heroku server**, run the below command to send `SIGUSR1` signal to the node process to enable its debugger. Check [documentation here](https://nodejs.org/en/docs/guides/debugging-getting-started/).

```
kill -usr1 <PID>
```

4. On your **local machine**, run the port forward command. (Note: Before running the below command, running `lsof -i:9229` should not return any PID. This is to make sure the port is vailable on your local machine.)

```
heroku ps:forward 9229 -a <APP>
```

5. On your **local Machine** VS Code, add this configuration.

```
{
	"type": "node",
	"request": "attach",
	"name": "Remote Heroku: Debug Remote Server",
	"address": "localhost",
	"port": 9229,
	"protocol": "inspector",
	"localRoot": "${workspaceFolder}",
	"remoteRoot": "/app"
}
```

<figure>
<img src="/assets/images/vsconfig.png" alt="">
</figure>

6. On your **local machine**, by running the debug configuration, you would be able to attach to the remote process via `9229` port.

<figure>
<img src="/assets/images/playdebug.png" alt="">
</figure>

7. You can set breakpoints and be able to debug remote heroku node process.