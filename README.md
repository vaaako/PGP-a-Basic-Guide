# Introduction
*GPG*, or *GNU Privacy Guard*, is a publick key cryptography implementation.<br>
This allows for the secure transmission of information between parties and can be used to verify that the origin of a message is genuine.

Usage example:
- Your friend creates a PGP, now he have the **Public** and **Private key**.
 The **Private key** is used to *Decrypt* a message *Encrypted* with the **Public key**.

- The **Public key** can be used for anyone to *Encrypt* a message, but only who has the **Private key** can read the message.
 So you use the **Public key** of your friend to send him a message, so only him can read the message.

- Your friend uses the **Private key** to read your message and sends back a message to you, but this time he *sign*,
 a *signature* is used to know who is the author of the message, only the **Private key** can be used to make a signature.



# Set Up GPG Keys (Linux only)
GPG is installed by default in most distributions.

If for any reason GPG is not installed, on `Ubuntu` and `Debian`, you can update the local repo index and install it by typing:
```sh
sudo apt-get update
sudo apt-get install gnupg
```

To begin, you need to create a key pair, You can do this by issuing the following command:
```sh
gpg --full-generate-key
```

This will take you through a few questions that you configure your keys.
**WARNING:** Your name and adress are visible for anyone with the **Public** or **Private key**.

- *Please select what kind of key you want:* **1**
- *What keysize do you want?* **4096**
- *Key is valid for?* **1y *(expires after 1 year. If you are just testing, you may want to create a short-lived key
 the first time by using a number like "1" (1 day) instead.)***

- *Is this correct?* **y *(If not you fix :P)***
- *Real name:* **Don't necessary need to be your real name**
- *Email address:* **This need to be a real email adress**
- *Comment:* **Optional comment that will be visible in your signature**
- *Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit?* **O**
- *Enter passphrase:* **Enter a secure passphrase here*(Remeber of that password, you will ned it to decrypt a message )***

GPG will generate the keys using entropy. Entropy describes the amount of unpredictability and nondeterminism that exists in a system.
 GPG needs this entropy to generate a secure set of keys.

## Get Public Key
You can get your **Public key** by the fingerprint. Use this command to see all your keys:
```sh
gpg -K
```

Now use this command to display the **Public key**:
```sh
gpg -a --export *fingerprint*
```


# Create a Revocation Certificate (you can skip that)
You need to have a way of invalidating your key pair in case there is a security breach or in case you lose your secret key.
 There is an easy way of doing this with the GPG software.


This should be done as soon as you make the key pair, not when you need it.
 This revocation key must be generated ahead of time and kept in a secure, separate location in case your computer is compromised
 or inoperable. To generate a revocation key, type:
```sh
gpg --output ~/revocation.crt --gen-revoke your_email@address.com
```

You should immediately restrict the permissions on the generated certificate file in order to prevent unauthorized access:
```sh
chmod 600 ~/revocation.crt
```


# How To Import Other Users Public Keys
GPG would be pretty useless if you could not accept other public keys from people you wished to communicate with.

You can import someone’s public key in a variety of ways. If you’ve obtained a public key from someone in a text file,
 GPG can import it with the following command:
```sh
gpg --import name_of_pub_key_file
```

There is also the possibility that the person you are wishing to communicate with has uploaded their key to a public key server.
 These key servers are used to house people’s public keys from all over the world.

A popular key server that syncs its information with a variety of other servers is the MIT public key server.
 You can search for people by their name or email address by going here
 in your <a href="https://pgp.mit.edu/" target="_blank">web browser</a> or using GPG:
```sh
gpg --keyserver pgp.mit.edu  --search-keys search_parameters
```


## How To Verify and Sign Keys
How do you know that the person giving you the public key is who they say they are? Instead of verifying the entire public
 keys of both parties, you can simply compare the “fingerprint” derived from these keys.
 This will give you a reasonable assurance that you both are using the same public key information.
```sh
gpg --fingerprint your_email@address.com
```

This will produce a much more manageable string of numbers to compare. You can compare this string with the person themselves,
 or with someone else who has access to that person.

## Sign Their Key
Signing a key tells your software that you trust the key that you have been provided with and that you have verified that
 it is associated with the person in question.
```sh
gpg --sign-key email@example.com
```

You should allow the person whose key you are signing to take advantage of your trusted relationship by
 sending them back the signed key. You can do this by typing:
```sh
gpg --output ~/signed.key --export --armor email@example.com
```

When they receive this new, signed key, they can import it, adding the signing information you’ve
 generated into their GPG database. They can do this by typing:
```sh
gpg --import ~/signed.key
```


## Making and verifying signatures
A digital signature certifies and timestamps a document. If the document is subsequently modified in any way,
 a verification of the signature will fail. A digital signature can serve the same purpose as a hand-written
 signature with the additional benefit of being tamper-resistant. The GnuPG source distribution, for example, is
 signed so that users can verify that the source code has not been modified since it was packaged.

The command-line option **--sign** is used to make a digital signature. The document to sign is input, and the signed document is output.
```sh
gpg --output output_file_name --sign file_to_be_signed
```

The document is compressed before signed, and the output is in binary format.<br>
Given a signed document, you can either check the signature or check the signature and recover the original document.

To check the signature use the **--verify** option. To verify the signature and extract the document use the **--decrypt** option.
The signed document to verify and recover is input and the recovered document is output.


```sh
gpg --output output_file_name --decrypt file_with_signature
```


## Clearsigned documents
A common use of digital signatures is to sign usenet postings or email messages.

In such situations it is undesirable to compress the document while signing it.
 The option **--clearsign** causes the document to be wrapped in an ASCII-armored signature but otherwise does not modify the document.

```sh
gpg --clearsign file_name
```


## Deatached signatures
A signed document has limited usefulness. Other users must recover the original document from the signed version,
 and even with clearsigned documents, the signed document must be edited to recover the original.

Therefore, there is a third method for signing a document that creates a detached signature.

A detached signature is created using the --detach-sig option.
```sh
gpg --output output_file_name --detach-sig file_name
```

Both the document and detached signature are needed to verify the signature.
 The **--verify** option can be to check the signature.
```sh
gpg --verify file_with_signature original_file_name
```


# How To Make Your Public Key Highly Available
Because of the way that public key encryption is designed, there is not anything malicious that can happen if
 unknown people have your public key.

With this in mind, it may be beneficial to make your public key publicly available.
 People can then find your information to send you messages securely from your very first interaction.

You can send anyone your public key by requesting it from the GPG system:
```sh
gpg --output ~/mygpg.key --armor --export your_email@address.com
```

You can then send this file to the other party over an appropriate medium.<br>
If you want to publish your key to a key server, you can do it manually through the forms available on most of the server sites.

Another option is to do this through the GPG interface. Look up your key ID by typing:
```sh
gpg --list-keys your_email@address.com
```

The ID you are looking for is the highlilghted here: `pub   4096R/**311B1F84M** 2013-10-04`<br>
To upload your key to a certain key server, you can then use this syntax:
```sh
gpg gpg --send-keys --keyserver pgp.mit.edu key_id
```

The key will be uploaded to the specified server. Afterwards, it will likely be distributed to other key servers around the world.


# Encrypt and Decrypt Messages with GPG
## Encrypt
```sh
gpg --encrypt --sign --armor -r person@email.com name_of_file
```

This encrypts the message using the recipient’s public key, signs it with your own private key to guarantee that
 it is coming from you, and outputs the message in a text format instead of raw bytes.
 The filename will be the same as the input filename, but with an .asc extension.

You should include a second "*-r*" recipient with your own email address if you want to be able to read the encrypted message.
 This is because the message will be encrypted with each person’s public key, and will only be able to be decrypted with the associated private key.

So if it was only encrypted with the other party’s public key, you would not be able to view the message again, unless
 you somehow obtained their private key. Adding yourself as a second recipient encrypts the message two separate times,
 one for each recipient.


## Decrypt
```sh
gpg file_name.asc
```

If instead of a file, you have the message as a raw text stream, you can copy and paste it after typing **gpg**
 without any arguments. You can press "CTRL-D" to signify the end of the message and GPG will decrypt it for you.

# Key Maintenance
There are a number of procedures that you may need to use on a regular basis to manage your key database.

To list your available GPG keys that you have from other people, you can issue this command:
```sh
gpg --list-keys
```

Your key information can become outdated if you are relying on information pulled from public key servers.
 You do not want to be relying on revoked keys, because that would mean you are trusting potentially compromised keys.

You can update the key information by issuing:
```sh
gpg --refresh-keys
```

This will fetch new information from the key servers.

You can pull information from a specific key server by using:
```sh
gpg --keyserver key_server --refresh-keys
```
