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
```
emacs/nano ~/.bash_profile
export PS1="[\u@\h \w]\n\$ "
export newnode="-q interactive -R rusage[mem=10000] -R span[hosts=1] -W 480 -n 1 -Is bash"
export shortnode="-q short -n 1 -W 4:00 -R rusage[mem=40000]"
export longnode="-q long -n 1 -W 40:00 -R rusage[mem=40000]"
```
Then, you can request new interactive node with:
```
bsub $newnode
```
