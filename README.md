# CipherChat

An end-to-end ecrypted instant messaging application built on MERN Stack.

### Implementation Process:

1. The User registers with two input fields:
   - `username` (unique for all users)
   - `password`
2. SHA256 Hash function comes into play with this two fields, creating these datas:
   - `auth = SHA256(username + password);`
     - This gets saved onto the database, and will be used to authenticate user  
       without storing the password.
   - `passphrase = SHA256(auth + password);`
     - This is stored locally only. This serves as the passphrase seed for generating  
       the user's private key and public key.
   - `privateKey = generateRSAKey(passphrase, 1024);`
     - Creates a 1024 bit RSA private key, used to generate public key, this is not  
       sent to the server.
   - `publicKey = publicKeyString(privateKey);`
     - Derives a public key from the private key, this is sent onto the server and saved  
       for everyone to see and able to send messages privately.
3. The User Logins with `username` and `password`
   - `auth = SHA256(username + password)` for authentication
     - if success, jwt token will be saved locally and the `passphrase` for generating  
       the keys.
4. Global Channel **(Not Encrypted)**
   - Clients sends `msgObj` containing `senderName`, `message`, and `timestamp` on plain text  
     and `"global"` as the msgObj's `channel`
   - Client fetches all msgObj with `channel === "global` as filter, and displaying them in  
     asc order based on their timestamp. \* Styling: If `senderName === username`, chatbubble will be displayed on the left side  
      and be labeled sent by `You`.
5. Private Channels **(Encrypted e2e)**
   - The User chooses someone to chat on a private channel
     - `userPublicKey` and `otherUserPublicKey` hashed with md5 will be their converstation's  
       private `channelId`.
       - `channelId = "0eRdpe5e7718b338fy12g130f6cb3f24"`
   - When The User sends a message, it creates 2 encrypted copies before it's sent to server.
     1. msgObj is encrypted with the `userPublicKey`. `name`, `message`, and `timestamp` fields are all encrypted.  
        Adds a `pbkHash` field which is a `SHA256` hash of the public key used to encrypt the msgObj.
        \_ `pbkHash = 5ccd628243718daagr5a2a10444c2a30b5f51016dd12382f8af6f2dffa721`
        - The User will be the only one who can decrypt and read this message.
     2. msgObj is encrypted with The `otherUserPublicKey`. Same fields encrypted, same field added.
        - otherUser will be the only one who can decrypt and read this message.
     - Two encrypted copies: One encrypted with the **sender's public key** and one with the **recipient's public key**
   - Client fetches all msgObj with `channel === "0eRdpe5e7718b338fy12g130f6cb3f24"` and `pbkHash === 5ccd628243718daagr5a2a10444c2a30b5f51016dd12382f8af6f2dffa721` as filter, and displaying them in asc  
     order based on their timestamp. \* This will only fetch msgObjects encrypted using the `userPublicKey` which can then be decrypted  
      using `userPrivateKey`

### Simple Explanation

Alice and Bob exchanges public keys with each other, when Alice sends a message to Bob she encrypts it with Bob's  
Public Key and another copy with her publicKey. Bob does the same when he sends his messages. Alice then fetches  
all messages on their channel that was encrypted using her public key and decrypts the conversation with her private  
key then displayed on the client.

#### Topics Related & Technologies Used

Cryptography, Assymetric Encryption, RSA, Hash Functions, WebDev, Javascript, NodeJs, ReactJs, ExpressJS, MongoDB, WebSockets
