# Summer learning

## backup an entire azure data storage account

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

## Storing a ssh key in AZ Keyvault

Storing pem files un Azure KV can be cumbersome due to line break,  
I have tested the propose solution here: https://itprovssoftware.wordpress.com/2018/03/18/storing-your-private-ssh-key-in-azure-key-vault/  
with success and for now stick with it !


## small CSS addition for nice printing

```
<style>
        @media print {

            # to be sure that background will have the correct color on print
            body {
                print-color-adjust: exact; 
            }

            # class shorthand to easily remove one item only on print
           .noprint {
              visibility: hidden;
           }
        }
    </style>
```

## amount of GPU memory for a given LLM

([ref](https://blog.eleuther.ai/transformer-math/)) en bytes

M = P * 4 * Q/32 * 1.2

P = nombre de params (codés de base sur 4 octets)
Q = format de la quantization (en bit): 32 (inchangé par rapport à l'oroginal), 16, 8 ou 4 
1.2 facteur d'overhead (see ref)

Result can be checked against Hugging face tool: [Model size](https://huggingface.co/spaces/hf-accelerate/model-memory-usage)

Exemple: for [Dolphin 25 (based on Mixtral-8x7b)](https://erichartford.com/dolphin-25-mixtral-8x7b) which is nice for coding tools, with the smallest quantization (4 bits int):
P=7B, Q=4 =>  M=4.2 Gb
