
title Auto Provision NeoLink
actor User (Browser)
User (Browser)->Auth0: IP SSO link
Auth0->User (Browser): Redirect to IdP
User (Browser)->NeoLink: authorize
note over NeoLink: login or already logged in
NeoLink->Auth0: idpresponse
Auth0->Post Login: User details
Post Login->User Management: Provision User (with User Details)
User Management->Auth0: Check user organization
opt no org
    Auth0->User Management: No org
    User Management->Auth0: create organization
    User Management->Auth0: Add user to organization
end
User Management->Auth0: Update user roles
note over User Management: 
    Upsert user in Proxymity DB 
    with company id
end note
User Management-->Post Login: success
Post Login->Auth0: Action complete
note over Auth0: Generate JWT token
Auth0-->User (Browser): Redirect to IP app

#### user-management-service:
Auth0 management client domain:
proxymity-auth0.eu.auth0.com

Auth0 management client Id:
auth0.m2m.organisation.management.clientId
JOou6JyIyeLRmGyH0IXrKFZYNmiN7k7V

Secret:
auth0.m2m.organisation.management.secret
jRi5cHszTbCaPr7kxF8rou3gyxZgnPVj_puKfXAZFZUIaMgNLJIgfwxEUBQR2BWk

Action dependencies:
`axios@1.7.7`
`auth0@4.2.0`

today:
`auth0@4.15.0`
`axios@1.7.9`


We have Auth0 app that have a database connection for regular (username, password) users and an Enterprise SAML connection for SSO users from another company. The app requires that a user will be associated to an org so we created a Post-Login action to associate the user to and org.
The problem is that sometimes (not consistent) auth0 will issue an error that the user has no orgs. We have some questions:
- We assumed the app login flow after SSO redirect will be called only after Post Login actions complete. Is this assumption correct?
- Is it possible to change the error message about missing org or customize it with additional text?
- We also tried to call the action in Post Registration trigger but it seems it is not invoked for users created by SSO/Enterprise. Is this correct?
Given the above - do you have suggestions on how we can achieve the requirement to associate a user to an org before the app is handling the login flow so it will not error?
Thank you,
Miron


Need to use different m2m app client: **Post Login Action Management API Client**
Domain: proxymity-auth0.eu.auth0.com
Client id: YoZ4uOwRMw9kxC8mgNaZNMnr4WAKfcht
Client secret: k70J0_HDtz88rZ9jNQOuSkBZV1ePwXFfpELPoKcY0tEQojLGcoRjduC5I3BF7xoZ
User management URL: https://cdyw91nanb.execute-api.eu-west-1.amazonaws.com/dev/pv/v1/users/provision
![[Pasted image 20250129190451.png]]
In dev branch commented AppConfigBallotServiceConfig due to class path clash with AppConfigServiceConfig...

In UAT the m2m client is: **ProxymityAuth0ManagementApiApp**
Domain: proxymity-auth0-uat.eu.auth0.com
Client id: 31RzzPPES69nuCri655tvlDWcUy6IxZz
Client secret: gLo4SJ0_5uARezopBjBIMRZhIov5BgKBKYzFVvJQQLfXBgCrkvl4vFsk-73YoCrh
User management URL: https://n3w4taxxdb.execute-api.eu-west-1.amazonaws.com/dev/pv/v1/users/provision

App should be set to both:
![[Pasted image 20250130172720.png]]


The m2m app should be authorized for user management:
![[Pasted image 20250130173134.png]]

And the user management API should have this role:

![[Pasted image 20250130173402.png]]
The token used to access user-management should look like this:
```
{
  "iss": "https://proxymity-auth0-uat.eu.auth0.com/",
  "sub": "31RzzPPES69nuCri655tvlDWcUy6IxZz@clients",
  "aud": "https://user-management",
  "iat": 1738583078,
  "exp": 1739187878,
  "scope": "create:users.autoprovision",
  "gty": "client-credentials",
  "azp": "31RzzPPES69nuCri655tvlDWcUy6IxZz",
  "permissions": [
    "create:users.autoprovision"
  ]
}
```
if not - user management will return 500 error to the Action that called the user management provision API.

**The organization should have the NeoLink connection, otherwise it will fail!**
