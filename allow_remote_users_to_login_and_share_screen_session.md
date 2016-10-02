# Allowing a remote user to SSH into localhost and concurrently share terminal (with a screen session):

## Please ensure to cleanup after...

1. Create a temp home dir for temp user in /tmp:
  * ``` sudo mkdir /tmp/test ```
  
2. Create temp user  (the user is named “test” in this example) with ~ in /tmp:
  * ``` useradd -c "temp user for sharing terminal session with screen" -d /tmp/test -e 2016-10-02 -f 0 -s /bin/bash ```
  
  *This creates a user with:*
    * ```-c```	<- a descriptive comment
    * ```-d /tmp/test```		<- this makes /tmp/test the home directory
    * ```-e	YYY-MM-DD```	<- this creates a user that will be deactivated at the specified date in the form: YYY-MM-DD
    * ```-s /bin/bash```		<- this makes bash the shell
    
3. Give the temp user RW permissions to temp home directory:
  * ```sudo chown -R test:test /tmp/test```
    
4. Create ~/.profile that starts screen session with a predefined session name (“shared-session” here) for a non-interactive shell session:
  * ```vim /tmp/test/.profile```
    * ```#!/bin/bash

          # temp bashrc file to run commands on initialization:

          # run screen on for non-interactive login:
          /usr/bin/screen -S shared-session```
          
5. Create a ~test/.bashrc file that alerts you, when the user has logged in:
  * ```vim ~test/.bashrc```
    * ```# This is a bashrc file, who's sole purpose is to run a command that alerts other users that a user has logged in:```
        
          ```# echo message to wall:```
                
           ```echo "I HAVE A NEW BASH SESSION" | wall ```
        
6. Set the passwd for the temp user:
  * ```passwd test```

7. Inform the remote user to start an SSH session  to your localhost:
  *ssh test@{FULLY QUALIFIED DOMAIN NAME || YOUR PUBLIC IP ADDRESS}
  * _You will obviously have to open up a port on your firewall to allow SSH in from the WAN…_
  * When the user logs in, you should be alerted with a message to your terminal: "I HAVE A NEW BASH SESSION"
  * you could probably just text them the command:
    * ```ssh test@${YOUR_PUBLIC_IP}
  * A quick command to get your public IP using curl:
    * ```curl ifconfig.me```
  
8. On your local host, switch to the temp user:
  * ```Su test```
  * ```Enter password…```
  
9. Confirm that a screen session with the pre-defined name (in this example we called the screen session “_shared-session_”):
  * ```Screen -list```
  * Output:
    * ```There is a screen on:```
      ```30503.shared-session    (01/10/16 22:39:59)     (Attached)```
      ```1 Socket in /var/run/screen/S-test.```
          
10. In order to override a screen error (Cannot open your terminal '/dev/pts/0' - please check.), we have to run the following command before joining the remote user’s screen session:
  * ```Script /dev/null```
  
11. And finally join the remote user’s screen session:
  * ```screen -x shared-session```
  
>Now you, and the other user will be sharing a terminal, where you can both type commands at a bash prompt. 
>If the size of your screen terminal looks funny (it would be the same as what ever the remote user’s screen size is), you can do CTRL+a, then F, to resize the window.
