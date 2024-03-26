# backup an entire azure data storage account

for e.g. to apply an immutability policy

On WSL:
- install azcopy for linux
- az login with your user

create a temporary container in the same region and same subscription

check your right on portal
- 'storage blob data reader' pour la source
- 'storage blob data contributor' pour la destination

```
export AZCOPY_AUTO_LOGIN_TYPE=AZCLI
./azcopy copy 'https://storagehqbackup.blob.core.windows.net/' 'https://tempmigratehqbackup.blob.core.windows.net' --recursive
```

ref: https://learn.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json
