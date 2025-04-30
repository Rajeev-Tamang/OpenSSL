# OpenSSL

## OpenSSL CheatSheet
https://drive.google.com/drive/folders/1f0gmJCV1OqZ5VMiL8VhXv-SMaGb9NV6T


## 1. What is SSL? What is TLS ?
- The internet is many routers owned by many Diffrent ISPs. On either end : you and who you are 
   speaking too.
- When we  send request to websites, it either uses http or https, when using the http, the requested data is moved from different router and the data that travels are not encrypted, *in websites its not always asking for html file but some time we need to send the username,password,credit card number, bank account and so on and if these data are not encrypeted than our personal data are in big risk.*
- **So to solve this SSL/TLS are used.**
- SSL/TLS create a secure,protected tunnel across internet. i.e **https**
- SSL/TLS can also protect other data transfer. Example: to access corporate network(mail server, database.etc) , to connect to public vpn provider(nordvpn,experss vpn,cyberghost,etc)

| SSL | TLS |
|-----|-----|
|Secure Sockets Layer|Transport layer security|
|Created by NetScape in 1994| SSL maintenance handed to IETF in 1999. IETF renamed protocol to TLS|


***NOTE: SSL and TLS are just remaned version***

***
***
***

## 2.How do SSL/TLS protect your data?
- Data sent across a wire can be captured by anyone in the middle.

```mermaid
sequenceDiagram
    participant C as Client
    participant M as MITM
    participant S as Server

    C->S:Request
    
```
- puropse of SSL/TLS is to protect the data in three ways:

   - i)**Confidentiality**: Data is only Accessible by client and server - *Encryption*
   - ii)**Integrity**: Data is not modified between client and server. - *Hashing*
   - iii)**Autherntication**: Client/server are indeed who they are. - *PKI*

***NOTE: TUNNEL IS CONCEPTUAL ILLUSTRATION. DATA IS STILL PASSING THROUGH OTHER NETWORKS.***


***
***
***

## 3. Anti-Replay and Non-Repudiation.
### Anti-Replay
```mermaid
sequenceDiagram
    participant BranchBank as BranchBank
    participant Attacker as Attacker (MitM-With SSL/TLS there is confidiality, intergrity and authentication but the middle man can still capture the data and send it again)
    participant HQ_Bank as HQ_Bank

    BranchBank->>Attacker: Transcation data
    Attacker--xHQ_Bank: Multiple Repeted Transction
    Note over BranchBank,HQ_Bank: Bank Transtion Data
```

- *Suppose the Branch A is sending transtion of $1000 to HQ bank , in some specific user account, if the middle man capture it then , what? We know that he can not read data, due to confidentiality (Encryption) , we know he cannot change it , even if he did it we will know i.e due to integrity (Hashing) but what if he capture and crate multiple copy then sended in that same wire? That specific user acoount wil be full of $$$.*
- So, inorder to be protected from that there is anti-replay.
- Anti-Replay is provided with bulit-in sequqence number.
- That sequence number is built into Intergrity+ Authentication Mechanism.

### Non-Repudation:
- Meaning od Repudation: to refuse to have anything to do with.
- Suppose client sended some data and they are deining that they haven't sended it.
- Sendor cannot later deny sending a message. Byproduct of intergrity+ Authentication

***
***
***

## 4.Key Player of SSL.

- SSL/TLS  ecosystem involves three key players:
   - client
   - server
   - certificate authority (CA)
 
 - Client:
   - Entity initiatinf the TLS Handshake.
   - when we say client, we dont mean to say users, rather web browser, phone, app, IOT, etc.
   - Optionally authenticated (rare)


- Server:
 - Entity receiving the TLS handshake.
 - Web server:
   - Apache,IIS,NginX,etc...
   - Load Balancer or SSL accelerator.
 - Always authenticated [ meaning server have to prove that i am the valid one by giving certificate ]

- Certificate Authority:
   - Governing Entity that issues Certificates. [ Generates Certificate for Server ]
   - Trusted by client and server.
   - Provide Trust Anchor
      - if we trust CA , we trust what the CA trusts [meaning all client do not trust server but if  
        client trust the CA and CA trust the Server than , Client can trust CA]
   - Five Organizations secure 98% of the internet.
     ```mermaid
     graph LR
     IdenTrust["IdenTrust<br>51.9%"]
     DigiCert["DigiCert<br>19.4%"]
     Sectigo["Sectigo<br>17.5%"]
     GoDaddy["GoDaddy<br>6.9%"]
     GlobalSign["GlobalSign<br>2.9%"]

     IdenTrust---DigiCert
     DigiCert---Sectigo
     Sectigo---GoDaddy
     GoDaddy---GlobalSign
     ``` 
***
***
***
