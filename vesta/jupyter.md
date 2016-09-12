# Using Jupyter Notebooks with Vesta

Jupyter Notebooks can be used as a way to access files & run code in Vesta.
It is a particularly useful way of doing exploratory data analysis and of
writing and debugging code, both due to the functionality of the notebooks and
because it allows you to see your files and write code in the browser on your
local machine.

## On Vesta:

You must first open up Jupyter Notbooks in headless mode on the server using
the command: `jupyter notebook  --no-browser --port 8888`

IMPORTANT: The `8888` is a four-digit port number. To avoid port conflicts we
are setting up a document where you can each claim a port number. If multiple
users use the same port then it will result in conflicts. The number itself is
arbitrary but must be the same in all of these commands.

## On your local machine:

You can now connect to the Notebook on your local machine using an ssh tunnel.

To connect to the notebook use the command:
`ssh -i ~/.ssh/your_private_key -f -N -L localhost:8888:localhost:8888 your_net_id@vesta.soc.cornell.edu`

You can then open up your browser and type `http://localhost.8888` to open the notebook
or alternatively if you are a Mac or Linux user type `open http://localhost.8888` into the command line to
automatically open it in your default browser.

You will see the directory where you ran the command on Vesta and can access files
as if you are logged into Vesta.

To help to speed this process up you can save these two commands in a text file and
run it as a shell script. You will need to change the permissions to make it
executable using `sudo chmod 755 file_name` and enter your password.

## Using in combination with screen:
To use it most efficiently you should also familiarize yourself with `screen`.
This will allow you to keep your notebook running and maintain your local
connection to Vesta even when you are not connected in the command line.
All you need to do is open a screen session on vesta, run the command to start
the headless notebook, and then detach from the screen session. This will
keep the headless notebook running in the background. It is still recommended
that you frequently save your work as any interruption of Vesta service may
cause your screen session to end.
