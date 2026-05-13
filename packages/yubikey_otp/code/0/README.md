# yubikey_otp

For general information see:
* https://developers.yubico.com/OTP/OTP_Walk-Through.html
* https://developers.yubico.com/yubikey-val/Validation_Protocol_V2.0.html
* https://docs.yubico.com/yesdk/users-manual/application-otp/yubico-otp.html
* https://upgrade.yubico.com/getapikey/

This is a simple api call and validator for the yubikey's OTP.

## 1. Set up

Go to `https://upgrade.yubico.com/getapikey/` and get an API key. Save the
clientID.

## 2. Register user on <platform>

```nim
  let
    clientID = <clientID from step 1>
    otp = <users otp from yubikey>

  let
    data = yubikeyRegister(clientID, otp)

  if data.success:
    # Save the `data.publicID` to the database and associate it with the user
  else:
    # Handle the error
```

## 3. Validate on login

```nim
  let
    clientID = <clientID from step 1>
    publicID = <users publicID from registration>
    otp = <users otp from yubikey>

  if yubikeyValidate(clientID, publicID, otp):
    # Success
  else:
    # Failure
```