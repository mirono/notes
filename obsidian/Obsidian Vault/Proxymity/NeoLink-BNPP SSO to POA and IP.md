# New Auth0 Tenant
I created a new Auth0 tenant to simulate NeoLink/BNPP IdP:
proxymity-neolink

The tenant has a single DB connection:
Username-Password-Authentication

And it holds the simulated NeoLink users.

Create a new app:
Neolink-Simulated-idp (Regular Web App)
Download the certificates (from Advanced Settings) - proxymity-neolink.pem
SAML Protocol URL (from Endpoints):
https://proxymity-neolink.eu.auth0.com/samlp/maO1TSIaoUjGVZibziYSZVwTHq6SCZoW
SAML Metadata URL:
https://proxymity-neolink.eu.auth0.com/samlp/metadata/maO1TSIaoUjGVZibziYSZVwTHq6SCZoW

After creating the SAML connection (described below) and getting the connection details, we can continue to configure the IdP.

Set the Application Callback URL to the SAML connection ACS URL.
Set the audience: "audience":  "urn:auth0:proxymity-auth0:NeoLink-Simulated",

![[Pasted image 20240827151745.png]]

We can test it now via the "Debug" button. After loging in with the "NeoLink" credentials we should see a decoded SAML response:
![[Pasted image 20240827151716.png]]

The SAML Audience should match the Entity ID of the connection (below):
<saml:Audience>urn:auth0:proxymity-auth0:NeoLink-Simulated</saml:Audience>

Details:
https://auth0.com/docs/authenticate/protocols/saml/saml-configuration/configure-auth0-as-service-and-identity-provider

# Add Simulated NeoLink IdP to POA SSO
In the dev tenant: proxymity-auth0

## Create SAML connection
Create a new Enterprise Connection (SAML):
Name: NeoLink-Simulated
Sign-In URL: the SAML protocol url of the IdP (see above). Same for Sign-Out URL.
For signing certificate - upload the certificate downloaded above.

URL from Setup tab: https://proxymity-auth0.eu.auth0.com/p/samlp/d2nKoVgj
AssertionConsumerService URL: 
https://proxymity-auth0.eu.auth0.com/login/callback?connection=NeoLink-Simulated

After completing the IdP setup, we can test this connection with the "Try" button.

Since the connection was associated with POA app, you should probably be presented with an MFA screen. After putting the MFA code, if everything is configured OK you should see something like:

![[Pasted image 20240827153311.png]]

## Create Social SAML Connection
Another option is what we explored in previous POCs - use a social connection - custom SAML.
Create a new custom social connection - NeoLink.
Authorization URL: https://proxymity-neolink.eu.auth0.com/authorize
Token URL: https://proxymity-neolink.eu.auth0.com/oauth/token
Scope: openid profile email
Client ID: maO1TSIaoUjGVZibziYSZVwTHq6SCZoW
Client Secret: u9Livg5O9IVwDg6OrqJCYVWstDGnhbIsGrWDZYMFFdesDPv47DiDsb2oDCtOxMg9
(From the app created in the IdP tenant)

With a fixed userInfo call, this works as expected:
> function(accessToken, ctx, cb) {
  const profile = {
    user_id: "auth0|61bf410910714a00693fba83",
    email: "m.ophir@proxymity.io"
  };
  cb(null, profile);
}> 

![[Pasted image 20240827165010.png]]

To get the user info from the IdP:
...
I was not able to map attributes this way.

# Testing With Local IdP Simulator
## IdP Simulator
## I used Samling from:
https://fujifish.github.io/samling/

Run as Docker:
Docker file:
```
## Build
FROM node:22-alpine3.19
WORKDIR /usr/src/app

ARG NODE_ENV
ENV NODE_ENV $NODE_ENV

COPY package.json /usr/src/app/
RUN npm install

COPY . /usr/src/app/
RUN npm run build && npm cache clean --force

## Server
FROM nginx:stable-alpine
COPY --from=0 /usr/src/app/public /usr/share/nginx/html/public/
COPY --from=0 /usr/src/app/samling.html /usr/share/nginx/html/index.html
```
Build it:
```sh
docker build -t fujifish/samling .
```
Run it:
```sh
docker run -d -p 8081:80 fujifish/samling
```
After running it the IdP Url for SSO will be:
http://127.0.0.1:8081/
## Create SAML connection
Create a new Enterprise Connection (SAML):
Name: samling
Sign-In URL: the SAML protocol url of the IdP:
http://127.0.0.1:8081/
For signing certificate - take the certificate from Sampling page:
load http://127.0.0.1:8081/
![[Pasted image 20240903202400.png]]
All other settings are the default ones (no logout, no encryption)

Invoke the SSO flow:
https://proxymity-auth0.eu.auth0.com/authorize?client_id=kveyh31ZSbyhH7gW3F8Y86faB2jQfwDX&response_type=code&redirect_uri=https://app-poa-dev.proxymity.io/&connection=samling

When Invoked, the Samling UI is shown:
![[Pasted image 20240903202641.png]]
Name Identifier: this will be the user name (or part of the user name when federated).
Assertion Consumer URL: https://proxymity-auth0.eu.auth0.com/login/callback?connection=samling
SAM Attributes:
```
company=PROXIMITY
emailaddress=m.ophir@proxymity.io
givenname=Miron
surname=Ophir
roles=IP_ADMIN
```

After pressing 'Next' button we can see the SAML response.
After pressing 'POST' the IdP SAML response is returned to Auth0. The resulting user info:

```JSON
{
    "authenticationmethod": "urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified",
    "created_at": "2024-09-03T11:58:32.186Z",
    "identities": [
        {
            "user_id": "samling|123456",
            "provider": "samlp",
            "connection": "samling",
            "isSocial": false
        }
    ],
    "issuer": "http://127.0.0.1:8081/",
    "name": "",
    "nameIdAttributes": {
        "value": "123456",
        "Format": "urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified"
    },
    "nickname": "",
    "picture": "https://cdn.auth0.com/avatars/default.png",
    "sessionIndex": "_samling_2212001_1614504587",
    "updated_at": "2024-09-03T13:18:08.274Z",
    "user_id": "samlp|samling|123456",
    "company": "PROXIMITY",
    "emailaddress": "m.ophir@proxymity.io",
    "givenname": "Miron",
    "roles": "IP_ADMIN",
    "surname": "Ophir",
    "last_ip": "2a0d:6fc2:5ea1:eb00:686f:7e93:22d4:54de",
    "last_login": "2024-09-03T13:18:08.274Z",
    "logins_count": 7,
    "blocked_for": [],
    "guardian_authenticators": [],
    "passkeys": []
}
```

# Testing With BNPP/NeoLink IdP
## Create SAML connection
Create a new Enterprise Connection (SAML):
Name: NeoLink
Sign-In URL: the SAML protocol url of the IdP (see above). Same for Sign-Out URL.
https://ssologinrec.bnpparibas.com/saml/service/idp
For signing certificate - take the certificate from BNPP metadata URL:
https://ssologinrec.bnpparibas.com/saml/service/metadata
User ID Attribute: NameID
Protocol Binding: HTTP-Redirect

Invoke the SSO flow:
https://proxymity-auth0.eu.auth0.com/authorize?client_id=kveyh31ZSbyhH7gW3F8Y86faB2jQfwDX&response_type=code&redirect_uri=https://app-poa-dev.proxymity.io/&connection=NeoLink

After login into the IdP: the raw user record in Auth0 is:
![[Pasted image 20240903200839.png]]

```JSON
{
    "authenticationmethod": "urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport",
    "company": "PROXIMITY",
    "created_at": "2024-09-03T11:33:05.065Z",
    "emailaddress": "m.ophir@proxymity.io",
    "givenname": "Miron",
    "identities": [
        {
            "user_id": "NeoLink|40813347",
            "provider": "samlp",
            "connection": "NeoLink",
            "isSocial": false
        }
    ],
    "issuer": "urn:federation:sso:bp2s:idp",
    "name": "",
    "nameIdAttributes": {
        "value": "40813347",
        "Format": "urn:oasis:names:tc:SAML:1.1:nameid-format:X509SubjectName"
    },
    "nickname": "",
    "picture": "https://cdn.auth0.com/avatars/default.png",
    "roles": "IP_ADMIN",
    "sessionIndex": "2XfnId1nqttJEYtHacakzfFyMqOjyZn7XTVN2uq5looPB150NS",
    "surname": "OPHIR",
    "updated_at": "2024-09-03T13:21:04.968Z",
    "user_id": "samlp|NeoLink|40813347",
    "last_ip": "2a0d:6fc2:5ea1:eb00:686f:7e93:22d4:54de",
    "last_login": "2024-09-03T13:21:04.967Z",
    "logins_count": 3,
    "blocked_for": [],
    "guardian_authenticators": [],
    "passkeys": []
}
```

# Token and Claims
The generated token for database connection (normal login) user is:
```json
{
  "https://proxymity.io/email": "m.ophir@proxymity.io",
  "https://proxymity.io/email_verified": true,
  "https://proxymity.io/identity_user_id": "649ada8aa6fa9fe40a1689ce",
  "https://proxymity.io/roles": [],
  "iss": "https://proxymity-auth0.eu.auth0.com/",
  "sub": "auth0|649ada8aa6fa9fe40a1689ce",
  "aud": [
    "https://poa",
    "https://proxymity-auth0.eu.auth0.com/userinfo"
  ],
  "iat": 1725440686,
  "exp": 1725442486,
  "scope": "openid profile email",
  "azp": "kveyh31ZSbyhH7gW3F8Y86faB2jQfwDX",
  "permissions": []
}
```
While the generated token for NeoLink/Federated user is:
```json
{
  "https://proxymity.io/identity_user_id": "NeoLink|40813347",
  "https://proxymity.io/roles": [],
  "iss": "https://proxymity-auth0.eu.auth0.com/",
  "sub": "samlp|NeoLink|40813347",
  "aud": [
    "https://poa",
    "https://proxymity-auth0.eu.auth0.com/userinfo"
  ],
  "iat": 1725440921,
  "exp": 1725442721,
  "scope": "openid profile email",
  "azp": "kveyh31ZSbyhH7gW3F8Y86faB2jQfwDX",
  "permissions": []
}
```
So we need to add the missing token fields...

We have an Action in Auth0 called `SetCustomClaimClientIdentity` which is doing some claims manipulation:
```node
/**
* Handler that will be called during the execution of a Client Credentials exchange.
*
* @param {Event} event - Details about client credentials grant request.
* @param {CredentialsExchangeAPI} api - Interface whose methods can be used to change the behavior of client credentials grant.
*/

exports.onExecuteCredentialsExchange = async (event, api) => {
if(event.request.body?.client_identity){
api.accessToken.setCustomClaim("https://proxymity.io/client_identity",  event.request.body.client_identity);
}

if(event.request.body?.transaction_id){
api.accessToken.setCustomClaim("https://proxymity.io/transaction_id", event.request.body.transaction_id);
}

};
```
This Action is used in the M2M flow:
![[Pasted image 20240904121830.png]]
Another Action that manipulate claims is ``:
```node.js
/**
* Handler that will be called during the execution of a PostLogin flow.
*
* @param {Event} event - Details about the user and the context in which they are logging in.
* @param {PostLoginAPI} api - Interface whose methods can be used to change the behavior of the login.
*/

exports.onExecutePostLogin = async (event, api) => {
	var map = require('array-map');
	const namespace = "https://proxymity.io";

	console.log(event.user.app_metadata.global_reporting_role);

	try {
		api.idToken.setCustomClaim(`${namespace}/roles`,
		event.authorization.roles);
		api.accessToken.setCustomClaim(`${namespace}/roles`,
		event.authorization.roles);
		api.idToken.setCustomClaim('reporting_role',
		event.user.app_metadata.reporting_role);
		api.accessToken.setCustomClaim('reporting_role',
		event.user.app_metadata.reporting_role);

		var ManagementClient = require('auth0').ManagementClient;
		var management = new ManagementClient({
			domain: event.secrets.domain,
			clientId: event.secrets.clientId,
			clientSecret: event.secrets.clientSecret
		});

		var params = { id: event.user.user_id, page: 0, per_page: 50,
		include_totals: false };

		let userPermissions = await management.getUserPermissions(params);
		var permissionsArray = map(userPermissions, function (permission)
		{
			return permission.permission_name;
		});
  
		api.accessToken.setCustomClaim("https://proxymity.io/access",
		permissionsArray.join(" "));

	} catch (e) {
		console.log(e);
	}
};
```

To add the NeoLink missing details, I created a new PostLogin action:
`Add NeoLink User Details`

```node.js
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


![[Pasted image 20240904130416.png]]

And the resulting token is:
```json
{
  "https://proxymity.io/identity_user_id": "NeoLink|40813347",
  "https://proxymity.io/roles": [],
  "https://proxymity.io/email": "m.ophir@proxymity.io",
  "iss": "https://proxymity-auth0.eu.auth0.com/",
  "sub": "samlp|NeoLink|40813347",
  "aud": [
    "https://poa",
    "https://proxymity-auth0.eu.auth0.com/userinfo"
  ],
  "iat": 1725443876,
  "exp": 1725445676,
  "scope": "openid profile email",
  "azp": "kveyh31ZSbyhH7gW3F8Y86faB2jQfwDX",
  "permissions": []
}
```

Which finally got me into the app successfully.


Added this to the NeoLink mappings:
```json
{
  "name": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier",
  "email": "emailaddress"
}
```
