1. start with application
2. First i need to log in.
3. who's allowed to use app
4. access a resource. (call an API)
5. the authoriziation isn't managed by the APP, it is farmed out to an authentication "provider"
6. Microsoft Identity.  (IAM) (Identity and Access Mgmt), MFA (Multi Factor Auth.
e.g MFA: something you remember (pin), something you have (phone, email), something you are (fingerprint)

Steps:
1. registration:  provide phone number and email - (something you have)
1a. provide password.  So for MFA you have #1 being the password (password manager systems)
1b. login API can send verification code to email or phone
1c. user matches the verification code to be verified
other MFA: FIDO key(physical fob), TOPT (time one time password like what MS Authenticator spits out every N minutes)


2. authentication will use those to send somet

How app uses Microsoft Identity.
1. app requests (of MI) an ID token (user authentication)
- this is an "OpenID" standard JWT (JSON Web Token) pron. "jawt"
2. app requests an access token to call API on behalf of the user
- both of these happen over a shared web service



1. web "surface" is used for a private conversation between user and MI
(developer of app has no visibility into this, in other words their app cannot somehow intercept any part of this conversation)
- the identity provider has set up the policy of what it will need from the user to have this conversation, and it all happens via that web "surface".
- artifacts (session state, cookies) are left on machine to allow for single sign on - like when you go to use github again and you are not prompted.


Note: mobile app access is a bit more complicated.  "embedded web views" 
- use M cell mobile libraries to handle

## ID Token
the ID Token is a JWT ((OpenID standard format)).
- signature - how it's been signed, plus the signature itself
- housekeeping (claims)
-- iss - issuer to trust - lkooks
-- aud - audience claim - identifies your application that is allowed to be used by this token
    "this tokens aud is this application identified by the hash value"
-- exp, iat, nbr - timeframes of the token.  exp = expires, iat = issued at, nbr = not use before
- user information
-- sub = subject claim  unique id for this user for this app (some sort of hash)
-- oid = object ID = long=lived
-- tid = tenant ID  - long-lived
-- name, username = do not use as keys as they change.  use sub, oid or tid

## Access Token.
If the Identity manager does not send an access token back to the app,
then the app is not permitted to use the API.

An access token is not required to be JWT, so don't assume it is one.
This token is created between API implementer and Microsoft Identity
So, Microsoft Graph API has an access token set up in Microsoft Identity.
- could change at any time

The Access Token is put onto the HTTP header from "app" to the API, and API will authenticate
the access token. (bearer token in auth header of the HTTP request.  API validates the signature,
validates the  aud and lifespan of the token)

If an application uses several APIs, then this process of getting access token has to be done for each

This token management is taken care of by the MSAL libraries.

Primary set of Transactions (summary):
- Apps request toeksn from Microsoft Identity
- Authentication happens over shared web surfaces to provide SSO
- App pass Access Tokens to APIs
- API validate Access Tokens before returning results
- Recommendation: use an Identity provider

Avoid "embedded web views" because they prevent the "shared web surface"
requirement. A "shared web surface" is like login.microsoft.com it is public and shared.