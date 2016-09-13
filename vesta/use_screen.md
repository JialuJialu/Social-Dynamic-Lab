#Use Screen to keep your session running when you disconnect

### Keep your SSH session running when you disconnect &
### Keep an IPython/Jupyter Notebook running on the server that can be accessed locally
from [howtogeek.com](http://www.howtogeek.com/howto/ubuntu/keep-your-ssh-session-running-when-you-disconnect/)
and [here](http://aperiodic.net/screen/quick_reference).

Screen is like a window manager for your console. It will allow you to keep multiple terminal sessions running and easily switch between them. It also protects you from disconnects, because the screen session doesn’t end when you get disconnected. This means you can keep a process running on Vesta even if your local machine is
turned off or not connected to the internet. It is particularly useful in combination with Jupyter Notebooks.

Now you can start a new screen session by just typing `screen` at the command line. You’ll be shown some information about screen. Hit `enter`, and you’ll be at a normal prompt.

You are encouraged to give your screen session a name, you can start a new screen with a name by typing
`screen -S name` where `name` is the chosen name.

To **disconnect** (but leave the session running)
Hit `Ctrl + A` and then `D` in immediate succession (i.e. Press and hold Ctrl and type A, release, then hit D).
You will see the message *detached*  

To **reconnect**,
If you only have one screen session running:
Type `screen -DR` *(mnemonic: screen doctor)* and you will automatically reconnect to it.
If you have multiple sessions the command will print out a list of your screen sessions and
you can reconnect to any of them using the command `screen -r name`.

To **quit** you can either type `exit` and hit Enter, type `Ctrl+A` then `:quit`,
or type `Ctrl+A` then `Ctrl+\`, which will ask if you want to quit. This will
permanently close the screen session.

To **view the help** for screen, type `Ctrl + A` and then `Shift+?`.
This will show you the different options available.

#### The rest of the commands are helpful, but optional:

To **reconnect to an existing session**, or create a new one if none exists.
If you have multiple sessions open you will see all of them.
`screen -Dr`

To **create a new window** inside of a running screen session  
Hit `Ctrl + A` and then `C` in immediate succession. You will see a new prompt.

To **switch** from one screen window to another  
Hit `Ctrl + A` and then `Ctrl + A` in immediate succession.
Note the red and blue bar at the bottom of the terminal window. Will show the
window you are currently located in.

To **list** open screen windows  
Hit `Ctrl + A` and then `W` in immediate succession.
