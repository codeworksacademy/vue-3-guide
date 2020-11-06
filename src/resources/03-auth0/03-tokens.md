# JSON Web Tokens (JWT)

Tokens are an open, industry standard [RFC 7519](https://tools.ietf.org/html/rfc7519) method for representing claims securely between two parties. Signed tokens can verify the integrity of the claims contained within it, while encrypted tokens hide those claims from other parties. When tokens are signed using public/private key pairs, the signature also certifies that only the party holding the private key is the one that signed it.

Tokens are generally comprised of three parts the `header`, `payload`, and `signature`. 

## Header

The header typically consists of two parts: the type of the token, and the algorithm used for signing the token, such as `HMAC` `SHA256` or `RSA`. The header is encoded with [`base64`](https://en.wikipedia.org/wiki/Base64)

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

## Payload & Claims
The second part of the token is the payload, which contains claims. Claims are statements about a user with some additional data. There are three types of claims: registered, public, and private claims.

- **Registered**
    - These claims provide basic interoperable details such as a unique identify, the token issuer, and the intended audience of the token. 
- **Public**
    - Public claims need to be namespaced and usually include the domain of the application where they are used.
        ```json
        {
            "https://blog.domain.com/profile": { "displayName": "Jimmy Tester" },
            "https://game.domain.com/profile": { "displayName": "Jimmy117" },
        }
        ```
- **Private**
    - These claims are intended to convey information that is private and not user driven. These claims can include the users permissions for a particular application 
        ```json
        {
            "https://blog.domain.com/permissions": ["create:blog", "edit:blog"],
            "https://admin.domain.com/roles": ["moderator"],
            "https://admin.domain.com/permissions": ["delete:comment"]
        }
        ```

> **IMPORTANT** The data in signed tokens is only [`encoded`](https://danielmiessler.com/study/encoding-encryption-hashing-obfuscation/#:~:text=Encoding%20is%20for%20maintaining%20data,order%20to%20return%20to%20plaintext.), it is readable by anyone. Do not put secret information in the payload or header elements of a JWT unless it is [`encrypted`](https://danielmiessler.com/study/encoding-encryption-hashing-obfuscation/#:~:text=Encoding%20is%20for%20maintaining%20data,order%20to%20return%20to%20plaintext.).


## Signature
The signature is used to verify the message wasn't changed along the way, and, in the case of tokens signed with a private key, it can also verify that the sender of the JWT is who it says it is.


## Identiy Access Management IAM

Auth0 makes it simple to exchange `JWT` and allows managers to easily adjust user claims through its interface. Admins can also track and monitor how users move through their applications and expire sessions with suspecious activity.