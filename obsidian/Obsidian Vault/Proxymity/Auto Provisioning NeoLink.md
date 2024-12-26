
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

Auth0 management client Id:
auth0.m2m.organisation.management.clientId
JOou6JyIyeLRmGyH0IXrKFZYNmiN7k7V

Secret:
auth0.m2m.organisation.management.secret
jRi5cHszTbCaPr7kxF8rou3gyxZgnPVj_puKfXAZFZUIaMgNLJIgfwxEUBQR2BWk

