# CTF - WEEK 4

## Challenge #1

- The purpose of this challenge is to make login to the Wordpress platform with an administrator acoount, using a CVE with known exploit to accomplish it.

1. On the main page of the Wordpress platform, click on the _Menu_ button located in the superior upper right corner.

<img src="CTFs/Img/Semana4-Desafio1/1.homepage.png" alt="Homepage image" width="50%"/><br/>

2. On the dropdown menu, click on the _Shop_ option.

<img src="CTFs/Img/Semana4-Desafio1/2.main_menu.png" alt="Main Menu image" width="50%"/><br/>

3. On the _Shop_ page, scroll down until you find the "WordPress Hosting" product card, and click on the respective image.

<img src="CTFs/Img/Semana4-Desafio1/3.product_card.png" alt="Product card image" width="50%"/><br/>

4. On the product page, scroll down to the product information section. Click on the _Additional information_ option.

<img src="CTFs/Img/Semana4-Desafio1/4.product_info.png" alt="Product information image" width="50%"/><br/>

- In this option, you can check WordPress puglins version. The version of the puglin we want is the _Booster for WooCommerce plugin_, being on the version **5.4.3**.

5. Now, you need to find the exploit for this puglin. You can go to the Exploit Database (on the link https://www.exploit-db.com/exploits/50299). Download the exploit on the download button.

6. Take the exploit code to contruct the _flag_.
<br/>

`flag{CVE-2021-34646}`

## Challenge #2

- On this challenge, we will explore the exploit and analyze is functionality, using it against the WordPress server.

1. After making the steps of the first challenge, continue to the _2nd_ step.

2. On the terminal, go to the folder where o saved the exploit file and execute it. Type the following command.
**NOTE**: Your python can have a different version, so on the beggining of the instruction use the version of your python. The number _1_ teferences to the _userID_ (normally the id of the administrator account is the _1_).

```console
python3.10 exploit_CVE-2021-34646.py http://ctf-fsi.fe.up.pt:5001/ 1
```

3. Doing this, will appear on the terminal 3 links. Go through the links, and check the one that will give you access to an administrator account (in my case, it was the first link).

<img src="CTFs/Img/Semana4-Desafio1/5.terminal_links.png" alt="Terminal links image" width="50%"/><br/>

4. You will be redirected to the homepage administrator account. There you need to ckick on the WordPress logo on the left upper corner. And then click on the options menu in the left upper corner.

<img src="CTFs/Img/Semana4-Desafio1/6.admin_account.png" alt="Administrator account image" width="50%"/><br/>


5. On the left side menu, click on the _Posts_ option, and then _All Posts_.

<img src="CTFs/Img/Semana4-Desafio1/7.all_posts.png" alt="All Posts page image" width="50%"/><br/>

6. After doing the previous step, you will see a message to the platform employees. Click on this post.

7. Finally, the flag will be in this message.

<img src="CTFs/Img/Semana4-Desafio1/8.flag_message.png" alt="Flag image" width="50%"/><br/>

`flag{please don't bother me}`
