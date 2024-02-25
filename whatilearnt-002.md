# this month findings - Feb 2024

A logbook on small findings techwise

## reset a WSL distro

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


## how is my battery (windows)

start powershell as administrator
```
powercfg /batteryreport
Start-Process .\battery-report.html
```

For a quick evaluation, in the top of the page, in "installed batteries" section, 
Compare the two lines:  DESIGN CAPACITY	and FULL CHARGE CAPACITY  
the first one is how much capacity was publicitize by the manufacturer and the second is how much you really have during the last cycles.  
when brand new, your battery capacity is not far from the 'design capacity' but with time and cycles, real capacity lowers, and your laptop unplugged last less and less.

The ratio (FULL CHARGE CAPACITY / DESIGN CAPACITY) is your current battery score.
