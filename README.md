# Zemyna

Zemyna acts as a kind of proxy, it makes the client connect to a special
local server that forwards all packets from the client to the actual server.
This way it's able to read every single packet that gets sent or received.
These packets can then be passed to other applications, so they can work with
them, e.g. loggers like [PaleTree](https://github.com/exectails/PaleTree).

## How to use

For Zemyna to be able to read packets, it needs control over the starting
process, as it temporarily adjusts your client, to tunnel its connection.
It does so by making a backup of your client.xml, that contains information
about where to connect to, and creates a new one. This change is reverted
once Zemyna closes, this way you don't have to fiddle with any files yourself.

To tell Zemyna where to connect the client to, you use command line parameters.
For example, let's say you wanted to log packets on the international server
Laima, you would simply start it like this from the command line, after moving
Zemyna to your game folder:

`Zemyna.exe host:54.88.25.242 port:2000`

The IP and port are of the login server for Laima. You can look up the host
and port for your target server in the official server list. The URL to it
can be found in the client.xml.

For easier reusability, you should create a link or bat file. Zemyna will
then take the necessary steps to be able to read packets from the network,
and start the client. A small window in the upper left corner will show you
that Zemyna is running, it closes automatically when the client gets closed.
You can also double click it to quickly close Zemyna and the client.

Since you can only have one server in the server list this way, the one you
specify in the parameters, you could create multiple links/bats, one for each
server.

If you want to use Zemyna on a server that doesn't use the Steam auto-login,
you need to supply Zemyna with a static\_\_Conf.txt, where the `UseSteamClient`
value is set to `NO`.

```
ServiceNation=GLOBAL
Dictionary=YES
UseNexonSSO=NO
UseNexonGLM=NO
UseHackshield=NO
UseSteamClient=NO
UseXigncode=NO
UseNISMS_TESTURL=NO
UseNISMS_ONLY_OFFER=YES
```

If the file is on your local web server, in "/toslive/patch", you would make
Zemyna use it like this:

`Zemyna.exe host:127.0.0.1 port:2000 staticconfig:http://127.0.0.1/toslive/patch/`

Note that the client access the file via SSL (HTTPS), so configure your web
server accordingly.

## How to connect to Zemyna

Zemyna uses the same API as the tool it was inspired by, "Alissa". It uses
WM_COPY messages to communicate between Zemyna's and the subscriber's window.
To subscribe to Zemyna, to receive packets, you send the "op" (dwData) `100`
to Zemyna's window (window name: "mod_Tossa"), to unsubscribe, you send `101`.
While you're subscribed, you receive all incoming (op `0x10101012`) and
outgoing (op `0x10101011`) packets via the same method.

For an actual example on how this works, I suggest looking at the corresponding
[functions in Pale](https://github.com/exectails/PaleTree/blob/master/PaleTree/FrmMain.cs#L561).
Alternatively you can also create a plug-in for Pale, which will be easier.

## Restrictions

- Zemyna currently only supports the Login and Channel servers.
- Due to this game's protocol, Zemyna's opcodes need to be updated regularly,
or there's a high chance for it not to function correctly. Check for updates
after each game patch and report potential issues, or update the opcodes text
file yourself.

## Korean TOS

If you check the releases you will find multiple versions, one of them being
for kTOS (notice the "k" prefix). Choose that one if you want to log packets
on the Korean server.

Due to kTOS using a web login, we had to do things a little differently for
the Korean version of Zemyna. You need an additional argument, "kr", which
activates certain KR only options. Example:

`Zemyna.exe host:54.88.25.242 port:2000 kr nosteam`

After you start Zemyna like that, the client won't start automatically, but it
will still modify the client files, so when you finally start the client via
the web login, the connection will still go through Zemyna, allowing you to log
packets using PaleTree.
