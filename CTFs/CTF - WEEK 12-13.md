# CTF - WEEK 10

## Challenge #1

- The purpose of this challenge is trying to make the administrator to accept you justification.

**1.** After some time, we saw that we could send a script on the input field in order to get the flag.

- So, we elaborate this script in order to get the `flag.txt`.

```js
<script>document.getElementByld('giveflag').click()</script>
```

<img src="CTFs/Img/Semana10-Desafio1/1.askFlag.png" alt="Request page"/><br/>

**2.** Then, after submitting your request, we were redirect to the "waiting" window, waiting for the request to be approved.

**3.** After some time, we reloaded the page and there it was, the _flag_.

<img src="CTFs/Img/Semana10-Desafio1/2.output_flag.png" alt="Flag image" /><br/>

# Flag

`flag{41cac5e48d2c11fac35feedaaef03c2c}`

## Challenge #2

- The purpose of this challenge is trying to find the flag on a working directory.

**1.** After exploring the site, we could see that we could enter a working directory with some files using the input field _ping a host_.

**2.** After some time exploring this working directory, we went to the main folder, and could see that there was a folder called _flags_.

<img src="CTFs/Img/Semana10-Desafio2/1.mainDirectory.png" alt="Main directory with flags folder" /><br/>

**3.** So, we entered this folder and there it was two `flag.txt` files.

<img src="CTFs/Img/Semana10-Desafio2/2.flagsDirectory.png" alt="flags.txt files" /><br/>

**4.** After this, we were able to cat both of this files in order to get the _flag_.

<img src="CTFs/Img/Semana10-Desafio2/3.code_ping.png" alt="Code input" /><br/>

**5.** Finally, we were able to display the _flags_.

<img src="CTFs/Img/Semana10-Desafio2/4.result_flag.png" alt="Flags result" /><br/>

# Flag

`flag{4c6a300c7ccb7d3734acf1b5d8239e58}`
