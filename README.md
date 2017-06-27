# Ecotricity-API
These are my findings when exploring the Ecotricity API for their new 'charging for charging' system.

##### Updates for version 2.

The version 2 API no longer repeatedly sends the username and password as part of the normal conversation, and instead uses access tokens.  However you can still use passwords as there is backward compatibility with version 1.  

There is first a call to the 'token' function which generates the access token required, then a call to 'user' to validate the password.

#### checkDuplicateEmail
This one doesn't look like it's a particularly good idea, it tells you whether or not a particular email address has an account.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/checkDuplicateEmail HTTP/1.1
    email=scotthelme%40hotmail.com

Response:

    {"result":true}

The API returns true which seems to indicate the email address is free to be registered. Once I've registered my account I get the following.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/checkDuplicateEmail HTTP/1.1
    email=scotthelme%40hotmail.com

Response:

    {"result":false}

#### validateUserName
This is pretty much the same as the email address endpoint above. Provide it a username and it will tell you if an account exists with that username.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/validateUserName HTTP/1.1
    name=ScottHelme

Response:

    {"result":true}

<br/>

Then after registering my username I get the following.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/validateUserName HTTP/1.1
    name=ScottHelme

Response:

    {"result":false}

#### getAddresses
Just noting this down but not much to see here, you can look up addresses with postcodes if that's useful.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/getAddresses HTTP/1.1
    postcode=m14wb

Response:

    {"result":[{"summaryline":"Premier Inn, 112 Portland Street, Manchester, Greater Manchester, M1 4WB","organisation":"Premier Inn","number":"112","premise":"112","street":"Portland Street","posttown":"Manchester","county":"Greater Manchester","postcode":"M1 4WB","line1":"Portland Street","line2":"Premier Inn","line3":"","town":"Manchester"}]}

#### verifyEmail
I can't figure out what this endpoint does. Any combination of valid or invalid values just returns the same thing.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/verifyEmail HTTP/1.1
    email=scotthelme%40hotmail.com
    &username=ScottHelme

Response:

    {"result":true}

#### createUserEV
This is called at the end of the account creation process to actually create your user account with the service.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/createUserEV HTTP/1.1
    vehicleSpecification=%282011-%29
    &vehicleRegistration=*snip*
    &password=*snip*
    &deviceId=*snip*
    &lastDigits=test
    &address2=*snip*
    &cardType=test
    &token=test
    &city=*snip*
    &address1=
    &email=scotthelme%40hotmail.com
    &county=*snip*
    &village=
    &vehicleMake=Nissan
    &firstName=scott
    &street=*snip*
    &country=GB
    &houseName=
    &houseNumber=
    &name=ScottHelme
    &lastName=helme
    &hasEnergyAccount=0
    &postcode=*snip*
    &vehicleModel=Leaf

Response:

    {"result":true,"data":{"id":"*snip*","name":"ScottHelme","email":"scotthelme@hotmail.com","title":null,"firstname":"scott","lastname":"helme","phone":"","vehicleAdded":{"result":true},"tokenAdded":{"result":true}}}

What's *really* interesting in here is the third from last parameter of the POST request to create the account, hasEnergyAccount=0. I wonder if that was change to 1 or true to indicate that I do have an energy account if I'd get the free charging?..

#### login
Called upon login.  No longer used with v2 as this API has switched to oauth, but still works.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/login HTTP/1.1
    electricHighway=true
    &identifier=ScottHelme
    &password=*snip*

Response:

    {"result":true,"data":{"id":"*snip*","token":"","name":"ScottHelme","email":"scotthelme@hotmail.com","firstname":"scott","lastname":"helme","verified":"1","businessPartnerId":"*snip*","phone":"","electricHighwayAccount":true,"accountDetails":{"businessPartnerId":"*snip*","type":"1","firstName":"scott","lastName":"helme","emailAddresses":[{"address":"scotthelme@hotmail.com"},{"address":"scotthelme@hotmail.com","primary":"X"}],"street":"*snip*","village":"*snip*","city":"*snip*","postcode":"*snip*","telephoneNumbers":null},"googleAPIkey":"*snip*"}}

#### registerNotifications
Called after login.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/registerNotifications HTTP/1.1
    access_token=*snip*
    &deviceType=android
    &app=com.ecotricity.electrichighway
    &identifier=ScottHelme
    &appId=com.ecotricity.electrichighway
    &deviceId=*snip*

Response:

    {"deviceId":"*snip*","deviceType":"android","app":"com.ecotricity.electrichighway","result":true}

#### getCardList
This simply lists out the payment cards associated on your account.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/getCardList HTTP/1.1
    identifier=ScottHelme
    &access_token=*snip*  

Response:

    {"result":[{"lastDigits":"*snip*","cardType":"Visa (VISA)","cardId":"000001","cardIcon":"visa"}]}

#### getTransactionList
Viewing historic transactions on the account.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/getTransactionList HTTP/1.1
    identifier=ScottHelme
    &access_token=*snip*

Response:

    {"result":{"transaction":{"contractAccountId":*snip*","contractAccount":[{"documentNo":"*snip*","sessionId":"00008560","totalCost":"6.00 ","currency":"GBP","date":"20160726","pumpId":"1020","pumpConnector":"1","baseCost":"6.00 ","discountEcoGrp":"0.00 ","discountMultiChg":"0.00 ","surcharge":"0.00 ","freeCost":"0.00 "},{"documentNo":"*snip*","sessionId":"00008312","totalCost":"6.00 ","currency":"GBP","date":"20160726","pumpId":"1272","pumpConnector":"1","baseCost":"6.00 ","discountEcoGrp":"0.00 ","discountMultiChg":"0.00 ","surcharge":"0.00 ","freeCost":"0.00 "}]}}}

This is actually a list of all transactions attempted whether or not were charged - this makes it less than useful.

#### getUserVehicleList
Same again, this just lists out the vehicles registered to your account. It seems to be used for selecting chargers with the appropriate connector for your car.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/getUserVehicleList HTTP/1.1
    identifier=ScottHelme
    &access_token=*snip*

Response:

    {"result":[{"id":"0000001069","registration":"*snip*","specification":"(2011-)","model":"Leaf","make":"Nissan"}]}

#### getLocationDetails
Does what it says on the tin.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/getLocationDetails HTTP/1.1
    vehicleSpecification=%282011-%29
    &vehicleModel=Leaf
    &locationId=147
    &vehicleMake=Nissan

Response:

    {"result":{"pump":[{"status":"Swipe card only","latitude":"53.72297","longitude":"-2.486165","name":"BP Ewood","postcode":"BB2 4LA","location":"Bolton Road Blackburn","locationId":"147","pumpId":"1257","lastHeartbeat":"","pumpModel":"AC (RAPID) \/ DC (CHAdeMO)","connector":[{"compatible":"X","type":"DC (CHAdeMO)","status":"Swipe card only","name":"DC (CHADEMO)","connectorId":"1","sessionDuration":"20"},{"compatible":"","type":"AC (RAPID)","status":"Swipe card only","name":"AC (RAPID)","connectorId":"2","sessionDuration":"20"}]}]}}

#### getPumpList
Lists pumps close to your location using lat/lon.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/getPumpList HTTP/1.1
    latitude=55.378051
    &vehicleSpec=%282011-%29
    &vehicleMake=Nissan
    &vehicleModel=Leaf
    &longitude=-3.435973

Response:

    {"result":[{"latitude":"55.219691","longitude":"-3.410582","name":"Annandale Water Services","postcode":"DG11 1HD","location":"A74(M) Jct 16","locationId":"111","pumpId":["1201"],"pumpModel":"AC (RAPID) \/ DC (CHAdeMO)","available":false,"swipeOnly":true,"distance":17.682033646467}, ... }

That response is a much larger list that I've cut down. It also seems to tell you your distance from the charger.

#### getPumpConnectors
This lists the available connectors on a charger and seems to require auth so that it can tell which which (if any) connector is for your car.

This API no longer returns the price of the connection (the field is still there but is always null).  That is now provided by the quote function.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/getPumpConnectors HTTP/1.1
    access_token=*snip*
    &pumpId=1106
    &identifier=ScottHelme
    &appId=com.ecotricity.electrichighway
    &vehicleModel=Leaf
    &vehicleId=*snip*
    &deviceId=*snip*
    &vehicleMake=Nissan

Response:

    {"result":{"status":"Available","latitude":"53.567784","longitude":"-2.230268","name":"Birch Services","postcode":"OL10 2QH","location":"M62 Westbound Jct 18\/19","pumpId":"1106","connector":[{"compatible":"X","type":"DC (CHAdeMO)","status":"Available","name":"DC (CHADEMO)","connectorId":"1","sessionDuration":"30"},{"compatible":"","type":"AC (RAPID)","status":"Available","name":"AC (RAPID)","connectorId":"2","sessionDuration":"30"},{"compatible":"","type":"CCS","status":"Available","name":"CCS","connectorId":"3","sessionDuration":"30"}],"connectorCost":null}}

If the charger can be used by your car you can see the "compatible":"X" in the response for the appropriate charger.

Note sessionDuration still reads 30 minutes even though 45 minutes is now standard.  This appears to be a bug.

#### quote

Returns a personalised charge quote to the user.  Doesn't appear to be a per-pump value.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/quote HTTP/1.1
    access_token=*snip*
    &identifier=TonyHoyle
    &vehicleId=*snip*
    &deviceId=*snip*
    &appId=com.ecotricity.electrichighway
    
Response:

    {"result":{"sessionPricing":[{"title":"Free for Ecotricity customers","pricingData":[{"title":"Free charges used","value":"7 of 52"},{"title":"To be used by:","value":"09\/03\/2018"}]}],"fixed":"0.00 ","variable":{"unitOfMeasurement":"kWh","value":"0.00"},"sessionId":"00429497","sessionDuration":"45"}}

#### getSettings

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/getSettings HTTP/1.1
    deviceId=*snip*
    appId=com.ecotricity.electrichighway

Response:

    {"result":true,"autocomplete":"1","amplitude":"1","google_maps_key":"..","amplitude_key":"..","defaultChargeCopy":"A charging session is \u00a36 for 30 minutes and free for Ecotricity energy customers.","defaultGuestChargeCopy":"Charging costs just \u00a30.17 per kWh, plus a \u00a33 connection fee. We won't take payment until you've successfully finished your charge session.","quoteHeading":"Your charge session","quoteCopy":"Here's how your charge is calculated. Maximum charge session is 45 minutes.","maintenance":"It\u2019s currently free to charge your vehicle on all of our Electric Highway pumps while we carry out some maintenance work. Just follow the pump's on-screen instructions to charge up.","maintenanceHeading":"We\u2019re tweaking the network\u2026","maintenanceEnabled":"0","marketingCopy":"Would you like us to keep you up to date on what the Ecotricity Group is doing? We won't pass your details on to anyone else","terms":{"title":"Terms EH","terms":"...","app_version":"1"}}
    
This is consiterably expanded from the original text but it's not clear if the app uses most of it.
    
#### forgottenUsername
Triggered when you click forgotten username in the app.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/forgottenUsername HTTP/1.1
    email=scotthelme%40hotmail.com

Response:

    {"result":true}
    {"result":false, "message":"*reason*"}

#### forgottenPassword
Triggered when you request a password reset.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/forgottenPassword HTTP/1.1
    platform=android
    &email=ScottHelme

Response:

    {"result":{"hashkey":"*snip*"}}

You need to hit the following URL to activate the token for the next step: https://www.ecotricity.co.uk/ecovalidate/token/(hashkey)

#### getPasswordToken
Triggered when you hit forgotten password in the app, the hashkey value is returned in the previous API call.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/getPasswordToken HTTP/1.1
    platform=android
    &hashkey=*snip*

Response:

    {"result":{"success":false,"error":"TOKEN_NOT_VALIDATED"}}

Once you have clicked the activation link in the email.

Response:

    {"result":{"success":true,"hashkey":"*snip*"}}

Note: The hashkey provided here is a new value.

#### usePasswordToken
Final step in the password reset process, provide the new password.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/usePasswordToken HTTP/1.1
    platform=android
    &hashkey=*snip-hashkey-forgottenPassword*
    &password=*snip*
    &forgotpassword_hash_key=*snip-hashkey-getPasswordToken*
    &confirm_password=*snip*

#### changeEmail
Used to change email address on account.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/changeEmail HTTP/1.1
    newEmail=*snip*
    &identifier=ScottHelme
    &access_token=*snip*

Response:

    {"result":true}
    {"result":false, "message":"*reason*"}

The email validation link is https://www.ecotricity.co.uk/ecovalidate/change-email/(token)

#### registerVehicle
Add a new car to your account.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/registerVehicle HTTP/1.1
    vehicleModel=Leaf
    &access_token=*snip*
    &vehicleMake=Nissan
    &identifier=ScottHelme
    &vehicleRegistration=MY64ABC
    &vehicleSpecification=%282011-%29

Response:

    {"result":true}
    {"result":false, "message":"*reason*"}

#### unregisterVehicle
Remove a vehicle from your account.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/unregisterVehicle HTTP/1.1
    vehicleSpecification=%282011-%29
    &access_token=*snip*
    &vehicleModel=Leaf
    &identifier=ScottHelme
    &vehicleRegistration=MY64ABC
    &vehicleMake=Nissan
    &vehicleId=*snip*

Response:

    {"result":true}
    {"result":false, "message":"*reason*"}

#### changePassword
Used to change password in your account. Just provide old password and new password.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/changePassword HTTP/1.1
    newPassword=*snip*
    &identifier=ScottHelme
    &access_token=*snip*

Response:

    {"result":true}
    {"result":false, "message":"*reason*"}

#### startChargeSession
Used to actually start a charging session.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/startChargeSession HTTP/1.1
    access_token=*snip*
    &pumpId=1106
    &identifier=TonyHoyle
    &pumpConnector=1
    &cardId=0
    &appId=com.ecotricity.electrichighway
    &sessionId=0000001
    &deviceId=*snip*
    &cv2=

Response:

    {"result":true}
    {"result":false, "message":"*reason*"}
    {"result":false, "soaperror":true}

#### stopChargeSession
Used to stop a charging session early.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/stopChargeSession HTTP/1.1
    access_token=*snip*
    &deviceId=*snip*
    &sessionId=00011520
    &identifier=ScottHelme
    &pumpConnector=1
    &pumpId=1263

Response:

    {"result":true} 
    {"result":false, "message":"*reason*"}

#### getChargeStatus
Used to get the status of a charge session.  This has two forms.  The second form is used at app startup to find out if any charges are running.

Request type 1:

    POST https://www.ecotricity.co.uk/api/ezx/v1/getChargeStatus HTTP/1.1
    deviceId=*snip*
    &vehicleMake=Nissan
    &sessionId=00000228
    &vehicleModel=Leaf
    &pumpConnector=1
    &pumpId=1263

Request type 2:

    POST https://www.ecotricity.co.uk/api/ezx/v1/getChargeStatus HTTP/1.1
    access_token=*snip*
    &identifier=TonyHoyle
    &deviceId=*snip*
    &appId=com.ecotricity.electrichighway

Response:

    {"result":{"status":"Completed","message":"Your charge session has finished. Please return and check your charger.","completed":true,"cost":"","sessionId":"00000228","started":"20170422123928","finished":"20170422131142","pumpId":"1102","pumpConnector":"1","createdDate":"20170422","createdTime":"123828"}}

This is a completed charge 

Response:

    {"result":{"status":"Retry","message":"Sorry there's been problem please disconnect your vehicle and try again.","completed":false,"cost":"","sessionId":"00000228","pumpId":"1263","pumpConnector":"1"}}
    
This is a failed charge

Response:

    {"result":{"status":"In Progress","message":"Your charge session is still progressing.","completed":false,"cost":"","sessionId":"00011520","energyConsumption":"000000","started":"20160729162822","pumpId":"1032","pumpConnector":"1"}}    
    
This is a successful start, containing the start time and energy used.  The energy used is updated infrequently and appears to be measured in watts.  The app continues to poll the charge status every 5 seconds to update its display.

<br />

Response:

    {"result":{"status":"Completed","message":"Your charge session has finished. Please return and check your charger.","completed":true,"cost":"6.00 ","sessionId":"00011520","energyConsumption":"004700","started":"20160729162822","finished":"20160729164140","pumpId":"1032","pumpConnector":"1","createdDate":"20160729","createdTime":"162800"}}

This is a completed charge.  The cost is now filled as is the finish time.  The created date and time may be invoice related - after this message is received an invoice will shortly arrive via email.

#### token
This is part of the oauth login sequence to the electric highway.  There are two request types, one for initial login and one for a returning user.

Note the response isn't pure json.  This may be a bug at the server end.

The client id and client secret are fixed:
client_secret = 1363c5f65ec09a2458788e75717a51403b5edd09ae5a40063a53960801305c97
client_id = 2a66896a0ee4aff229fca61772308e2db24a690316a44bf892d510671ef7f834

Request 1 (login):

    POST https://www.ecotricity.co.uk/api/ezx/v1/token HTTP/1.1
    password=<snip>
    &grant_type=password
    &appId=com.ecotricity.electrichighway
    &client_secret=<client secret>
    &deviceId=<device id>
    &client_id=<client id>
    &username=<snip>
    
Request 2 (return):

    POST https://www.ecotricity.co.uk/api/ezx/v1/token HTTP/1.1
    refresh_token=<token>
    &client_secret=<client secret>
    &grant_type=refresh_token
    &deviceId=<device id>
    &client_id=<client id>
    &appId=com.ecotricity.electrichighway
    
Response:

    ac
    {"access_token":"..","expires_in":86400,"token_type":"Bearer","scope":null,"refresh_token":".."}
    0

#### user
This is called after token to retrieve user details as the app starts up.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/user HTTP/1.1
    access_token=<token from token call>
    &identifier=<username>
    &electricHighway=true
    &deviceId=<device id>
    &appId=com.ecotricity.electrichighway
    
Response:

{"result":true,"data":{"id":"99999","token":"","name":"*name*","email":"*email*","firstname":"A","lastname":"Person","verified":"1","businessPartnerId":"999999","phone":"","electricHighwayAccount":true,"accountDetails":{"businessPartnerId":"999999","type":"1","title":"0001","firstName":"A","lastName":"Person","emailAddresses":[{"address":"*email*"},{"address":"*email*","primary":"X"}],"telephoneNumbers":null,"houseNumber":"1","street":"AStreet","village":"AVillage","city":"ATown","postcode":"AA1 1AA"},"googleAPIkey":"*key*","updatedTerms":false,"marketing":false}}
    
#### archiveSession
It is not clear what this does.

Request:

    POST https://www.ecotricity.co.uk/api/ezx/v1/archiveSession HTTP/1.1
    deviceId=*snip*
    &sessionId=00000111
    &appId=com.ecotricity.electrichighway
    
Response:

    {"result":true}

#### Other bits
There is a list of what seems to be all supported cars here:
https://www.ecotricity.co.uk/api/ezx/v1/getVehicleList

You can read the T&C's if you *really* want to here:
https://www.ecotricity.co.uk/api/ezx/v1/terms?eh=true

The app seems to load the T&C doc every time you open it, bit of a waste.
