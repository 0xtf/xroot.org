---
layout: default
title: OpenPGP Signing Policy
---

# openpgp key signing policy

## Preamble
This policy is valid for all signatures made by the key on the [contact](contact.html) page.


## Location
I live in Lisbon (Portugal) and I'm available to sign keys at any time (really!). If you live in Lisbon or if you're passing by, I encourage you to contact me so we can arrange a meeting.


## Prerequisites for signing
The signee (the key owner who wishes to obtain a signature to his/her key from me) must have an easy way of sharing his/her public key. I do not require the key to be available on a Key Server, even though it's encouraged. I don't mind fetching the key from a private location, such as a homepage.

The signee must be able to prove his/her identify by a way of a valid identity card and valid driving license. Both these documents must contain a picture of the signee.

The signee must have his/her OpenPGP key fingerprint printed on a piece of paper or card. I personally use [Moo Cards](http://moo.com/). The fingerprint is the output of the following command (or equivalent if not a user of GnuPG):

```
gpg --fingerprint 0x12345678
```

The signee must be patient and understand that the whole process of signing a key takes some time.

## The act of signing
After having received (or exchanged) the proof detailed in the above, I will sign the sheet of paper myself to avoid a fraud.

When at home, I will sign the key and upload it to an SKS Key Server, unless I didn't fetch the key from a Key Server or if asked not to. If that's the case, I'll send separate signatures of each UID, on a encrypted e-mail message, to the corresponding e-mail addresses (unless they're Level 2 signature).

The levels of signatures are described below.

## Level of signatures

The levels of signatures, as described on [RFC 4880, Section 5.2.1 - Signature Types](http://tools.ietf.org/html/rfc4880#section-5.2.1), used by my signatures:

* Level 2 - Given to sign-only keys. Usually their UIDs are of the type "Firstname Lastname" and not "Firstname Lastname <user@mailaddress.invalid>" which means that I can't (automatically) send verification mails to them.
* Level 3 - Given to sign-and-encrypt keys, which pass all the checks: I have met the signee, I have verified his/her identity card and fingerprint.
