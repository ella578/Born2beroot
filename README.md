# Born2beroot

1. [Virtual machine setup ⚙️](#4--virtual-machine-setup-%EF%B8%8F)

	1.1 [Installing sudo & configuration of users and groups 👤]
	
	1.2 [Installing & configuring SSH 📶]
	
	1.3 [Installing & configuring UFW 🔥🧱]
	
	1.4 [Setting up the sudo policies 🔒]
	
	1.5 [Setting up the strong password policy 🔑]
2. [Script 🚨]

	2.1 [Total result of the script 🆗]
	
3. [Crontab ⏰]
4. [Signature.txt 📝](

1 ◦ The beginning of the installation starts with changing user to root so we can install sudo, for this purpouse we write ```su``` in the bash prompt and introduce the root password, in my case ```xxxxxxx42```. Once we are done we write down the command ```apt install sudo``` so the package manager install the required packages for sudo.
2 ◦ We must reboot machine so the changes can be applied. For that porpouse we will use the commando ```sudo reboot```.
3 ◦ Once the machine is rebooted we have to input the encryptation password and the login again. To check if sudo have been installed correctly we must switch to root user and then use the command ```sudo -V```; this command will show the sudo version (it will show extra info like the plugins installed). **OPTIONAL** ➤ In case of the output being too large we can redirect the command output to a file via ```sudo -V > file.txt``` and then edit the file using ```nano file.txt```. Other option would be putting ```| more``` after the command.
4 ◦ **Now, this step is for the everyone that didn't put his user as the other user asked by the subject during the installation of the system.** Still in the root user we will create an aditional user with ```sudo adduser <login>```. If you had already done it will show the same message as is the image.
5 ◦ We will create a new group called ```user42```. For that we must use ```sudo addgroup user42```.
6 ◦ With ```sudo adduser <user> <groupname>``` we can include a user to a group. We mst include out user in the groups ```sudo``` and ```user42```.
7 ◦ Once we are done with that we can check it using ```getent group <groupname>``` or editing the /etc/group file using ```nano /etc/group```; the groups ```sudo``` and ```user42``` must be present with our user.

### 1.2 - Installing & configuring SSH 📶

🧠 <b> What is SSH❓</b> The acronym SSH stands for "Secure Shell." The SSH protocol was designed as a secure alternative to unsecured remote shell protocols. It utilizes a client-server paradigm, in which clients and servers communicate via a secure channel.

1 ◦ First thing, we should update the system using ```sudo apt update```.
2 ◦ Following up we will install the main tool for remote access with the SSH protocol, using OpenSSH. The installation requieres the package ```sudo apt install openssh-server```. When we are asked for confirmation we will write ```y```, and just then the installation will proceed.
Anyone curious that the installation have been realices without problems we can use ```sudo service ssh status``` and it will show how is the state of it. **Active** must be show to continu

3 ◦ Going on, some files have been created and we need to configur them. For that we will use [Nano](https://en.wikipedia.org/wiki/GNU_Nano) or [VIM](https://en.wikipedia.org/wiki/Vim_(text_editor)) (we will need to install vim since it's not preinstalled using ```sudo apt install vim```) or any other text editor. First file that we will edit will be ```/etc/ssh/sshd_config```. If you are not on root you will not be able to edit the file; as you know, for switching to root we use ```su```.

4 ◦ The ```#``` means that line it is commented; the lines that we will be edit have to be uncommented. Once we are editing the  file we need to update the following lines:
#Port 22 -> Port 4241
➤ #PermitRootLogin prohibit-password -> PermitRootLogin no

When finish we have to save the changes and leave the file.

5 ◦ Now with the file ```/etc/ssh/ssh_config```. (not ```sshd_config```)
Edit the following line: 

➤ #Port 22 -> Port 4241
6 ◦ Finally we must restart the ssh service so it can be updated. For that purpuse we will use ```sudo service ssh restart``` and once it is done we will check the service state with ```sudo service ssh status``` and confirm that everything is alright.

### 4.3 Installing & configuring UFW 🔥🧱

🧠 <b>What is [UFW](https://en.wikipedia.org/wiki/Uncomplicated_Firewall)❓</b> It is a [firewall](https://en.wikipedia.org/wiki/Firewall_(computing)) which use the command line for setting up [iptables](https://en.wikipedia.org/wiki/Iptables) using a small number of easy commands.

1 ◦ First things first, we need to install the packages for UFW, for that we will use ```sudo apt install ufw```, then when we are asked for confirmation type ```y``` and the installation will proceed
2 ◦ When we are done with it, we want to start it using the command ```sudo ufw enable``` and then it have to show us the the *firewall is ative.*
3 ◦ Then we must allow our firewall to accept the connections that will happens in the 4242 port. What we will do is use ```sudo ufw allow 4241```. 
4 ◦ Lastly we will check if everything done here is correct checking the actual state of our firewall. For that we will use ```sudo ufw status```. Alternatively ```sudo ufw status verbose``` or ```sudo ufw status numbered``` can be used.
### 4.4 Setting up the sudo policies 🔒

1 ◦ Begining with this section, we will create a file in */etc/sudoerd.d/*. The file will serve the purpouse of storing our sudo policy. The command that we will use will be ```touch /etc/sudoers.d/sudo_config```.
2 ◦ Then we must create a directory as is asked in the subject in */var/log/* because each commands need to be logged, the input and output. We will use ```mkdir /var/log/sudo``` for our folder.
3 ◦ We must edit the file that we created in the first step of this section. Use any text editor, but for this guide as is in every screenshot we will use nano. Use ```nano /etc/sudoers.d/sudo_config```.
 ◦ Once we are editing the file we must set it up with the following commands.

```
Defaults  passwd_tries=3
Defaults  badpass_message="Mensaje de error personalizado"
Defaults  logfile="/var/log/sudo/sudo_config"
Defaults  log_input, log_output
Defaults  iolog_dir="/var/log/sudo"
Defaults  requiretty
Defaults  secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

➤ As it should be on the file.
🤔 <b>What does each command❓ </b>

![F5B5BED3-C144-4EDF-91AB-226533DD5B18_4_5005_c](https://user-images.githubusercontent.com/66915274/211846396-e3212104-b8ce-412c-ac1a-e4d3124dfba8.jpeg)

🟩 **GREEN**	-> Total tries for entering the sudo password.

🟥 **RED**		-> The message that will show when the password failed.

🟨 **YELLOW**	-> Path where will the sudo logs will be stored.

🟦 **BLUE**	-> What will be logged.

🟫 **BROWN**	-> TTY is required lol.

🟪 **PURPLE**	-> Folders that will be excluded of sudo

### 4.5 Setting up a strong password policy 🔑

1 ◦ First step will be editing the login.defs file.
2 ◦ Once we are done editing the file, we will set the next parameters:

➤ PASS_MAX_DAYS 99999 -> PASS_MAX_DAYS 30

➤ PASS_MIN_DAYS 0 -> PASS_MIN_DAYS 2
PASS_MAX_DAYS: It's the max days till password expiration.

PASS_MIN_DAYS: It's the min days till password change.

PASS_WARN_AGE: It's the days till password warning.

3 ◦ For continuing the installation we must install the next packages with the following command```sudo apt install libpam-pwquality``` , then we wrute ```Y``` so we can continue; we wait till it finish
4 ◦ Next thing we must do is is edit a file and change itś content. We will use ```nano /etc/pam.d/common-password```. 
5 ◦ After retry=3 we must add the following commands:

```
minlen=10
ucredit=-1
dcredit=-1
lcredit=-1
maxrepeat=3
reject_username
difok=7
enforce_for_root
```
➤ This is how the line must be↙️
➤ This is how the file must look ↙️
🤔 <b>What does each command❓</b>

minlen=10 ➤ The minimun characters a password must contain.

ucredit=-1 ➤ The password at least have to contain a capital letter. We must write it with a - sign, as is how it knows that's refering to minumum caracters; if we put a + sign it will refer to maximum characters.

dcredit=-1 ➤ The passworld at least have to containt a digit.

lcredit=-1 ➤ The password at least have to contain a lowercase letter.

maxrepeat=3 ➤ The password can not have the same character repited three contiusly times.

reject_username ➤ The password can not contain the username inside itself.

difok=7 ➤ The password it have to containt at least seven diferent characters from the last password ussed. 

enforce_for_root ➤ We will implement this password policy to root.



### 5-1 Architecture 

For the architecture of the SO to be shown, you will use the command ```uname -a``` ("-a" == "--all"). What this command does is print all information, except if the CPU is unknow or the platform hardware.

### 5-2 Physical Cores

For the number of fisical cores to be shown we will use the file /proc/cpuinfo, which give us information about the CPU: its type, brand, model, performance, etc. We will use ```grep "physical id" /proc/cpuinfo | wc -l``` with the command grep looking inside the file "physical id" and with wc -l to count the line of the grep output. 

### 5-3 Virtual Cores

To show the number of virtual cores is very similar to the previous one. We will again use the file /proc/cpuinfo, but in this case we will use the command ```grep processor /proc/cpuinfo | wc -l```. The usage is practically the same as before, only that instead of counting the lines of "physical id" we will do it with "processor". We do it this way for the same reason as before, the way of quantifying marks 0 if there is a processor.

### 5-4 RAM

To show the RAM memory we will use the command ```free``` to see at the moment information about the RAM, the used part, free, reserved for other resources, etc. For more info about the command we will put free --help. We will use free --mega since that unit of measure appears in the subject.

Once we have run this command, we must filter our search since we do not need all the information that it provides. The first thing we need to show is the used memory, for which we will use the command ```awk```, which processes data based on text files, that is, we can use the data that interests us from a file. Finally, what we will do is compare if the first word of a row is equal to "Mem:" we will print the third word of that row, which will be the used memory. The whole command together would be ```free --mega | awk '$1 == "Mem:" {print $3}'```. In the script the return value of this command will be assigned to a variable that will be concatenated with other variables so that everything is the same as specified in the subject.

To obtain the total memory, the command is practically the same as the previous one, the only thing we must change is that instead of printing the third word of the row, we want the second one ```free --mega | awk '$1 == "Mem:" {print $2}'```.

Finally, we must calculate the % of used memory. The command is again similar to the previous ones, the only modification we will make is in the printing part. As the operation to get the percentage is not exact, it can give us many decimals and in the subject only 2 appear, so we will do the same, that is why we use ```%.2f``` so that only 2 decimals are shown. Another thing you may not know is that in printf to show a ```%``` you have to put ```%%```. The whole command ```free --mega | awk '$1 == "Mem:" {printf("(%.2f%%)\n", $3/$2*100)}'```.

### 5-5 Disk memory

To view the occupied and available memory of the disk, we will use the ```df``` command, which stands for "disk filesystem", it is used to get a complete summary of the use of disk space. As indicated in the subject, the used memory is shown in MB, so we will then use the -m flag. Next, we will do a grep to only show us the lines that contain "/dev/" and then we will do another grep with the -v flag to exclude lines that contain "/boot". Finally, we will use the awk command and sum the value of the third word of each line to once all the lines are summed, print the final result of the sum. The entire command is as follows: ```df -m | grep "/dev/" | grep -v "/boot" | awk '{memory_use += $3} END {print memory_use}'```.

To obtain the total space, we will use a very similar command. The only differences will be that the values we will sum will be $2 instead of $3 and the other difference is that in the subject the total size appears in Gb, so as the result of the sum gives us the number in Mb we must transform it to Gb, for this we must divide the number by 1024 and remove the decimals.

Finally, we must show a percentage of the used memory. To do this, again, we will use a command very similar to the previous two. The only thing we will change is that we will combine the two previous commands to have two variables, one that represents the used memory and the other the total. Once we have done this, we will perform an operation to obtain the percentage ```use/total*100``` and the result of this operation will be printed as it appears in the subject, between parentheses and with the % symbol at the end. The final command is this: ```df -m | grep "/dev/" | grep -v "/boot" | awk '{use += $3} {total += $2} END {printf("(%d%%)\n"), use/total*100}'```.

## 5-6 CPU usage percentage

To view the percentage of CPU usage, we will use the ```vmstat``` command, which shows system statistics, allowing us to obtain a general detail of the processes, memory usage, CPU activity, system status, etc. We could put no option but in my case I will put an interval of seconds from 1 to 4. We will also use the ```tail -1``` command, which will allow us to produce the output only on the last line, so of the 4 generated, only the last one will be printed. Finally, we will only print word 15, which is the available memory usage. The entire command is as follows: ```vmstat 1 4 | tail -1 | awk '{print %15}'```. The result of this command is only part of the final result since there is still some operation to be done in the script for it to be correct. What should be done is to subtract the amount returned by our command from 100, the result of this operation will be printed with one decimal and a % at the end and the operation would be finished.

### 5-7 Last reboot

To see the date and time of our last restart, we will use the ```who``` command with the ```-b``` flag, as this flag will display the time of the last system boot on the screen. As has happened to us before, it shows us more information than we want, so we will filter it and only show what we are interested in, for this we will use the awk command and compare if the first word of a line is "system", the third word of that line will be printed on the screen, a space, and the fourth word. The entire command would be as follows: ```who -b | awk '$1 == "system" {print $3 " " $4}'```.

### 5-8 LVM active

To check if LVM is active or not, we will use the ```lsblk``` command, which shows us information about all block devices (hard drives, SSDs, memories, etc) among all the information it provides, we can see lvm in the type of manager. For this command we will do an if because we will print Yes or No. Basically, the condition we are looking for will be to count the number of lines in which "lvm" appears and if there are more than 0 we will print Yes, if there are 0 we will print No. The entire command would be: ```if [ $(lsblk | grep "lvm" | wc -l) -gt 0 ]; then echo yes; else echo no; fi```.

### 5-9 TCP connections

To check the number of established TCP connections, we will use the ```ss``` command replacing the now obsolete netstat. We will filter with the ```-ta``` flag so that only TCP connections are shown. Finally, we will do a grep to see those that are established as there are also only listening and close with wc -l to count the number of lines. The command is as follows: ```ss -ta | grep ESTAB | wc -l```.

### 5-10 Number of users

We will use the ```users``` command which will show us the names of the users there are, knowing this, we will put ```wc -w``` to count the number of words in the command output. The entire command is as follows: ```users | wc -w```.

### 5-11 IP adress & MAC

To obtain the host address, we will use the ```hostname -I``` command and to obtain the MAC, we will use the ```ip link``` command which is used to show or modify the network interfaces. As more than one interface, IP's etc. appear, we will use the grep command to search for what we want and thus be able to print only what is requested. To do this, we will put ```ip link | grep "link/ether" | awk '{print $2}'``` and in this way we will only print the MAC.

### 5-12 Number of commands executed with sudo

To obtain the number of commands executed with sudo, we will use the ```journaclctl``` command, which is a tool that is responsible for collecting and managing the system logs. Next, we will put ```_COMM=sudo``` in order to filter the entries by specifying its path. In our case we put ```_COMM``` because it refers to an executable script. Once we have filtered the search and only the sudo logs appear, we still need to filter a bit more as when you start or close the root session it also appears in the log, so to finish filtering we will put a ```grep COMMAND``` and this will only show the command lines. Finally, we will put ```wc -l``` so that the lines are numbered. The entire command is as follows: ```journalctl _COMM=sudo | grep COMMAND | wc -l)```. To check that it works correctly, we can run the command in the terminal, put a command that includes sudo and run the command again and it should increase the number of sudo executions.


