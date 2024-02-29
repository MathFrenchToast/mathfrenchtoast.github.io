# manage mutiple WSL images of the same distro


We will create a minimal installation and defined a user  
create a based image,  
and then create 2 deriveds image (Work and Home).  

I assume you already have wsl 2 installed (if not follow the step [here](https://learn.microsoft.com/en-us/windows/wsl/install)).  

I'll use ubuntu 22
if you already have this distro I advice to create an export of your current distro then unregister it.

In the  windows store [go to the ubuntu22](https://www.microsoft.com/store/productId/9PN20MSR04DW?ocid=pdpshare)
download and run
at prompt define your username and password.
then perform a first update/upgrade abd exit

```bash
# download and install last system update
sudo apt update -y && sudo apt -y upgrade
# add this line to the profile to be in your home dir at startup 
echo "cd" >> .profile
exit
```

Our base image is ready, we will now export it.
I propose a directory layout under c:\WSL, but you can change the path if you want
In a powershell window:


```powershell
# create the directory structure
mkdir -p c:\WSL\img
mkdir -p c:\WSL\instances

# shutdown any running wsl session
wsl --shutdown

# export our image as  base image
wsl --export Ubuntu-22.04 "c:\WSL\img\DefaultUbuntu22.04.tar"
```

Now that we have a base image, we will create 2 derived images, one for work the other for home.
still in your PS window:
```powershell
# import the same image twice with two different name and location
wsl --import WorkUbuntu22 "c:\WSL\instances\WorkUbuntu22" "c:\WSL\img\DefaultUbuntu22.04.tar"
wsl --import HomeUbuntu22 "c:\WSL\instances\HomeUbuntu22" "c:\WSL\img\DefaultUbuntu22.04.tar"

# you can check your distro are created:
wsl --list
```

That it to run one or the other just use (change the username with the one choose above:
`wsl --distribution WorkUbuntu22 --user mathieu` 
or   
`wsl --distribution HomeUbuntu22 --user mathieu`

note: as on any stanadrd wsl distro the windows hd is accessible on `/mnt/c`

From here, you can create:
- shortcut for this on your desktop (targeting "C:\Windows\System32\cmd.exe" /k  wsl --distribution WorkUbuntu22 --user mathieu)
- define one of the image as your default (e.g. `wsl --set-default WorkUbuntu22`)
- remove one of your distro, e.g. wsl --unregister WorkUbuntu22 (you'll have to delete the files in c:\WSL\instances)
