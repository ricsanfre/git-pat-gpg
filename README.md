# How to Use of GitHub's Personal Access Token (PAT) with git cli and GPG

In Linux GPG encryption can be used to encrypt/decrypt passwords and tokens data using a GPG key-pair
GnuPG is a free software implementation of the OpenPGP standard that allows you to encrypt and sign your data and communications using GPG encryptions
GnuPG can be used as cross-platform password manager, including GIT HTTPS credetials. 

## GnuPG Installation and configuration

### Step 1. Install GnuPG packet

    sudo apt install gnupg 

Check it is installed

    gpg --help

### Step 2. Generating Your GPG Key Pair

GPG key-pair consist on a public and private key used for encrypt/decrypt

    gpg –-gen-key

The output of the command is like this:

```
gpg (GnuPG) 2.2.4; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Note: Use "gpg --full-generate-key" for a full featured key generation dialog.

GnuPG needs to construct a user ID to identify your key.

Real name: Ricardo
Email address: ricsanfre@gmail.com
You selected this USER-ID:
    "Ricardo <ricsanfre@gmail.com>"

Change (N)ame, (E)mail, or (O)kay/(Q)uit? O
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: /home/ansible/.gnupg/trustdb.gpg: trustdb created
gpg: key D59E854B5DD93199 marked as ultimately trusted
gpg: directory '/home/ansible/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/home/ansible/.gnupg/openpgp-revocs.d/A4745167B84C8C9A227DC898D59E854B5DD93199.rev'
public and secret key created and signed.

pub   rsa3072 2021-08-13 [SC] [expires: 2023-08-13]
      A4745167B84C8C9A227DC898D59E854B5DD93199
uid                      Ricardo <ricsanfre@gmail.com>
sub   rsa3072 2021-08-13 [E] [expires: 2023-08-13]

```

During the generation process you will be prompted to provide a passphrase.


## Exporting Your Public Key
If you need to export and share your public key to others, you run the commands below… The public key is used to authenticate that the content encrypted by you actually came from you…

It is also used to decrypt the content you encrypted…

   gpg --armor --export ricsanfre@gmail.com > public_key.asc

You can also use the commands below to export the key into a readable text file…

   gpg --armor --output key.txt --export ricsanfre@gmail.com

You can then send the public key file to those who should get it..



## Git PAT encryption and usage

First you need to obtain a valid authentication token for GitHub. See procedure how to obtain a [personal accesss token](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token)

### Step 1. Encrypt GitHub PAT token with GPG

To encrypt token(password) run:

    gpg -e -o [PATH_TO_ENCRYPTED_TOKEN] -r "[GPG_KEY_USER_ID]"

    gpg -e -o $HOME/git_pat_gpg -r ricsanfre@gmail.com

type the token (or copy-paste it) then press Ctrl+D for ending input, or use file name with this token.

### Step 2. Make a custom git credential helper: 

Create bash file with name `git-credential-gpgpat` (without .sh extension) in $HOME 

```
#!/bin/bash
token=`gpg -d -r "[GPG_KEY_USER_ID]" [PATH_TO_ENCRYPTED_TOKEN] 2>/dev/null`
echo protocol=https
echo host=[YOUR_HOST]
echo username=[YOUT_GIT_USER_NAME]
echo password=$token
```

### Step 3. Check script

Execute the script and checks that is outputing the unencrypted password


### Step 3. Add the git credential helper


    git config --global credential.helper [ABSOLUTE_PATH_TO_BASH_SCRIPT ]

    git config --global credential.helper $HOME/git_credential_gpgpat
    