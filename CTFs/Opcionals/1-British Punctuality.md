# Opcional CTF - British Punctuallity

## British Punctuality

- We started off this challenge by exploring the directories and files of the environment we had entered.
- After some time, we could check that on the path `home/flag_reader` we have found that the **my_script.sh** file where we could see the printenv command being used to call a relative path.
- This allowed us to rewrite the PATH environement variable.

![](https://i.imgur.com/qSIRP21.png)

- After changing the directory to the _tmp_ folder, we have found a last_log file.

![](https://i.imgur.com/oVnHK2D.png)

- After some time, we could confirm that the server was performing the script each every minute. Unfortunately, we dont't have access to the _flag_ file but the _cron_ has.

- With this in mind, we created ```aux.c``` with code to open flag.txt:

```
#include <stdio.h>
#include <stdlib.h>

int main(){
    system(“cat /flags/flag.txt“); 
}
```

- And compiled it using the name ```printenv```. Doing this, the server will run our code, instead of the standard _printenv_ command.

![](https://i.imgur.com/grHyLzn.png)

- Following this perspective, we also needed to update the PATHS of the environement variables and put it on the first line, in order to our _printenv_ be called, and to be the one the system will run instead. 

- To do this, we created a file named _env_ with the following code:

![](https://i.imgur.com/EHAN2Q7.png)

- Finally, we opened _last_log_ file, waited a few seconds and there it was the flag.

### Flag

`flag{2f761ab782abaf2784f9d678ad9abfe}`
