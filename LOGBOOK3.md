# Trabalho realizado na Semana #3

## Identificação (CVE-2017-17215)

- Huawei HG532 with some customized versions has a remote code execution vulnerability.
- The delivered payload has been identified as OKIRU/SATORI, an updated variant of Mirai.
- The suspected threat actor behind the attack has been identified by his nickname, ‘Nexus Zeta’.
- This vulnerability affects Huawei HG532 rooters (version in unknown).

## Catalogação

- The discovery was first reported on the December 14th of 2017 by Ankit Anubhav, a researcher at security firm NewSky.
- An authenticated attacker could send malicious packets to port 37215 to launch attacks. Successful exploit could lead to the remote execution of arbitrary code.
- This CVE was given a base score of 8.8 in the Severity and Metrics scale, by National Vulnerability Database (NVD) analysts.
- **WHAT ARE BUG-BOUNTIES? THIS CVE HAS THIS?**

## Exploit

- To exploit the vulnerability against a client, an attacker need to send a specially-crafted URL request to the /icon/ path containing “dot dot” sequences in the port 37215.
- This allows the attacker to view arbitrary files on the system.
- The vulnerability allows remote administrators to execute arbitrary commands by injecting shell meta-characters “$()” in the NewStatusURL and NewDownloadURL.
- After the commands have been executed, the exploit returns the default HUAWEIUPNP message, and the ‘upgrade’ is initiated.

## Ataques

- On 23rd November, Check Point Researchers noticed some of Check Point’s sensors and honey-pots were generating suspicious security alerts.
