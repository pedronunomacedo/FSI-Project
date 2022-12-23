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
