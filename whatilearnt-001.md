A logbook on small findings techwise
====================================

---
# reset a WSL distro

in Powershell

```
wsl --list  
wsl --unregister DISTRO-NAME
```

This will remove the virtual filesystem.

you can check with `wsl --list` that the distro is no longer there
Then you can on the Windows store, relaucnh it without re-downloading it


BTW, you can (should ?) make a backup of your vm first ((doc)[https://learn.microsoft.com/en-us/windows/wsl/basic-commands#import-and-export-a-distribution]) just in case
```
wsl --shutdown
wsl --export <Distribution Name> <FileName>
```

you'll be able to restore it with: `wsl --import <Distribution Name> <InstallLocation> <FileName>`  
where `<InstallLocation>` enable yout to define where it will be installed instead of the default: %localappdata%\Packages

----

# Docker python with gui 

## on windows

You will need a X server. I recommend  (vcxsrv)[https://sourceforge.net/projects/vcxsrv/]  
(other popular choice is install xming)
upon installation allow firewall rule.

get you lan IPv4 by running `ipconfig`  
in the example below mine was: 192.168.10.119  

run your docker file with:
`docker run -e DISPLAY=192.168.10.119:0.0 --network=host <<imagename>>`

## on wsl

`export DISPLAY=$(route.exe print | grep 0.0.0.0 | head -1 | awk '{print $4}'):0.0`  
docker run -e DISPLAY=$DISPLAY --network=host  

---
# Git status and branch in prompt (WSL ubuntu)

I used to have a hoem made shell method in my bashrc to find current branch status and add it to the shell prompt. 
It was a bit slow, And I decide to google a bit to find a quicker alternative.
I found that there is an (official prompt part of git packages)[https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh]


Add this to your ~/.bashrc:
```
source /etc/bash_completion.d/git-prompt
export GIT_PS1_SHOWDIRTYSTATE=1
export PS1='\[\e]0;\u@\h: \w\a\]${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]$(__git_ps1 "(%s)")\$ '
``` 
from:

https://stackoverflow.com/questions/44237255/automatic-display-of-git-status-in-linux-bash/66689916#66689916

--- 

# docker copy vs Add

Today I refactor some of my Docker file, 
I have a mix of ADD and COPY command.
Quick reminder from the Docker doc: prefer COPY over ADD but if you want to ADD file from URL in your image

---

# Maven - override deployment repo

Do you know that you can override deployment repo at runtime ?
often dev env is not the same as the ci env.

you can use 
mvn deploy -DaltReleaseDeploymentRepository=myrepo::https://user:pass@server/repo
other "alts" are: 
- altReleaseDeploymentRepository
- altSnapshotDeploymentRepository

see: https://maven.apache.org/plugins/maven-deploy-plugin/deploy-mojo.html


<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 https://maven.apache.org/xsd/settings-1.0.0.xsd">
    <localRepository>/usr/share/maven/ref/repository</localRepository>


    <servers>
        <server>
            <id>adelya</id>
            <username>devadelya</username>
            <password>${env.ARCHIVA_ADELYA_PWD}</password>
        </server>
    </servers>
    <profiles>
        <profile>
            <id>AdelyaHQ Archiva</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>

            <repositories>
                <repository>
                    <id>adelya</id>
                    <name>devinternal</name>
                    <url>http://192.168.110.21:8080/repository/adelya/</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </repository>
            </repositories>

        </profile>
    </profiles>
</settings>

ADD mvn/platform_mvn_files/settings.xml .ADD mvn/platform_mvn_files/settings.xml .
