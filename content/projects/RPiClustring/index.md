---
title: "Raspberry Pi Clustring Computation Platform" 
date: 2022-01-15
tags: ["Raspberry Pi","PXE Boot","FIDO2 Authentication","Webauthn"]
author: ["Eusden Lin"]
description: "This project is a simple application for localhost Fido2 Authentication using python webauthn api." 
summary: "This project is a simple application for localhost Fido2 Authentication using python webauthn api." 
cover:
    image: "cover.png"
    alt: "The Webauthn Structure"
    relative: true
editPost:
    URL: "https://github.com/EusdenLin/webauthn"
    Text: "Github Repository"

---

---


## Target
* Complete a simple fido2 local authentication flow 

## What I've done
* flash the usb stick as a FIDO2-support authentication device
* make sure the device is available on the test website and google
* found a capable sample to implement the authentication flow
* understand the files (app.py, base64.js, webauthn.js) in flask_demo
* Understand the structure of the whole flask project
* Complete a simple fido2 local authentication flow 

---


### [Application Github Page](https://github.com/EusdenLin/webauthn)

## structure of fido2 
![](https://i.imgur.com/nsBJaOn.png)

### demo project in webauthn.io with java and google web app engine
![](https://i.imgur.com/DQbiEJ9.png)
This test server is from https://github.com/google/webauthndemo

use different usb stick to test the authentication 
use google web app engine and java as dev tools.

### demo project in webauthn.io with python and flask

resources:
* [flask tutorial](https://flask.palletsprojects.com/en/2.0.x/)
* [github repo page](https://github.com/duo-labs/py_webauthn)
* [another flask tutorial](https://medium.com/seaniap/python-web-flask-jinja2%E6%A8%A3%E7%89%88%E5%BC%95%E6%93%8E%E7%9A%84%E4%BD%BF%E7%94%A8-f71305e2da34)
* [how to use SQLAlchemy in flask](https://medium.com/seaniap/python-web-flask-使用sqlalchemy資料庫-8fc49c584ddb)
* [webauthn intro (js code explain)](https://blog.techbridge.cc/2019/08/17/webauthn-intro/)
* [webauthn guide](https://webauthn.guide/)


## setup a fido2 client server

### Usage
```
flask run
```
Open ```127.0.0.1:5000``` on your browser. (localhost:5000 is not allowed due to invalid domain)

Then you can use the simple authentication tool.

#### [demo video](https://www.youtube.com/watch?v=nWuQgNkLKQ0)

### application structure

* webauthn.js: front-end for communication to usb stick
> This js file was modified from [py_webauthn/flask_demo](https://github.com/duo-labs/py_webauthn/tree/4a0f8cd1db3b7635a1951a933d5a690beedf7c50) which was deleted in master branch.
* base64.js: for decode/encode
* app.py: back-end for authentication/registraion functions (flask/webauthn)
* index.html: user interface for input username

### authenticaton flow - Registration

#### didClickRegister(for registration):
* With event listener, the front-end would send form data(username) to server, the server would response with a json file with some credential create options.
* credential create options: rely party, challenge, username, authenticatorSelection, ...

#### navigator.credentials.create
* Create a client credential with the credential create options.

Then the credential will be posted to the server, and verified the credential using challenge to check whether the credential is valid or not.

If valid, the server will store a dict ```{username, (publickey, credentialID)}``` for authentication.

### authenticaton flow - Authentication

#### didClickRegister(for registration):
* With event listener, the front-end would send form data(username) to server, the server would response with a json file with some authentication options.
* authentication options: challenge, allow_credentials, ...

#### navigator.credentials.get
* gain the credential while register
* The credential assertion will be posted to server for authentication. If the assertion is valid, then the user is allowed to login.






 
### the overview of app.py py_webauthn
[THE USAGE OF PYTHON WEBAUTHN PACKAGE](https://github.com/duo-labs/py_webauthn)
* webauthn.WebAuthnMakeCredentialOptions: 
1. __```@app.route('/')```__
    * return the index.html which contains the input and button
2. __```@app.route('/webauthn_begin_activate', methods=['POST'])```__
    * ```request.form.get``` to get register username and register display name.
    * ```util``` generates keys
    * ```WebAuthnMakeCredentialOptions``` pack all the information into an object, then return this as a json file by ```jsonify```to webauthn.js

3. __```@app.route('/webauthn_begin_assertion', methods=['POST'])```__

4. __```@app.route('/verify_credential_info', methods=['POST'])```__

5. __```@app.route('/verify_assertion', methods=['POST'])```__

6. __```@app.route('/logout')```__

### the overview of webauthn.js in static folder
There are two part in the java script file: Registration function and Authentication function

#### Registration Functions:
* __didClickRegister__
    1. gather data from form in html
    2. post data to the server, generate PublickeyCredentialOption(```getCredentialCreateOptionsFromServer```)
    3. transform it(```transformCredentialCreateOptions```)
    4. create new credential keypair(```navigator.credentials.create```, usb stick is read at the same time)
    5. Encode the keypair(```transformNewAssertionForServer```)
    6. Post to the server(```postNewAssertionForServer```)
* __getCredentialRequestOptionFromServer__
    1. gather credential from /webauthn_begin_assertion, method='post'
* __transformCredentialRequestOption__
    1. tramsform credentialRequestOption 
* __getCredentialCreateOptionsFromServer__
    1. gather credential from /webauthn_begin_activate, method='post'
* __transformCredentialCreateOption__
    1. tramsform credentialCreateOption

#### Authentication Functions:
* __didClickLogin__
    1. gather login data
    2. post the data to server (```getCredentialRequestOptionFromServer```)
    3. convert to binary (```transformCredentialRequestOptions```)
    4. request the authenticator to create an assertion using the credential private key (```navigator.credentials.get``` , usb stick is read at the same time)
    5. encode the byte arrays that contained in the assertion data as string for posting to server (```transformAssertionForServer```)
    6. post the assertion to the server for verification (```postAssertionToServer```)
* __transformNewAssertionForServer__
    1. transform the binary data in the credential into base64 strings
* __postNewAssertionToServer__
    1. Posts the new credential data to the server for validation and storage with /verify_credential_info
* __transformAssertionForServer__
    1. Encodes the binary data in the assertion into strings for posting to the server
* __postAssertionToServer__
    1. Post the assertion to the server for validation and logging the user in using /verify_assertion

### how the workflow For registration:

1. Get username and name
2. Send them to the server
3. Server responds with challenge
4. MakeCredential
5. Send response to the server
6. Check that server likes it
7. PROFIT!

### how the workflow For authentication:

1. Get username(remove password field)
2. Send to the server
3. Server responds with challenge
4. GetAssertion
5. Send response to the server
6. Check that server likes it
7. PROFIT!

(challenge, RP_NAME, RP_ID, ukey, username, display_name) information generate by back-end
(webauthn_credential.public_key, webauthn_credential.credential_id, webauthn_credential.sign_count) information obtain by webauthn.js


### the structure of a public key before navigator.getCredential
```javascript=
PublicKeyCredential {
    challenge: // 32 random bytes generated by the server
    
    rp: // relying party
    
    user: { // User
        id:  
        name:, 
        displayName:,         
    }, 
    
    pubKeyCredParams: [
        {
            type: "public-key",
            alg: ,//encode algorithm
        }
    ],
    
    authenticatorSelection: {
        userVerification: "preferred"
    },
    
    timeout: 360000  //6 minutes   
}
```

### the structure of a credential that return after insert the usb stick
for example:
```json=
PublicKeyCredential {
    id: 'ADSUllKQmbqdGtpu4sjseh4cg2TxSvrbcHDTBsv4NSSX9...',
    rawId: ArrayBuffer(59),
    response: AuthenticatorAttestationResponse {
        clientDataJSON: ArrayBuffer(121),
        attestationObject: ArrayBuffer(306),
    },
    type: 'public-key'
}
```
1. clientDataJSON:

![](https://i.imgur.com/cG34tOk.png)
* challenge: challenge 是由 server 產生的一個 buffer，裡面含有一串隨機加密過的 bytes，用來防止 "replay attacks"
* type: authenticator的type, 例如TouchID 這種存在於設備內部的，屬於 "platform" type，而 Yubikey 這類外部硬體設施則屬於 "cross-platform" type。
* origin: 作用網域
* 拿出 challenge、origin 與 type 來驗證，challenge 應該要與當初 Server 產生的一致、origin 要正確，且 type 要確定為 create，才能代表是在註冊使用者

2. attestationObject
* authData：authData 這個 byte array 包含著所有 registration event 的 metadata，以及 public key。

* fmt：這個是包含著 attestation 的 format，如果你在 create credentials 時有要求 Authenticators 提供 attestation data，那 server 可以從這個欄位知道該如何 parse 與 validate attestation data。

* attStmt：這就是要求來的 attestation data，根據 fmt 的不同會有不同的結構，以這邊範例為例，我們拿到的是一個 signature 與 x5c certificate，servers 可以用這資料來驗證 publickey 是不是來自預期的 authenticator，或是根據 authenticator 的資訊而 reject authenticate (像是覺得不能信任該 certifacate，等等)


#### what is ORM

ORM maps applications and databases, which makes developers don't have to write SQL language.

### flask with database: SQLAlchemy

```python=
app.config['SQLALCHEMY_DATABASE_URI']='sqlite:///users.sqlite3'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
db = SQLAlchemy(app)
```
_setting the SQL file path, create an object (db)_

```sql=
class Users(db.Model):    
    _id = db.Column('id', db.Integer, primary_key=True)        
    name = db.Column('name', db.String(100))    
    email = db.Column(db.String(100))    
    def __init__(self, name, email):        
        self.name =name        
        self.email = email
```
_\_\_init\_\_: the constructor of the database_

## Demo

### [Demo in webauthn.io](https://www.youtube.com/watch?v=CCfQYdA8m-U)

### [Demo with testing google account](https://www.youtube.com/watch?v=q0HzDzx-3_k)



## Resources
* [getting started with opensk](https://wiki.makerdiary.com/nrf52840-mdk-usb-dongle/opensk/getting-started/)
* [nRF52840 MDK USB Dongle](https://github.com/makerdiary/nrf52840-mdk-usb-dongle/)
* [opensk by makerdiary](https://github.com/makerdiary/opensk)
* [opensk by google](https://github.com/google/opensk)
* [how to resolve rustup doesn't allow 'if' in constant](https://github.com/rust-lang/rust/issues/49146)
* [FIDO2 test website](https://webauthn.io/)
* [installation guide of google opensk](https://github.com/google/OpenSK/blob/stable/docs/install.md)
* [make a new  uf2 bootloader](https://github.com/adafruit/Adafruit_nRF52_Bootloader#making-your-own-uf2)
* [virtual-authenticators-tab](https://github.com/google/virtual-authenticators-tab)
* [How to Setup FIDO2 for ASP.NET Passwordless Authentication](https://www.youtube.com/watch?v=8bxpzx6072A)
* [setup FIDO2 server that supply FIDO2 authentication](https://github.com/StrongKey/fido2)
* [Standalone Installation - strongkey fido2 server(SKFS)](https://docs.strongkey.com/index.php/skfs-home/skfs-installation/skfs-installation-standalone)
* [getting started for developer - fido2 alliance](https://fidoalliance.org/developers/)
* [another way to setup a fido2 server](https://github.com/line/line-fido2-server)
* [setup fido2 client](https://github.com/VinCSS-Public-Projects/FIDO2Client)
* [maven server tutorial](https://www.runoob.com/maven/maven-tutorial.html)