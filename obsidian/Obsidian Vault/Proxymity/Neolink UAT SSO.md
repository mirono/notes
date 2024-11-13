![[Pasted image 20241028163904.png]]
![[Pasted image 20241028163940.png]]
Mappings:
```json
{
  "name": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier",
  "email": "emailaddress",
  "multifactor": [
    "none"
  ]
}
```

UAT SAML Request:
```xml
<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol"    	Destination="https://ssologinrec.bnpparibas.com/saml/service/idp"    ID="_4ce1994bffe91531d90510c85e65d095"    IssueInstant="2024-10-28T15:08:59Z"    ProtocolBinding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Version="2.0">
<saml:Issuer xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion">urn:auth0:proxymity-auth0-uat:NeoLink</saml:Issuer>
</samlp:AuthnRequest>
```

DEV SAML Request:
```xml
<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol"    	Destination="https://ssologinrec.bnpparibas.com/saml/service/idp"    ID="_a25eff4ad3eeaa38d6d51535e0157acd"    IssueInstant="2024-10-28T15:18:58Z"    ProtocolBinding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Version="2.0">
<saml:Issuer xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion">urn:auth0:proxymity-auth0:NeoLink</saml:Issuer>
</samlp:AuthnRequest>
```

![[Pasted image 20241106180205.png]]

Investor Portal SSO link:

https://proxymity-auth0-uat.eu.auth0.com/authorize?client_id=s3W0JYRWIl6xS0tEHp74O5fAfdlyJkq0&response_type=code&redirect_uri=https%3A%2F%2Finvestor-uat.proxymity.io/login&connection=NeoLink

POA SSO link:

https://proxymity-auth0-uat.eu.auth0.com/authorize?client_id=9wWTKET834CgBBVq0GgZ7qjVr4mDAvLW&response_type=code&redirect_uri=https://app-poa-uat.proxymity.io/&connection=NeoLink

# Disable MFA for getAccessTokenSilently
```js
// --- AUTH0 ACTIONS TEMPLATE https://github.com/auth0/os-marketplace/blob/main/templates/add-email-to-access-token-POST_LOGIN ---  
/**  
 * Handler that will be called during the execution of a PostLogin flow.  
 *  
 * @param {Event} event - Details about the user and the context in which they are logging in.  
 * @param {PostLoginAPI} api - Interface whose methods can be used to change the behavior of the login.  
 */  
exports.onExecutePostLogin = async (event, api) => {  
    
  if (event.client.name == 'POA') {  
     let authMethods = [];  
    if (event.authentication && Array.isArray(event.authentication.methods)) {  
      authMethods = event.authentication.methods;  
    }  
    const isFederated = Boolean(  
      authMethods.find((method) => method.name === "federated")  
    );  
    if (isFederated) {  
      api.multifactor.enable("none");  
    }  
  }  
  
  // try {  
  //   if (event.user.identities[0].connection == "NeoLink") {  
  //     api.multifactor.enable("none");  
  //     return;  
  //   };  
  // } catch(e) {  
  //   console.log(e);  
  // }  
    
  
  if (event.client.name !== 'Investor Portal') {  
    return;  
  }  
  
  const isRefreshTokenProtocol =  
    event?.transaction?.protocol === "oauth2-refresh-token";  
  let authMethods = [];  
  if (event.authentication && Array.isArray(event.authentication.methods)) {  
    authMethods = event.authentication.methods;  
  }  
  const isMFAAuthenticated = Boolean(  
    authMethods.find((method) => method.name === "mfa")  
  );  
  
  if (isMFAAuthenticated || isRefreshTokenProtocol) {  
    api.multifactor.enable("none");  
  }  
};  
// exports.onExecutePostLogin = async (event, api) => {  
//   const authMethods = event.authentication?.methods || []  
  
//   const completedMfa = !!authMethods.find((method) => method.name === 'mfa')  
  
//   if (!completedMfa) {  
//     api.multifactor.enable('any', { allowRememberBrowser: true })  
//   }  
// };  
/**  
 * Handler that will be invoked when this action is resuming after an external redirect. If your  
 * onExecutePostLogin function does not perform a redirect, this function can be safely ignored.  
 *  
 * @param {Event} event - Details about the user and the context in which they are logging in.  
 * @param {PostLoginAPI} api - Interface whose methods can be used to change the behavior of the login.  
 */  
// exports.onContinuePostLogin = async (event, api) => {  
// };
```

Disable MFA for NeoLink:

```js
try {    
    if (event.user.identities[0].connection == "NeoLink") {    
      api.multifactor.enable("none");    
      return;    
    };    
} catch(e) {    
    console.log(e);    
}
```

# Add NeoLink User Details
```js
exports.onExecutePostLogin = async (event, api) => {  
  try {  
    const namespace = "https://proxymity.io";  
    if (event.user.identities[0].connection == "NeoLink") {  
      api.accessToken.setCustomClaim(  
        `${namespace}/email`,   
        event.user.emailaddress);  
      api.idToken.setCustomClaim(  
        `${namespace}/email`,   
        event.user.emailaddress);  
    };  
  } catch(e) {  
    console.log(e);  
  }  
};
```

