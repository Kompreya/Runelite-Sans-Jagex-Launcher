# Runelite Sans Jagex Launcher
Some Linux scripts to launch Runelite without needing to open and launch through Jagex Launcher. This lets you create a simple .desktop shortcut for example that will launch Runelite directly with your Jagex login creds.

## Notes: 
1) This process still requires you to somehow login to your Jagex account using your login credentials through the Jagex Launcher or similar process. 
The simplest way is to install and login through the Jagex Launcher flatpak, Lutris, or Bolt.

2) Once you login via the Jagex Launcher once and obtain your creds, you will not need to open it again for 30 days, after which your session ID expires and will need to retrieve the new credentials by launching the Jagex Launcher again.

3) TODO: Automate the session refresh process, in a secure fashion. I'm looking into a way to launch the jagex launcher, grab and store the creds in a secure fashion, and in a manner that the runelite launch script can reference. This will be so I don't need to rerun this process every 30 days.

4) If you have multiple OSRS characters, you will need to create a unique script per character. If so, be sure to name your scripts in a memorable way so you know which character you're launching Runelite for.

Warning: Before you proceed, please note that the credentials you are obtaining via these commands are sensitive. Do NOT share them with anyone else, and take precautions to ensure these credentials are secure on your system. The steps and scripts I am providing to you will not expose these creds to anyone but you.

## How To:

1) Install, open and login into the Jagex Launcher. Choose your character and launch Runelite. You don't need to actually log into your Runescape character at this point.

2) In terminal, get the PID for Runelite, such as
   ```
   ps aux | grep runelite

3) Using the PID, get the JX environment variables. The variables exposed by this command are sensitive and unique to your login session. Keep them safe!
   ```
   cat /proc/<PID>/environ | tr '\0' '\n' | grep JX_   

4) Create a .sh script somewhere on your system, giving it a desired name. Copy the following contents into your script.
   ```
   #!/bin/bash

   # Put the env variable values from step 3 here
   export JX_SESSION_ID=
   export JX_ACCESS_TOKEN=
   export JX_DISPLAY_NAME=
   export JX_CHARACTER_ID=
   export JX_REFRESH_TOKEN=

   # Put the path to .runelite/repository2 here
   RUNELITE_DIR="/path/to/.runelite/repository2"

   # This runs all the necessary jars
   RUNELITE_CLASSPATH=$(find "$RUNELITE_DIR" -name "*.jar" -printf "%p:")

   # Launches Runelite with the variables. Change the path after -XX:ErrorFile= to the path to your .runelite/logs/jvm_crash_pid_%p.log location
   /usr/lib/jvm/java-17-openjdk-amd64/bin/java -cp "$RUNELITE_CLASSPATH" -XX:+DisableAttachMechanism -Xmx768m -Xss2m -XX:CompileThreshold=1500 -Dsun.java2d.opengl=false -Drunelite.launcher.version=2.6.9 -XX:ErrorFile=/path/to/.runelite/logs/jvm_crash_pid_%p.log net.runelite.client.RuneLite

  a) Copy the env variable values obtained from step 3 to the script, into their relevant fields
  
  b) For RUNELITE_DIR=, set the actual path to your .runelite/repository2
  
  c) Change the path after -XX:ErrorFile= to the path to your .runelite/logs/jvm_crash_pid_%p.log location
  
  d) Also, verify the Runelite version and your path to your jvm, and update those accordingly. If you know what you're doing, you can modify the various other java arguments.

5) Save your script and make it executable, such as with:
   ```
   chmod +x /path/to/your/script.sh

6) Run the script to test if it works. It should launch Runelite directly with your login info. Jagex Launcher is no longer needed to be running nor used at this point, so it can remain closed until your session expires after 30 days.

7) Optionally, create a .desktop shortcut or some other preferred method of launching Runelite via the script.

