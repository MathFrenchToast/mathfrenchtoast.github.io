# this week findings

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
