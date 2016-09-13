#Chroot Jail for sftp transfers

From http://thefragens.com/2011/12/chrootd-sftp-on-mac-os-x-server/

### 10.10 Directions for Juno using bindfs and a folder on second volume. 
####chroot’d SFTP on Mac OS X server
So here you are finding that you need to grant someone else SFTP access to your server. There are lots of reasons to do this, in my case it’s because I needed to grant access to someone’s web designer. We initially worked it out by him emailing me files and me SFTP’ing them up to the server in the correct location. Now he needs direct access to fix some things and I want to give him only what he needs without compromising security. Enter the chroot jail. After lots of googling and some encouragement from the Mac OS X Server email list, I’ve got it working. Here’s how it works.
First, you should create the new user in Workgroup Admin and either assign them access privileges for SSH via Server Admin or assign them to a group that has SSH access privileges. Further discussion is below.

From the Terminal, start off right.
 
```
sudo cp /etc/sshd\_config /etc/sshd_config.bkup
sudo chown root /
sudo chmod 755 /
sudo mkdir -p /chroot/user/scratchpad
sudo chown -R root /chroot
sudo chown user /chroot/user/scratchpad
sudo chmod -R 755 /chroot
```


Every additional new user added will then be something along the lines of the following.

```
sudo mkdir -p /chroot/user2/scratchpad
sudo chown root /chroot/user2
sudo chown user2 /chroot/user2/scratchpad
sudo chmod -R 755 /chroot/user2
```
 
Every folder in the path to the chroot jail must be owned by root. I don’t think it matters what group the folder is in. What I did above was to

1. backup /etc/sshd\_config  
2. change ownership of the root directory to root  
3. change permissions of the root directory to 755  
4. create a chroot folder
5. create a user folder inside the chroot folder
6. create a folder inside the user folder that user can modify
7. set ownership and permissions
8. Now to edit /etc/sshd_config to the following.
 
```
#Subsystem  sftp    /usr/libexec/sftp-server
Subsystem   sftp    internal-sftp
Match User user
  X11Forwarding no
  AllowTcpForwarding no
  ForceCommand internal-sftp
  ChrootDirectory /chroot/user
```
 
This creates a chroot jail. When the user logs in will drop them into the folder /chroot/user, in that folder is a folder they can add things to /chroot/user/scratchpad.
If you want to create a Group in Workgroup Admin for ‘Chroot Users’ then add the new users that you created in Workgroup Admin to the Group; you won’t have to keep editing the /etc/sshd_config file. Instead of the above, add the following. Make sure you add the ‘Chroot Users’ group to the SSH access ACL in Server Admin.
 
```
#Subsystem  sftp    /usr/libexec/sftp-server
Subsystem   sftp    internal-sftp
Match Group chrootusers
  X11Forwarding no
  AllowTcpForwarding no
  ForceCommand internal-sftp
  ChrootDirectory /chroot/%u
```
 
If you have more than one chroot group just repeat the Match Group setup again.
To test whether the above is working, issue the following from the terminal.

```
$ sftp user@domain.com
Password:
sftp>
```

Getting in is one thing. Now you have to mount the folder you want to use. Unfortunately you can’t use a symlink inside of a chroot jail. This is where Homebrew is your best friend. I don’t know why I’ve never seen fit to install this before. After installation just issue the following commands.
 
`brew install bindfs`
 
You might have to restart. Now with an empty folder created in /chroot/useryou can mount --bind to a folder outside of the chroot jail. For example
 
`sudo /usr/local/bin/bindfs -u user /Library/Server/Web/Sites/Server/Documents/mysite/yourfolder /chroot/user/scratchpad`

So far this seems to work here.
Update for Mountain Lion Server
As I’ve updated my server from Snow Leopard to Mountain Lion, there’s one extra step.
From Workgroup Manager, you will need to create a home folder. Nothing really has to go into it, but it needs to be present. My settings are as follows.
Mac OS X Server/Share Point URL: afp://myserver.example.com/Users
Path to Home Folder username
Full Path /Network/Servers/myserver.example.com/Users/username
After setting this up the first time it seems to auto-populate for every other user. You’ll have to go to the Home tab, select it and Save.
 

--
### Older config using disk images
 
We do two things to make this work:

1. Set up chroot jail on the main drive according to the article below
2. mount a disk image stored on TerraFirma to a folder in the chroot jail
sudo hdiutil attach /Volumes/TerraFirma/twitterHD.dmg -mountpoint /jail/twitterHD

Note that "repair permissions" will reset permission structure on the chroot jail and users will no longer be able to connect.

http://www.macresearch.org/restricted-sftp-mac-os-x-leopard

#### Restricted SFTP in Mac OS X Leopard
By dgohara at Fri, Jan 9 2009 1:17pm |Tutorials

On occasion you may want to exchange data with someone else. You'd like to grant that person access to your system (or network) but in such a way that the person has limited access to other areas of the system (or general resources on your network). This article will show you how to setup a chrooted jail that restricts the user to only SFTP on Mac OS X Leopard. It further limits the user so that they cannot traverse the filesystem outside the bounds you specify.  
If you the exchange is one way (for example you want someone to be able to download a file), you can sometimes post it to a website and send them the URL. But sometimes you want to be able to exchange files back and forth. Of course for small files, email is the most obvious way to do this these days.  
Another way that is increasingly used is to restrict the user to a specific portion of the computer that serves as the exchange intermediate (sometimes called a chrooted jail). FTP servers often offer such a facility, but more often people are moving away from FTP to other mechanisms. There are third party applications such as RSSH that can be used to create chrooted environments using secure transfer methods like SSH. They work, but sometimes they can be difficult to set up (or are not supported on the platform of choice).

Requirements:  
  - Mac OS X Leopard 10.5.5 or higher  
  - Server or Client version  
  - Remote login enabled (SSH/SFTP)  

A user account that is allowed to remotely access the system (in this example the user account name is bubbrubb). All commands are performed as root (or via sudo). If root is disabled on your system, you can use sudo or 'sudo su' to root. An important restriction for this to work, is that where ever your files exist on the computer the path to those files must be owned and only be writable by root.  
A simple example:  
`/foo`  

The root directory (tick) needs to be owned by root, and only writable by root. Similarly the directory foo must also be owned by root and only writable by root. The group and everyone permissions can be readable or executable, just not writable.

Another example. Say you want your files to be stored on a drive separate from your main OS drive:
`/Volumes/Storage`

Again, `/`, `/Volumes` and the volume `Storage` must all be owned by root and only writable by root. If you are wondering how a user would write to the disk if only root owns it, I'll explain that in a bit.

##### Storage Location Setup
For this example we'll use `/Volumes/Storage`. (Note, you can do this via symlinks too, but for now, we'll just use the disk structure that Mac OS X sets up on disk insertion).  

`sudo su to root`:
```
chmod g-w /
chmod g-w /Volumes
chmod g-w /Volumes/Storage
chown root /Volumes/Storage
Make a directory for your user:
mkdir /Volumes/Storage/bubbrubb
chown bubbrubb /Volumes/Storage/bubbrubb
chmod 700 /Volumes/Storage/bubbrubb
```

The above does the following:  
1. Create a path to the general storage location that only root can modify. (this will not work, if this isn't true, the connection will outright fail).
2. Create a directory that bubbrubb owns and can write to. We restrict the other permissions so that only user bubbrubb can read and write there (you could log into the server using bubbrubbs account OR modify the group permissions so that others accounts in that group can read and write there too). The important part is that all folders leading up to that one are writable only by root.

##### SSH Remote Access
First test that the user account you want to restrict can ssh into your system. If that works, then continue. If not, figure out why, then:  
`cp /etc/sshd_config /etc/sshd_config.bkup`

Then edit sshd_config commenting out the line:
`Subsystem sftp /usr/libexec/sftp-server`
and adding the line:
`Subsystem sftp internal-sftp`

Then add a block similar to the follow (substituting the User name for the appropriate name on your system):
```
Match User bubbrubb
X11Forwarding no
AllowTcpForwarding no
ChrootDirectory /Volumes/Storage
```

On Leopard Server the match criteria take effect immediately (that is you do not need to restart the service), you may need to on Leopard Client.

Note that in the above Match statement we place the user into the directory one level up from where they can write. Remember, that the path that the user logs into by default can only be writable by root. Once the user logs in, they cd to their directory and can then write files there.

#####User connections and uploading files
First try connecting via ssh:

```
mymac:~ bubbrubb$ ssh bubbrubb@example.com
Password:
```

The connection will hang after you type in your password. Press control-C to cancel the connection. If it goes through some setting didn't take effect.
Then try with sftp:  

```
mymac:~ bubbrubb$ ssh bubbrubb@example.com  
Password:  
sftp> cd bubbrubb  
sftp> put testfile  
Uploading file to /bubbrubb/testfile  
testfile 100% 0 0.0KB/s 00:00  
sftp> get testfile  
Fetching /bubbrubb/testfile to testfile  
sftp>   
```

If you made it this far, then you have successfully set up your chrooted jail.

**Notes:**  
This does not appear to work on systems running Leopard earlier than 10.5.5. I tried this using a system running 10.5.4 and it failed. I didn't do an exhaustive test. YMMV.
I can't stress this enough. The path permissions up to and including the entry point for the jail MUST be owned by root and writable only by root. If not, the connection will fail.
You can increase the verbosity of the sftp connection using the -v, -vv, or -vvv flags with SFTP.