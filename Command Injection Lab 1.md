* Lab Name:  OS Command Injection, Simple case
* Level: Apprentice
* Platform: PortSwigger

# Procedure

* I started Burp Suite (I have community edition).

* In the proxy tab, I turned on the intercept.

* I navigated to my lab through Chromium browser and checked on one of the items.
<img width="1364" height="631" alt="CI1" src="https://github.com/user-attachments/assets/b8605820-ef97-4fb7-ac10-f59e64745c5c" />

* Then, I clicked the option of checking stock which I intercepted in BurpSuite.
I manipulated the request by adding ";whoami" after storeId (productId and storeId were at the end of request body).
';' indicates the end of the command and 'whoami' reveals the user's name of server.
<img width="1365" height="615" alt="CI2" src="https://github.com/user-attachments/assets/bde052b2-4f1e-483c-8c9c-f2fc0247c036" />
