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


From your local machine, SSH into the **heroku server**

```
heroku ps:exec -a <APP>
```

**On the heroku server**, find the `node` process ID (PID) by running `ps aux` command

```
~ $ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
...
u536        46  0.0  0.0 767320 58160 ?        Sl   21:35   0:00 node ./bin/www
...
```

**On heroku server**, run the below command to send a signal to the process to let it know to enable its debugger. Check (documentation here)[https://nodejs.org/en/docs/guides/debugging-getting-started/].

```
kill -usr1 <PID>
```

**On local machine**, run the port forward command. (Note: Before running the below command, `lsof -i:9229` should not return any PID.)

```
heroku ps:forward 9229 -a <APP>
```

**On local Machine** VS Code, add this configuration.

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

**On local machine**, By running the debug configuration, you would be able to attach to the remote process via `9229` port.

You can set breakpoints and be able to debug remote heroku node process.