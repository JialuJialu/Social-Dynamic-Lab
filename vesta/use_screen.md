#Use Screen to keep your session running when you disconnect

### Keep Your SSH Session Running when You Disconnect
from [howtogeek.com](http://www.howtogeek.com/howto/ubuntu/keep-your-ssh-session-running-when-you-disconnect/)

Screen is like a window manager for your console. It will allow you to keep multiple terminal sessions running and easily switch between them. It also protects you from disconnects, because the screen session doesn’t end when you get disconnected.

Now you can start a new screen session by just typing screen at the command line. You’ll be shown some information about screen. Hit `enter`, and you’ll be at a normal prompt.

To **disconnect** (but leave the session running)
Hit `Ctrl + A` and then `Ctrl + D` in immediate succession. You will see the message *detached*  

Each time you **reconnect**, type  
`screen -DR` *(mnemonic: screen doctor)*    
to force any of your screen sessions to detach from whatever terminal they are on and reattach to your active screen session.

To **view the help** for screen, type `Ctrl + A` and then `Ctrl + \`  
(Press and hold control and type a then \ )

#### The rest of the commands are helpful, but optional:
To reconnect to an already running session
`screen -r`  

To **reconnect to an existing session**, or create a new one if none exists  
`screen -Dr`

To **create a new window** inside of a running screen session  
Hit `Ctrl + A` and then `C` in immediate succession. You will see a new prompt.

To **switch** from one screen window to another  
Hit `Ctrl + A` and then `Ctrl + A` in immediate succession.

To **list** open screen windows  
Hit `Ctrl + A` and then `W` in immediate succession