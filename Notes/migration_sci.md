# Migration from GHPCC to SCI
New SCI uses public/private key pair to authenticate so that you don't need to use passcode. Currently, **VPN is needed** to connect to SCI.
## Connect from local Mac to SCI
1. Use `ssh-keygen` to create a key pair. Note, the default private key will be saved to `~/.ssh/id_rsa`. Pick another name if you already have `id_rsa`. Example name: `id_rsa_mac`. Or, you can use the existing `id_rsa.pub` for step 2 and 3.   
2. Copy the content of `~/.ssh/id_rsa_mac.pub` to https://hpcportal.umassmed.edu/PublicKeys under the `Key Management` menu.
3. To connect from local Mac to SCI;
```
ssh your_name-umw@hpc.umassmed.edu
```
If you are using a filename other than `id_rsa`, you probably need to use:
```
ssh -i ~/.ssh/your_keyfile your_name-umw@hpc.umassmed.edu
```
4. If you are using a name other than `id_rsa` but still want to use `ssh your_name-umw@hpc.umassmed.edu`, you can:
```
touch ~/.ssh/config
```
Then, add the following lines to the `config` file:
```
Host hpc.umassmed.edu
    HostName 146.189.26.12
    User your_name-umw
    IdentityFile ~/.ssh/your_keyfile
```
You do need to replace `your_keyfile` with the actual one while `your_name-umw` does not matter. Also note that the permission of the `config` file can not be too loose, execute `chmod 644 ~/.ssh/config` if too loose.

## Connect from SCI to GHPCC
1. First log onto SCI, then, use `ssh-keygen` to create a key pair. The default private key will be saved to `~/.ssh/id_rsa`. Pick another name if you already have `id_rsa`. Example name: `id_rsa_sci`. Or, you can use the existing `id_rsa.pub` for step 2 and 3.
2. Copy the content of `~/.ssh/id_rsa_sci.pub` to the end of `~/.ssh/authorized_keys` on your GHPCC account.
3. To connect from SCI to GHPCC;
```
ssh your_name@ghpcc06.umassrc.org
```
If you are using a filename other than `id_rsa`, you probably need to use:
```
ssh -i ~/.ssh/your_keyfile your_name@ghpcc06.umassrc.org
```
4. If you are using a name other than `id_rsa` but still want to use `ssh your_name@ghpcc06.umassrc.org`, you can:
```
touch ~/.ssh/config
```
Then, add the following lines to the `config` file:
```
Host ghpcc06.umassrc.org
    HostName 205.172.168.15
    User your_ghpcc_id
    IdentityFile ~/.ssh/your_keyfile
```
You do need to replace `your_keyfile` with the actual one while `your_ghpcc_id` does not matter. Also note that the permission of the `config` file can not be too loose, execute `chmod 644 ~/.ssh/config` if too loose.

## Copy files from GHPCC to SCI
Follow wiki: https://hpc.umassmed.edu/wiki/index.php?title=Scientific_Computing_for_Innovation_Cluster#rsync      

To copy data from the GHPCC cluster to the SCI cluster start a terminal running on the SCI cluster. To recursively copy a directory, you can run a command such as:
```
rsync -avz your_name@ghpcc08.umassrc.org:/project/umw_john_doe/mydirectory /project/umw_john_doe/
```

## References
1. New SCI wiki page (must on VPN): https://hpc.umassmed.edu/   
2. For help: hpc@umassmed.edu
