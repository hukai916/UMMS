1. Display disk utility usage:
```
df -h | grep "keyword"
```
Or
```
du -sh *
```

2.	Create alias on MacOS
```
open -e ~/.zshrc
alias sci="xxx"
source ~/.zshrc
```

3. Create alias on SCI:
Open `~/.bash_profile` with `nano` or `emacs`. Then add the following commands at the bottom:
```
export PS1="[\u@\h \w]\n\$ "
export newnode="-q interactive -R rusage[mem=10000] -R span[hosts=1] -W 480 -n 1 -Is bash"
export shortnode="-q short -n 1 -W 4:00 -R rusage[mem=40000]"
export longnode="-q long -n 1 -W 40:00 -R rusage[mem=40000]"
export hpc="xxx@ghpcc06.umassmedrc.org"
```
Then, you can request new interactive node with:

```
source ~/.bash_profile
bsub $newnode
```
To view the command parameters:
```
echo $newnode
```

4. Copy from hpcc to sci using rsync:
```
bsub $longnode -o rsync-log.txt "rsync -avz $hpc:target_folder dest_folder"
```
Or
```
bsub -q long -n 1 -W 100:00 -R select[rh=8] -R rusage[mem=40000] -o rsync-log.txt "rsync -avz $hpc:target_folder dest_folder"
```
