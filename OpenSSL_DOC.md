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

    Note over C,S:Request
    
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
 
 - **Client:**
   - Entity initiating the TLS Handshake.
   - when we say client, we dont mean to say users, rather web browser, phone, app, IOT, etc.
   - Optionally authenticated (rare)


- **Server:**
  - Entity receiving the TLS handshake.
  - Web server:
    - Apache,IIS,NginX,etc...
    - Load Balancer or SSL accelerator.
 - Always authenticated [ meaning server have to prove that i am the valid one by giving certificate ]

- **Certificate Authority:**
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

## 5.Hashing

- Algorithm which takes as input a message of arbitary length and produces as output a "Fingerprint" 
  of  the original message.
- *Let's say we want to hash the word "Hello" which simply adds tha alphabetic numeric of letter*

```mermaid
 graph LR
hello-->|Hashing Algorithm <br> 8+5+12+12+15|52
```
- Result of the Hashing Algorthim is called a Digest.
   - Also called: Checksum, Fingerprint , Hash, CRC , etc.

- if orginal data is changed , resulting digest will be different.
   - suppose someone changed the hello to cello , the resulting diest will be 47
```mermaid
graph LR
cello-->|Hashing Algorithm<br>3+5+12+12+15|47
```

- **Real world Hashing Algorithms must satisfy four requirements:**
     - Infeasible to produce a given digest.
     - Impossible to extract orginal message.
     - Slight Changes produce drasric differnces.
     - Resulting digest is fied width(length)
  ![Hashing](https://github.com/user-attachments/assets/78422bf7-9ff3-4272-9168-80b61d92aed2)

### Collisons:
   - Two messages result in identical digests.
   - Connot be avoided.
        - it is a byproduct of "fixed width digest"
   - Imaginary Hashing algorithms:
        - prac-hash-3 bit digest.
             - "bit" is either 0 or 1
             - 3 bits means eight possible digest [ 2^8]
```mermaid
graph TB
000---001---010---011---100---101---110---111
```
- Common Hashing Algorithms:
     - MD5         128bits
     - SHA/SHA1    160bits
     - SHA2 family:
          - SHA-224   224bits
          - SHA-256   256bits
          - SHA-382   384bits
          - SHA-512   512bits
      
***
***
***
## 6.Data Integrity.
   - Hashing is used to provide Integrity.
        - Sender Calculates Digest From the message.
        - Sender sends Message and Digest.
        - Receiver Calculates Digest From received Message.
        - Receiver compares both digests.
             - If digests are identical, the message was not modified in transit.
```mermaid
graph LR
UserA((UserA))-->|Message + Digest|UserB((UserB))
```
- What if there is Middle Man , attacking and capturing.
```mermaid
sequenceDiagram
    UserA->>Attacker: Message+Digest
    Attacker->>UserB: Modified Message + New Modified Digest
   note over UserA,UserB: This is How Actual data should have travel |Message+Digest| <br>But Middle Man is Capturing the Message+Digest sended from userA ,<br> then attacker change the message+digest and send it to UserB.
```
- Interceptor can modify the message and re-calculate hash.
- Receiver compares their Digest against modified Digest
- Simply sending the Messages Digest is not sufficient.
     - Something else must be done
 
- Both parties establish a mutual Secret Key.
- Sender combines Message+Secret Key to create Diges.
- Receiver verifies by calculating hash of message + secret key.
     - Message was not modified in transit - **INTERGITY**
     - Sender has the identical Secret Key- **Authentication**

### Message Authentication Code(MAC):
   - Concept of Combining Message + Secret Key when Calculating Digest.
   - Provides Intergrity & Authentication for Bulk Data Transfer.
   - Message + secret key must be combined in the same way.
   - ***INDUSTRY STANDARD OF HOW TO COMBINE THE KEY  AND MESSAGE IS HMAC. i.e HASHING [KEY+MESSAGE] 
     IS DIFFERNET THAN HASHING [MESSAGE+KEY].***
   - Hash Based Message Authention code (RFC 2104)

### Hashing Algorithm:
   - Input: Message.
   - Output: Digest.
   - Example: MD5,SHA1,etc
### MAC-Message Authentication Code:
   - Input: Message + Secret Key.
   - Output: Digest
   - Exampe: HMAC (Hash Based Message Authentication Code).

***
***
***

## 7.Encryption:

- Encryption is used to provide Confidentiality.
   - Confidentiality: only intended recipent can interpret thedat.

```mermaid
graph LR
Hello(Plaintext)---|Encryption<br>Decryption|lohel(CipherText.)
```
   - Plain Text: Data before encryption & after decryption.
   - Cipher Text: Data while encrypted.
- Simple Encryption: Transforms PlainText into Cipher text.
     - Doesn't scale
     - Hard to do securely
     - Cannot simply use a standard algorithm.
**In real world Key Based Encryption is used**
- Key Based Encryption:
     - Combines industry vetted algotithm with a Secret Key.
          - Alogrithm is created by experts.
          - Secret keys can be randomly generated.
- There are two types of key based encryption:
     - Symmetric Encryption: Encrypt and Decrypt using the same keys.
     - Asymmetric Encryption: Encryprt & Decrypt using diferent key.

- For understanding this , lets take lowercase alphabets [ a b c d e f g h i j k l m n o p q r s t u v w x y z]
- **lets take the work hello and use the encryption using secret key 3 i.e move the alphabet by 3. similarly for decryptining moving backward the alphabet by 3]**

### SYMMETRIC KEY
```mermaid
graph LR
hello---|Encryption<br>secretkey=3|khoor
```
```mermaid
graph LR
hello---|Decryption<br>secret key = 3|khoor
```
 ### ASYMMETRIC KEY 
```mermaid
graph LR
hello---|Encryption<br>secretkey=5|mjqqt
```
```mermaid
graph LR
mjqqt---|Decryption<br>secret key = 21|hello
```
- *In Asymmetric encryption, one key is used foe encrption and other is for decryption*
   - Two different keys that are mathmatically related.
   - What one key encrpts, only the other can decrypt.
        - **One key will be made Public**
        - **Other Key will be kept Private**
- ***IF SOMEWANTS WANT TO SEND ME DATA, I SHOULD HAVE ALREADY SHARED MY PUBLIC KEY WHICH CAN BE ACCESS BY ANYONE , THE SENDER ENCRYPT THE DATA WITH MY PUBLIC KEY AND SEND IT TO ME, WHEN I RECEIVE THE DATA , ONLY MY PRIVITE KEY WILL BE ABLE TO DECRYPT IT***

  ### Strength and Weakness:
   #### Symmteric Encryption:
     - Stregth: Faster-Lower CPU Cost.
     - Strength: Ciper text is smae size as plain text.
     - Weakness: Secret Key must be shared-Less Secure.
     - Ideal for BULK DATA Transfer
   #### ASymmteric Encryption:
     - Weakness: Slower-requires much larger key sizes.
     - Weakness: Ciper text expansion
     - Strength: Private key is never shared-More Secure
     - restricted to limited Data


### SYMMETRIC Encryption Algorithms:
   - DES - 56BIT KEY
   - RC4 - 128 BIT KEY
   - 3DES-168 BIT KEY
   - AES -128,192 OR 256 BIT KEY
   - CHACHA20-128 OR 256 BIT KEY
     
### ASYMMETRIC Encryption Algorithms:
   - DSA
   - RSA
   - DIFFIE-HELLMAN
   - ECDSA
   - ECDH

## 8. PUBLIC & PRIVATE KEYS:
   - Asymmetric key pair can be used for : Encryption. (Confidentiality)
   ```mermaid
      graph LR
      Dipak(((Dipak<br>publickey🔑<br>privatekey🔒)))---Rajeev(((Rajeev<br>Publickey🔑<br>privatekey🔒)))
   ```
   - If Rajeev wants to send a secret message to Dipak.
   - Rajeev will use the Dipak's Public Key to Encrypt the message.
   - Then , ont the private key of Dipak can decrypt the message ( Only the correlating Private Key can Decrypt).

 - ***With Asymmetric key , we can also achive authentication and integrity***
 - When Dipak does not care about confidentiality but wants to prove that its Dipak who sended the Data then.
 - Dipak will use his own private key to encrypt the message.
 - Anyone with Dipak public key can decrypt the message. When Dipak sends message to rajeev by encrypting with his own privtae key, rajeev will use dipak's public ko decrypt the message.
 - If Rajeev is able to decrypt the message , it proves two things:
      - Rajeev know the message has been send by Dipak: **Authentication**
      - It also proves that message has not been modified in transit: Intergrity.
- ***THE PROCESS WE JUST DICUSS IS KNOWN AS SIGNATURE***
- Asymmetric Key Pairs can provide Encryption & Signatures.
  - But in reality, it is not that simple.
  - Remember, Asymmetric encryption has limitations
       - It can't be used for bulk data , but can use it for limited data.
  - Bulk data should be protected with symmteric encryption.
       - ![insightful](https://img.shields.io/badge/insightful-%F0%9F%92%A1-blue?style=for-the-badge)
            What if we used Asymmetric keys to share Symmetric keys?

- Dipak randomly generates a Symmteric Secret key.
- Dipak encrypt the "Symmteric secret key" with the public key of Rajeev & send it to rajeev.
- Rajeev decrypt the message with his private key, now both dipak and rajeev has a similar symmteric secret key.
- Bulk data can now be symmetrically encrypted.
     - can be done in either direction, for arbitary amount of data.
- ***THIS IS KNOWN AS HYBRID ENCRYPTION, CONCEPT OF USING BOTH ASYMMETRIC & SYMMETRIC ENCRYPTION.***
  
- But what about Signatures?
     - Entire message can't be "encrypted"  with private key.
          - Again , Asymmrteic encryption has limitatons.
     - Could we sign a fixed , representational sample of the message?
          - Yes, Hashing Algorithms.
          - Algorithms which taskes as input a message of arbitrary length & produces as output a "Fingerprint" of the original message.
      - ***PROCESS FOR USING AN ASYMMETRIC KEY PAIR FOR SIGNATURES:***
          - Dipak calulate the has of a message.i.e DIGEST
          - Dipak encrypts resulting digest with Dipak's Private key.
               - *THIS IS THE ACTUAL SIGNATURES*
          -  Rajeev will decrypts the signature with Dipak's public key.
          -  Rajeev will calculates hash of received message.
          -  If the digest generated by rajeev matched the digest send by Dipak. This proves two things:
            - Message has bot been changed. INTERGRITY.
            -  Only Dipak could have created the Signatures. Authentication.
- ***KEEP IN MIND MANY DIFFERENT ITEMS CAN BE SIGNED, NOT ONLY MESSAGES BUT ALSO CERTIFICATES, SOFTWARE, EMAIL,ETC***

***
***
***
## 9.HOW SSL/TLS USES CRYPTOGRPAHY?

|*******|******|********|
|-------|------|--------|
|Hashing|FingerPrint<br>MAC-Msg Auth Code|Intergrity & Authentication|
|Symmetric Encryption|Encryption|Confidentiality|
|Asymmetric Encryption|Key Exchange<br>Sigantures<br>Encryption|Establish Shared keys<br> Integrity & Authentication<br>Confidentiality|
***
***
***
## 10.PUBLIC KEY INFRASTRUCTURE (PKI)

 - Three entities that form a PKI: Client,Server & CA.
      - Client: Needs to connect securely or verify an indentity.
      - Server: Needs to prove its identity.
      - Certificate Authority: Validate identites & Generates Certificates.
        
- Most of us are Familiar with the PKI for WWW:
     - Client are Web Browsers: Chrome,Firefox,IE,etc.
     - Server are : Google.com,Youtube.com. reddit.com.twitter.com and any other HTTPS websites.
     - Certificate Authority are: IdenTrust,Digicert,sectigo,GoDaddy,GlobalSign.
- But other PKIs also exist:
     - Code Signing: When we try to install any software in PC , the diaglog box will apper where we see Progarm name, Veriifed Publisher, File origin. Here code signing PKI involves its own set of Client, server and CA.
          - Here , Client is Operating System, Software is server and there is Code signing CAs.

- There Could also be an internal Corporate PKI.
   - Client: Employee work computers/Phones
   - Server: Corportae Resources: Hr portal, ticketin system, etc.
   - CA: Corporate internal CA
***
***
***
## 11.RSA-Rivest Shamir Adleman
- Created in 1977 by, Ron Rivest, Adi Shamir , Leonard Adleman.
- Most Common Asymmetric Encryption algorithm.
- RSA creates a Pair of "Commutative" keys
     - i.e  Encrypt with one, Decrypt with other.
     - DH/DSA work differnetly

|FACTOR|PRIME|SEMI=PRIME|MODULO|
|------|-----|----------|------|
|Numbers you can multiply to get orignal number|Number whose factors are only 1 and itself|Numbers whose factors are prime numbers| Remainder [Division}|
|Factors of 12: <br> 1 2 3 4 6 14| 2 3 5 7 11 <br> 13 29 37 61| factors of semi-prime 21:<br> 1 3 7 21|13MOD5=3|
|Factors of 7:<br>1 7| NUmber divisible by only 1 and itself| Product of two primes is always sems-prime| 21MOD5=1<br>25MOD5=0)
  
### RSA EXAMPLE:
- Step 1: Genretaing keys.
- Step 2: Using the ley for encryption & Decryption.
 #### Genrating Keys:
   - Select two prime numbers (P&Q). Suppose P=7 Q= 19 
   - Calculate  product (P*Q) i.e 7*19=133 , lets says N=133
   - Calculate Totient: (P-1)*(Q-1)=6*18=108 , lets says, T=108
   - Select Public Key:
        - MUST BE PRIME NUMBER
        - MUST BE LESS THAN TOTIENT
        - MUST NOT BE A FACTOR OF TOTIENT
   - lets select the prime number :29 which is less than totient & is not a factor of totient. (PUBLIC key , E= 29)
   - Select Private Key. (D)
        - Product of D and E, divided by T must result in a remainder of 1.
        -  Private Key D=41
          
#### Encryption & Decryption:
   - Encryption:
        - Message^E MOD N = Cipher Text.
        - Cipher^D MOD N = Message.
     - Lets say our message value is 60, then encrypt with public key , Decrypt with private key.
          -(60^29) MOD 133 = 86 , cipher text - 86
          - (86^41) MOD 133 = 60 , message.
      

#### HOW SECURE IS RSA?
   - Security lies in difficulty of factoring semi-prime numbers.
        - If given the number 133, could you extract 7 & 19?
             - Really? ....Prove it, what are the factors of 1909?
   - In 1991, RSA Laboratory created the RSA challenge:
        - Released 54 semi-primes of various sizes and asked for Factors.
            - Competition ended in 2007- only 12 factors were identified.
            - As of 2020, another 11 were identified (no cash was awarded)
                  - Biggest number factored: 829bits (feb2020)
            - In 29 years, the 1024 bit number has never been factored.
                 - 1024 bit RSA keys was recommended standard since 2002.
                 - 2048 bit RSA keys is recommended standard since 2015

***
***
***
                   
## 12.Deffie-Hellman:
   - Allows two parties to establish a shared secret over an unsercured medium.
   - Shared secret is never transmitted , only values used to derive secret.

 ### Steps:
   - Both agree upon two numbers
   - P(Prime number)=13
   - G(Generator of P)=6
   - Both will generate the random number , suppose jehis generate private key 5  and nabin generates private key 4.
   -  Calculate Public Key: ( G^Private)MOD P = (6^5)MOD 13= 2
   -  Calculate public key of nabin : (6^4)MOD 13 = 9
   - Exchange the Public keys.
   - Calculate the shared secret key ( shared public&private)MOD p
   - jeshis shared sceret = 9^5MOD P = 3
   - Nabin shared Sceret = 2^4MOD P = 3

---
---
   - Establish Shared Secret over an unsecured medium
   - Shared secret is then used to generated symmetric keys.
***
***
***
## 13.DIGITAL SIGNATURE ALGORITHM:
- DSA is an asymmetric encryption algorithm.
     - Different than other algorithms we've disscused.
- RAS creates a pair of commutative keys. [RSA can be used for encryption, signatures and key exchange]
- Deffie-hellman. [Deffie hellman can only be used for key exchange]
- DSA simply created and validates signatures. No Encryption, No key exchange.

  |RSA|
  |---|
  |Encryption|
  |Signatures|
  |Key Exchange|

  |Deffie-Hellman|
  |--------------|
  |Key Exchange|

  |DSA|
  |---|
  |Signatures|

  
- DSA simply has two operations:
     ### Signature Generation:
     - input: 
```mermaid
         graph LR
          MESSAGE-->PRIVATEKEY-->RANDOM#NUMBER-->DSA_PARAMETERS
 ```
   - OUTPUT: Signatures

   ### Signature Verification:
   - INPUT :
     ```mermaid
     graph LR
     Message---PublicKey---Signatures---DsaParameters
     ```
     -OUTPUT: 1 or 0 (true or false)

     -***Random number is very impoertant: it must be unique for each message or DSA fails catastrophically, If random number is erver re-used , prviate key can be extracted***

***
***
***
## 14.GENERATING ADN INSPECTING RSAs.
 - Genrating RSA.
      - *openssl genrsa -out RSAkey1.pem 1024*
      - cat  RSAkey1.pem
 - Genrating RSA with encryption:
      - *openssl genrsa -out RSAkey2.pem -aes1256 2048*
      - cat  RSAkey2.pem.

 - View inside the RSAkey:
      - Openssl rsa -in RSAkey1.pem -text
![image](https://github.com/user-attachments/assets/3f76b4e9-4c19-482a-ac09-97ee02427f1b)

      - Openssl rsa -in RSAkey2.pem -text
![image](https://github.com/user-attachments/assets/6ab76fca-3e0f-477c-8d78-d6a675d92af0)

-***OPENSSL LIST -CHIPHER-ALGORITHMS**, list supported symmetric encryption algorithms that can be used to secure private key files.



***
***
***
## 15.GENERATING ADN INSPECTING DSA KEYS.
- Generating dsaparam file
     - openssl dsaparam -out dsaparam.pem 1024
     - cat dsaparam.pem
     - openssl dsaparam -in dsaparam.pem -text
     - 
![image](https://github.com/user-attachments/assets/768ee710-201e-43d6-b449-7ce56982953c)
-Generate DSA keys file with parameters file.
   - openssl gendsa -out dsa.pem dsaparam.pem
   - cat dsa.pem
   - openssl dsa -in dsa.pem -text
     ![image](https://github.com/user-attachments/assets/30623a25-c368-499c-a286-1a5e30f50a28)

- Generate DSA parameters and Keys in one file.
     - openssl dsaparam -genkey -out dsa-param-key.pem 2048 
![image](https://github.com/user-attachments/assets/a76bfd21-25ec-4a40-9998-c8f30d35a37a)
***
***
***
## 16.GENERATING ADN INSPECTING ELLIPTIC CURVE KEYS.
- Generate EC Parameters File:
     - openssl genpkey -genparam -algorithms EC -pkeyopt ec_paramgen_curve:secp384r1 -out EC-PARAM.pem
     - openssl ecparam -in EC-PARAM.pem -text -noout
- Generating EC keys from parameters file.
     - openssl genpkey -paramfile EC-PARAM.pem -out EC-KEY.pem
     - openssl ec -in EC-KEY.pem -text -noout
- Generate EC keys directly.
   - openssl genpkey -algorithms EC -pkeyopt ec_paramgen_curve:p-384 -out EC-KEY.pem
   - openssl ec -in EC-KEY.pem -text -noout

  -***openssl ecparam -list_curves***
  
***
***
***
## 17. Adding & Removing Encryption to RSA KEYS.
- openssl rsa -in RSA-KEY1.pem -aes256 -out RSA-KEY1-ENC.pem 
![image](https://github.com/user-attachments/assets/48b4fb9b-46be-4051-b707-ba82a2b5306a)
![image](https://github.com/user-attachments/assets/2335f94a-8b47-4476-9fe7-0bd3dfe29828)

- Remove Encryption from an RSA key file.
     - └─$ openssl rsa -in RSA-KEY1-ENC.pem -out RSA-KEY1-DEC.pem
     - ![image](https://github.com/user-attachments/assets/48addb83-983c-4a31-a96f-92532ce20c8e)
![image](https://github.com/user-attachments/assets/35ed7746-6355-48a4-ae9f-8741a57f2db7)

-***hash value of both RSA-KEY1-DEC.PEM AND RSA-KEY1.PEM***

***
***
***
## 18. OPENSSL PKEY UTILITY:
   - └─$ openssl pkey -in RSA-KEY1-DEC.pem -noout -text
         

   ![image](https://github.com/user-attachments/assets/f83a3dd1-9973-4594-b05a-23b2dd88d89c)
   - └─$ openssl pkey -in dsa.pem -noout -text
   - ![image](https://github.com/user-attachments/assets/ad44ffd8-9d38-42e6-b5c1-708e00bd0191)

   - *the pkey utility can do be used to inspect any .pem into text file*
   - *The pkey utility cannot do everything the algorithm-specific inspection utilities can do.
     ![image](https://github.com/user-attachments/assets/31b907c1-03de-420f-b769-6ed9c5c31d18)
   - IN RSA, the public key consits of two values: The modulus and a public exponent.
### Extracting only public key as text from any key file.
   - └─$ openssl pkey -in RSA-KEY1.pem -noout -text_pub

   - ![image](https://github.com/user-attachments/assets/d8e97efa-4b45-46d7-b612-ea8dfbda89ee)

### Extracting only Public key in PEM format.
   - └─$ openssl pkey -in RSA-KEY1.pem -pubout             
   - ![image](https://github.com/user-attachments/assets/0b1d08d9-1b05-4d6b-8f46-43d0a9337252)
---
---
---
## 19.MATCHING PRIVATE KEYS TO CERTIFICATES & CSRs.
   ### viewing x509 certificate as human readable text.
   - └─$ openssl x509 -in RSA-KEY1.pem -noout -text
   ### Viewing Certificate Signing request (CSR) contents as text.
   - └─$  openssl req -in RSA-KEY1.pem -noout -text 
---
---
---
## 20.Generating a CSRs(Certificate signing request-used to request a signed certificated from a certificate authority) & Certificatr using an ***existing Privtae*** key.
- x509 Certificate ( **Certificates like a public & Private key to identify- ex: a website or individual or orgazation, etc**)

- Generating the CSR : └─$ openssl req -new -key RSA-KEY1.pem -out RSA-KEY1-CSR.pem 
- Inspecting the CSR: └─$ openssl req -in  RSA-KEY1-CSR.pem  -noout -text

- Generating Self-Signed Certificates: └─$  openssl req  x509 -key RSA-KEY1.pem -out RSA-CERT.pem 
- └─$ openssl version -d                                      
      - OPENSSLDIR: "/usr/lib/ssl"

---
---
---
## 21.Generating a CSRs(Certificate signing request-used to request a signed certificated from a certificate authority) & Certificatr using an ***NEW*** Privtae key.
 - └─$ openssl req -new -out CSR.pem -subj "/CN=CommanName"
      - 10:27 privkey.pem
      - 10:27 CSR.pem
 - ![image](https://github.com/user-attachments/assets/cf716d13-d3c3-4e76-87d6-3538e5ce5691)
    
 - └─$ openssl pkey -in privkey.pem -noout -text

---
---
---
## 22. Extracting Specific Information from Certificates & CSRs.
- Viewing x509 certificate as human readable text
     - openssl x509 -in cert.pem -noout -text
     - openssl x509 -in cert.pem -noout -text -dates
     - openssl x509 -in cert.pem -noout -text -issuer -subject

   
- Viewing Certificate signing request(CSR) content as text.
     - openssl req -in CSR.pem -noout -text
---
---
---
## 23. Check if file is PEM,DER or PFX:
   - └─$ openssl x509 -in cert_export_CLIENT.p12
     
---
---
---
## 24. Converting files between PEM & DER Formats.
   - └─$ openssl x509 -in cert_export_CLIENT.p12
