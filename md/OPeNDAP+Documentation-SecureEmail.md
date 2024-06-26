# Secure Email

Sometimes it's very important to be sure who you are communicating with
and that only that person or group of people can read your messages. The
GPG software provides a way to do that usiing Public Key Cryptography.

See \[#Encryptingfiles below\] for information on using gpg to encrypt
files so only a designated set of recipients can read them.

First off, since we use Macs for most of our admin work, getting GPG/PGP
on a mac and integrated into the mailer is pretty important. You can use
the gpg software as a command-line tool, too, but it's nice to have its
function integrated into the mailer, at least those for encrypting,
decrypting and signing messages. I also use Thunderbird on Linux, and
it's got a neat plugin for GPG which I describe here.

## Get the basic software

- First, get PGP itself from <http://macgpg.sourceforge.net/>. All you
  need to do is to download the package and install it. The package
  installs it all in the correct places.
- *Trick*: If you have keys on another system and you want to use them
  on your mac, just copy the entire \`.gnupg\` directory to your home
  directory on the mac (\`scp -r <machine>:.gnupg .\`).
- If you don't have a key made up, use gpg to make one. How? see [How to
  make
  keys](http://scm.opendap.org:8090/trac/wiki/SecureEmail#Howtomakekeys)
  or \`man gpg\`

## Get a decent key manager (?)

- I think most of the GUI stuff is a waste, but [GPG Keychain
  Access](http://macgpg.sourceforge.net/) (it's at the same place as the
  base software) seems OK. Really, on the mac the gpg command line tool
  seems like the best bet for working with keys. You need to do a very
  few things with the keys: make keys, export the public keys, sign
  other people's keys. The GUI doesn't do a good job of any of those
  things

## Integrating GPG with a mailer

### Mac Mail

- Get [PGP For Apple's
  Mail](http://www.sente.ch/software/GPGMail/English.lproj/GPGMail.html#Download).
  To install, stop Mail, run the script 'Install GPGMail'.
- Done. There are some menu items you should look at Preferences... and
  the Message and Window menus. It's really pretty slick.
- When you use the command line tool to import keys (described below) be
  sure to update the mailer using the GPG menu option under the Windows
  menu.

### Thunderbird

There's a great plugin for pgp on Thunderbird named Enigmail. You'll
need Thunderbird 2.x, but both are easy to get. Combined, they make for
a great mailer on Linux systems. Be careful with your private keys if
you use the copy trick I show above... The fewer places you have your
private key, the less likely it is to get out into the wild.

# Digital Signatures for OPeNDAP

This describes how to encrypt files and sign source and binary packages
for distribution.

## How to make keys

Here's a short description, but it's really incomplete. There's a great
[PGP/GPG guide](http://www.gnupg.org/gph/en/manual.html) which is well
worth reading over. Even though it's 40 pages, you really can get a lot
out of the first 5 or 6. With that in mind:

- Make a key using \`gpg --gen-key\`. Make the default kind so you can
  sign and encrypt. When you give it an email address, make sure that's
  the one you will use. If you want support for more than one email,
  make keys for each one. (There might be other ways to support multiple
  emails with one key using subkeys.)
- You need to get your (public) keys to people. Use \`gpg --export
  --armor --output <filename> <email for key>\`. By convention the file
  name ends in \`.asc\`. You send this file to people and they import
  it.
- The --armor option encodes the public key in 7-bit ASCII so it's
  mailer-safe. Binary ones are a pain and might not work for your
  recipient.
- When you make your key, or shortly thereafter, make a revocation key
  too. This way if you forget the password to your private key you can
  declare it invalid, issue a new key, and so on. Really, *don't forget
  your key's password and don't let the private key get onto some else's
  computer*.
- Here's how to import: \`gpg --import <file>\`. I've also found that
  the GPG Keychain Access program imports keys pretty well.
- Read the GPG Guide for more information about signing keys, files, all
  that jazz.

## Protecting keys

It's important to keep the private key private! Since people really
trust this stuff, if someone gets your private key it will be very hard
to convince other people the stuff they did with it was not your doing.
So,

1.  Do not let it get away from you!
2.  Choose a good password when you make the key.
3.  Do not forget that password. If you do, ...
4.  Use a revocation key to invalidate the key.
5.  Store as few copies of the private key as you can.
6.  Burn it to a CD-ROM, etc., but make sure that it is not on the USB
    drive you take to that meeting... ;-)

## Encrypting files

To encrypt a file, use \`gpg\` with the \`-e\` and \`-s\` (\`--encrypt
--sign\`) switches. These select encryption and signing. Encryption is
obvious; signing is a good idea since it proves to the recipient that
*you* did, in fact, perform the encryption as well as when you did it.
The command looks like:

\`gpg -e -s <filename>\`

The gpg program will then prompt you for your password (to sign the
encrypted file) and the recipients email addresses (so their public keys
can be used for the encryption). It's pretty verbose so you can tell
what's going on. Enter the recipients one per prompt and use a blank
line to end the recipients.

To encrypt a file using a specific key (e.g., using a key other than one
bound to your ID), use the \`--local-user <key ID>\` option where
\`<key ID>\` is the email address bound to that key. If you're using the
OPeNDAP key to encrypt something, use the OPeNDAP key's ID for the ID of
the recipient.

## Decrypting files

To decrypt a file use the \`-d\` (\`--decrypt\`) command. You need to be
one of the intended recipients to do this and you'll need the private
key's password.

## Detached signatures

Detached signatures are a way to ensuring the integrity of a file
without altering the file by binding a digital signature directly to it.
You can make a detached signature using gpg:

\`gpg --local-user <key ID> --detach-sign <file>\`

The result is that \`<file>\` will be signed using \`<key ID>\` (if you
want to sign using your default private key you can omit this option)
and the signature (which will be a binary file) will be written to
\`<file>.sig\`. The --armor option will write out the signature in
ASCII.

If you have a large number of files you need to sign, you can use the
following:

``` bash
for f in *.rpm
do
    gpg --batch --passphrase <password> --batch --detach-sign --local-user security@opendap.org $f
    echo $f
done
```

Where \`pw_tmp\` holds the pass-phrase text that you would normally type
in once for each file. NB: You may be able to omit the *pw_tmp* file (an
still avoid typing the same password over and over if *gpg* is setup to
remember passwords for a short time (as it is on OS/X).

But you *must* be very careful to immediately delete the pw_tmp file
*right away* because it contains the private key's pass-phrase. This
will stave off insanity when you have to sign 30 rpms and dmgs for the
complete source and binary distributions on Linux and OS/X. Just
remember to delete that file *right away*...

## Using detached signatures

To verify a signature, you must have the public key matching the private
key that made the signature. In the directory with the signed file, use:

\`gpg --verify <file>.sig\`

## Keyservers

A keyserver provides a way to store your public key in a place where
people will know to look for it (for example, the Mac Mail plugin knows
about keyservers). One keyserver is at <http://wwwkeys.pgp.net>. There's
some good [documents describing key
servers](http://www.rossde.com/PGP/pgp_keyserv.html) available.

Note: Once you submit a public key to a keyserver, it can be very hard
to get it off that server, especially since most of the servers
synchronize with other keyservers. Make sure you generate a revocation
key and save it off if you do use a keyserver.

13 Aug 2007