# Manage multiple Github accounts and ssh keys

Issue:  
You deal with more than one github account on your computer, for example work and personal.
You want to have all your projects on the same drive and not to make a per repo config.

A solution found on the internet propose to use the ssh host file by creating differents hosts all pointing to github.
This host entries are then use like alias, and you must swap github.com by the alias name on you remote url. Which is tedious and error prone imho.

I once used [two WSL images](https://github.com/MathFrenchToast/mathfrenchtoast.github.io/blob/main/mutipleinstance-wsldistro.md) build from the same base, 
but I have now switch on a single filesystem solution.

I propose to combine two features of git:
- [gitconfig conditional includes](https://git-scm.com/docs/git-config#_conditional_includes)
- [core.sshCommand](https://git-scm.com/docs/git-config#Documentation/git-config.txt-coresshCommand) 

The idea is to have all your work and personal repo in separate folders and use conditional includes to override the core.sshCommand  based on current folder.

I assume that you have locally in your user home `.ssh` folder 2 pairs of keys, one for your personal account, one for your work account and that both are correclty set-up in both github account (if not [look here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account))

Let's suppose you have this folder layout
```
c:\
  |_ dev
     |_ personal
       |_ sideproject1
       |_ sideproject2
       |_ ...
     |_ work
       |_ workproject1
       |_ workproject2
       |_ ...
       
```

1. Create a local config in `personal` called .gitconfig.include

```
[core]
    sshCommand = ssh -i ~/.ssh/mypersonalprivatekeyfile -o IdentitiesOnly=yes

[user]
    email = 'personal.address@email.com'
    name = 'personal user name for commit'
```
 replace the value of the key name,  name and email according to your account
 do the same in the `work` folder but with the work set of values

note: using `~` as a shorthand for your home user folder in powershell is ok

2. add two `includeIf` directives in the main `.gitconfig`file
The main .gitconfig file is in your home user directory
add this:
```
[includeIf "gitdir:C:/dev/personal/"]
    path = C:/dev/personal/.gitconfig_include

[includeIf "gitdir:C:/dev/work/"]
    path = C:/dev/work/.gitconfig_include
```

This will tell to add to the current config all the content of the .gitconfig.include when you are in the given directory or its subdirectories.

note: trailing `/` is mandatory on gitdir, use the forward slash instead of the windows backslash.

And voilà

You can now got to either `c:\dev\work` or  `c:\dev\home`
and do any git clone
and any subsequent git pull/push 

You can extend this to more than two accounts.
