# POA invoke Link
## DEV

https://proxymity-auth0.eu.auth0.com/authorize?client_id=kveyh31ZSbyhH7gW3F8Y86faB2jQfwDX&response_type=code&redirect_uri=https://app-poa-dev.proxymity.io/&connection=NeoLink

## UAT
https://proxymity-auth0-uat.eu.auth0.com/authorize?client_id=9wWTKET834CgBBVq0GgZ7qjVr4mDAvLW&response_type=code&redirect_uri=https://app-poa-uat.proxymity.io/&connection=NeoLink


## PROD
https://proxymity-auth0-prod.eu.auth0.com/authorize?client_id=TBD&response_type=code&redirect_uri=https://app-poa.proxymity.io/&connection=NeoLink


----
# Investor Portal invoke Link
## DEV
https://proxymity-auth0.eu.auth0.com/authorize?client_id=RcFbzM7hkkM17K6lNfZatx25587d4jsr&response_type=code&redirect_uri=https%3A%2F%2Finvestor-fte.proxymity.io/login&connection=NeoLink

## UAT
https://proxymity-auth0-uat.eu.auth0.com/authorize?client_id=s3W0JYRWIl6xS0tEHp74O5fAfdlyJkq0&response_type=code&redirect_uri=https%3A%2F%2Finvestor-uat.proxymity.io/login&connection=NeoLink

## PROD
https://proxymity-auth0-prod.eu.auth0.com/authorize?client_id=TBD&response_type=code&redirect_uri=https%3A%2F%2Finvestor.proxymity.io/login&connection=NeoLink
---

155
n.agabyev@proxymity.io

Add mapping to NeoLink enterprize connection
```json
{
  "name": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier"
}
```

Add to CITI TEST org
Add IP_USER role
Add 40813347 to proxymity users table
Add NeoLink connection to CITI TEST

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

  

try {
	if (event.user.identities[0].connection == "NeoLink") {
	api.multifactor.enable("none");
	return;
	};
} catch(e) {
	console.log(e);
}

  

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
// const authMethods = event.authentication?.methods || []
// const completedMfa = !!authMethods.find((method) => method.name === 'mfa')
// if (!completedMfa) {
// api.multifactor.enable('any', { allowRememberBrowser: true })
// }
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








## Vanity URLS


https://bitbucket.org/proxymityio/proxymity.infra/pull-requests/160
https://bitbucket.org/proxymityio/proxymity.infra/branch/feature/PV-17443-create-a-pretty-client-url-use-

https://proxymity.atlassian.net/browse/PV-17443

I shared services account:

1. Create a bucket neolink-ip-dev.proxymity.io
    
2. Set the bucket “Static Web Hosting” enabled and use the redirect option:
    
    ![[Pasted image 20241014170018.png]]
    
3. Add to the redirect URL at the end: `&fixslash=true` because S# adds / at the end of the redirect URL which made the connection name NeoLink/ instead of NeoLink and it was not recognized by Auth0.
    
4. Define Route 53 A record for `neolink-ip-dev.proxymity.io` and point it to the S3 endpoint:
    

   ![[Pasted image 20241014170005.png]] 
5. Same process can be applied to POA.

