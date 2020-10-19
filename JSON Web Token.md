## JSON Web Token (JWT)


A **JWT** is represented in the form of three strings separated by dot. For example : *aaaaaaaaaaa.bbbbbbbbbbbbbb.ccccccccccc*. These three parts are called:  
<details>
  <summary>header</summary>
  * it is a JSON structure
  * ex : {"alg" : "HS256", "typ" : "JWT"}
  * for unencrypted JWTs, the header is simply {"alg" : "none"}
</details>
<details>
  <summary>payload</summary>
  * it is also a JSON structure
  * ex : {"sub" : "1234567890", "name" : "Test1", "admin" : true}
  * None of the claims are mandatory, although some of the claims have definite meanings. These specific claims are called **registered claims**.
  * Examples of registered claims:   
    * **iss** : issuer
    * **sub** : subject
    * **aud** : audience
    * **exp** : expiration (time)
    * **nbf** : not before (time)
    * **iat** : issued at (time)
    * **jti** : JWT ID
</details>
<details>
  <summary>signature</summary>
</details>

#### Two key aspects of JWT are :
* possibility of **signing** them using **JWS** (JSON Web Signatures) 
  * If somebody changes the content of the message, the signature generated from this modified message will no longer match the original.
* possibility of **encrypting** them, using **JWE** (JSON Web Encryption)
  * One cannot decrypt and read the message without the decryption key     
  
#### Some functions in a Javascript 2015 environment:   
* **base64** : a function that receives an array of octets and returns a new array of octets using the Base-64 URL algorithm
* **utf8** : a function that receives text in any encoding and returns an array of octets with UTF-8 encoding
* **JSON.stringify** : a function that takes a javascript object and serializes it to String (json) form
* **sha256** : a function that returns an array of octets, and returns a new array of octets using the SHA-256 algorithm
* **hmac** : a function that takes a SHA function, an array of octets and a secret, and returns a new array of octets using the HMAC algorithm
* **rsassa** : a function that takes a SHA function, an array of octets and the private key, and returns a new array of octets using the RSASSA algorithm.
