DEV
m.ophir - user record
![[Pasted image 20240929105236.png]]

app-dev before login
![[Pasted image 20240929105652.png]]

app-dev after login:
![[Pasted image 20240929105852.png]]

![[Pasted image 20240929110023.png]]

From the ID Token in the cookie:
```json
{
  "at_hash": "ACjEA9QfBK5bWiLG9r87Pw",
  "sub": "6d8cab2c-a584-4f9f-af02-d1fa9f3b255b",
  "email_verified": true,
  "last_login": "2024-08-27T13:14:17.629727+00:00",
  "iss": "https://cognito-idp.eu-west-1.amazonaws.com/eu-west-1_Zn2NxGoTD",
  "cognito:username": "m.ophir",
  "origin_jti": "7698be23-9957-463d-ae76-133a33059b93",
  "aud": "2hhf2u6ukslinukjqljqipllf0",
  "event_id": "254ead41-e902-4bbb-813e-7d6cff53d233",
  "token_use": "id",
  "auth_time": 1727596637,
  "exp": 1727598437,
  "iat": 1727596638,
  "jti": "d305bf7f-68e6-44a0-a611-943c5fe0c22f",
  "email": "m.ophir@proxymity.io"
}
```

PROD

m.ophir
|   |   |
|---|---|
|custom:last_login|2024-08-27T09:18:14.659063+00:00|

mo39119
|   |   |
|---|---|
|custom:last_login|2024-08-27T09:25:43.188050+00:00|

`aws cognito-idp admin-get-user --user-pool-id eu-west-1_2v9WIRySA --username mo39119 --profile aws-prod`

```json
{
    "Username": "mo39119",
    "UserAttributes": [
        {
            "Name": "sub",
            "Value": "b07a9573-3808-4e3f-a36c-9cdff9b6d93c"
        },
        {
            "Name": "email_verified",
            "Value": "false"
        },
        {
            "Name": "custom:last_login",
            "Value": "2024-09-29T11:02:27.951368+00:00"
        },
        {
            "Name": "email",
            "Value": "m.ophir@proxymity.io"
        }
    ],
    "UserCreateDate": "2021-08-12T15:50:04.774000+03:00",
    "UserLastModifiedDate": "2024-09-29T14:02:28.017000+03:00",
    "Enabled": true,
    "UserStatus": "CONFIRMED",
    "PreferredMfaSetting": "SOFTWARE_TOKEN_MFA",
    "UserMFASettingList": [
        "SOFTWARE_TOKEN_MFA"
    ]
}
```

`aws cognito-idp admin-update-user-attributes --user-pool-id eu-west-1_2v9WIRySA --username mo39119 --user-attributes Name=custom:last_login,Value=2024-08-08T11:02:27.951368+00:00 --profile aws-prod`
`aws cognito-idp admin-update-user-attributes --user-pool-id eu-west-1_SjTMVDZZv --username m.ophir --user-attributes Name=custom:last_login,Value=2024-08-08T11:02:27.951368+00:00 --profile aws-uat` `

PROD pre-token lambda - 5 months
2024-05-05T09:01:57.000Z

DEV
2024-04-17T10:41:55.000Z


```json
{
	'version': '1', 
	'triggerSource': 'TokenGeneration_Authentication', 
	'region': 'eu-west-1', 
	'userPoolId': 'eu-west-1_SjTMVDZZv', 
	'userName': 'm.ophir', 
	'callerContext': {
		'awsSdkVersion': 'aws-sdk-unknown-unknown', 
		'clientId': '6sqqsupa1cfa58qoc1hbrpr9rj'
	}, 
	'request': {
		'userAttributes': {
			'cognito:token_nbf': '1717493368304',
			'sub': '84a6e48c-bf5c-43e5-95cd-646abbc69fe4',
			'cognito:email_alias': 'm.ophir@proxymity.io',
			'cognito:user_status': 'CONFIRMED',
			'email_verified': 'true',
			'custom:last_login': '2024-08-08T11:02:27.951368+00:00', 
			'email': 'm.ophir@proxymity.io'
		}, 
		'groupConfiguration': {
			'groupsToOverride': [], 
			'iamRolesToOverride': [], 
			'preferredRole': None
		}
	}, 
	'response': {
		'claimsOverrideDetails': None
	}
}
```

```json
{
	'version': '1', 
	'triggerSource': 'TokenGeneration_HostedAuth', 
	'region': 'eu-west-1', 
	'userPoolId': 'eu-west-1_SjTMVDZZv', 
	'userName': 'm.ophir', 
	'callerContext': {
		'awsSdkVersion': 'aws-sdk-unknown-unknown', 
		'clientId': '6sqqsupa1cfa58qoc1hbrpr9rj'
	}, 
	'request': {
		'userAttributes': {
			'cognito:token_nbf': '1717493368304', 
			'sub': '84a6e48c-bf5c-43e5-95cd-646abbc69fe4', 
			'cognito:email_alias': 'm.ophir@proxymity.io', 
			'cognito:user_status': 'CONFIRMED', 
			'email_verified': 'true', 
			'custom:last_login': '2024-09-29T17:17:49.631122+00:00', 
			'email': 'm.ophir@proxymity.io'
		}, 
		'groupConfiguration': {
			'groupsToOverride': [], 
			'iamRolesToOverride': [], 
			'preferredRole': None
		}
	}, 
	'response': {
		'claimsOverrideDetails': None
	}
}
```

When the flow works:
```json
{  
  'version': '1',  
  'triggerSource': 'TokenGeneration_HostedAuth',  
  'region': 'eu-west-1',  
  'userPoolId': 'eu-west-1_SjTMVDZZv',  
  'userName': 'm.ophir',  
  'callerContext': {  
    'awsSdkVersion': 'aws-sdk-unknown-unknown',  
    'clientId': '6sqqsupa1cfa58qoc1hbrpr9rj'  
  },  
  'request': {  
    'userAttributes': {  
      'cognito:token_nbf': '1717493368304',  
      'sub': '84a6e48c-bf5c-43e5-95cd-646abbc69fe4',  
      'cognito:email_alias': 'm.ophir@proxymity.io',  
      'cognito:user_status': 'CONFIRMED',  
      'email_verified': 'true',  
      'custom:last_login': '2024-08-08T11:02:27.951368+00:00',  
      'email': 'm.ophir@proxymity.io'  
    },  
    'groupConfiguration': {  
      'groupsToOverride': [],  
      'iamRolesToOverride': [],  
      'preferredRole': None  
    }  
  },  
  'response': {  
    'claimsOverrideDetails': None  
  }  
}
```