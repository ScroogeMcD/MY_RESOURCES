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
  * Subject is the user, and claims are assertions about the user.
  * None of the claims are mandatory, although some of the claims have definite meanings. These specific claims are called **registered claims**.
  * Examples of registered claims:   
    * **iss** : issuer            : who issued the toke (the security token service STS)
    * **sub** : subject           : who the claims represent
    * **aud** : audience          : the recepient the token was meant for
    * **exp** : expiration (time) : until when the token is valid
    * **nbf** : not before (time)
    * **iat** : issued at (time)  : time at which token was issued
    * **jti** : JWT ID            : Unique token identifier
</details>
<details>
  <summary>signature</summary>
</details>

#### An example generation of JWT
```javascript
const encodedHeader = base64(utf8(JSON.stringify(header)));
const encodedPayload = base64(utf8(JSON.stringify(payload)));   
const signature = base64(hmac(`${encodedHeader}.${encodedPayload}`,secret, sha256));
const jwt = `${encodedHeader}.${encodedPayload}.${signature}`;
```

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

#### Federated Identity
A Federated Identity in Information Technology is the means of **linking a person's electronic identity and attributes, stored across multiple identity management systems**. Federated Identity is the means by which an authenticating party can attest to a third party that it had sucessfully authenticated someone or something. The third party accepts the attestation provided by the authenticating part based on mutual trust previously established between the parties, and as a result waives the requirement to authenticate the access requesting party.

Technologies used for federated identity include SAML(Security Assertion Markup Language), OAuth, OpenID, Security Tokens (Simple Web Tokens, JSON Web Tokens, SAML Assertions).
