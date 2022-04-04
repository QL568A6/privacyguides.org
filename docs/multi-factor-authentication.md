---
title: Multi-factor Authentication
description: "Using strong MFA can stop over 99% of unauthorized account accesses, and it's easy to set up on the services you already use."
icon: 'material/two-factor-authentication'
---

**Two-Factor Authentication** (also known as **2FA**, **Multi-Factor Authentication**, or **MFA**) is a security mechanism that requires additional steps beyond simply your username/email and password. If you've ever had to enter a 6-digit code sent to your phone to log in to a website, that's an example of 2FA.

The idea behind 2FA is that even if a hacker is able to figure out your password (something you *know*), they will still need a device you own like your phone (something you *have*) in order to generate the code needed to log in to your account. 2FA methods vary in security based on this premise: The more difficult it is for an attacker to gain access to your 2FA method, the better. Examples of 2FA methods from weakest to strongest are Email codes, SMS codes, Push Notifications, Time-based One-time Password (TOTP), Yubico OTP, and FIDO2/U2F.

## MFA Method Comparison

### Email or SMS 2FA
==**Email** or **SMS 2FA** are better than nothing at all, but only marginally.== Getting a code over SMS or Email takes away from the "something you *have*" idea, because there are a variety of ways a hacker could take over your phone number or gain access to your emails without having physical access to any of your devices at all! 

To make matters worse, if a hacker gains access to your email, they typically would be able to use it to both reset your password and receive the second factor authentication code, giving them full access to your account. ==If you have to choose between SMS 2FA and Email 2FA, and if you do not mind giving out your phone number, SMS is preferable.==

### Push Notifications
**Push Notifications** take the form of a message being sent to an app on your phone asking you to confirm new account logins. This is a lot better than SMS or Email, since an attacker typically wouldn't be able to get these push notifications without having an already logged-in device, thus requiring them to compromise your devices first. However, they can be easy to click through and accept accidentally, and are typically sent to *all* your devices at once, widening the availability of the 2FA code if you have many devices. ==This solution is also generally a cloud based solution, so you are reliant on the company you have an account with to implement their custom solution securely and you have to fully trust them to not compromise your second factor authentication or to profile you.== Finally, it requires you to keep an app for every login you have on your mobile device, which may or may not be convenient to you.

### Time-based One-time Password (TOTP)
**TOTP** is the most common form of two factor authentication. The service shares a secret code with you (a seed) that you would be then storing on your **authenticator apps** or **hardware security keys**. The six digits code is derived from the seed and the current time, making it difficult to duplicate the codes so they cannot be reproduced by a hacker later, even if they briefly had access to your current six digits code.

If you have a hardware security key with TOTP support, it is highly recommended that you store your seeds on the hardware key instead on a device with 24/7 internet access and where you are probably storing your password manager. The implementation for the TOTP secrets storage varies from key to key, so be sure to check our [Hardware Security Keys](#hardware-security-keys) section below for caveats.

### Yubico OTP
**Yubico OTP** is a authentication protocol typically implemented in hardware security keys. They key first generates a public ID, a private ID, and a Secret Key. These information are then uploaded to the Yubico OTP server.

When the user login to a website, all they need to do is to physically touch the security key. The security key will emulate a keyboard and type out a one-time password to authenticate with the website. The website will then forward the one-time password to the Yubico OTP server for validation. For an indepth explanation on how the protocol works, check out Yubico's [documentation](https://developers.yubico.com/OTP/OTPs_Explained.html).

![](/assets/img/multi-factor-authentication/yubico-otp.png)

When compared to TOTP, Yubico OTP has some pros and cons. Unlike TOTP, user would quickly recognize that their second factor authentication is compromised should an attacker manage to clone their hardware security key, as the counter stored on their security key would be smaller than what is stored on a server and their login would be rejected. However, this is still a cloud based service, and still need to trust Yubico to set up their servers securely and to not profile the user. The public ID associated with Yubico OTP is reused on every website and can be another avenue for third parties to profile the user. The number of Yubico OTP a key can generate is limited to 32767 before it needs a reset.

==If your threat model requires you to have different identities on different websites, **do not** use Yubico OTP with the same hardware security key across those websites.==

### FIDO2 / U2F
**FIDO2 / U2F** is the most secure and private form of second factor authentication. While the user experience is similar to Yubico OTP, the key does not type out a one-time password and validate with a third party server. Rather, it uses public key authentication between the website and the key itself. Certain websites can also request that the key's PIN is entered before any authentication can happen.

A detailed explanation on how the protocol works is explained at an RSA conference:

<iframe width="100%" style="height:50vh"
  src="https://www.youtube-nocookie.com/embed/aMo4ZlWznao"
  title="How FIDO2 and WebAuthn Stop Account Takeovers"
  frameborder="0"
  allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
  allowfullscreen>
</iframe>

FIDO2 / U2F has the superior security and privacy properties when compared to any multi factor authentication protocols. The public key authentication done by FIDO2/U2F is more secure than shared secrets that is used in Yubico OTP and TOTP. FIDO2/U2F does not use any public ID, so the key is not identifiable across different websites like Yubico OTP. It also does not use any third party cloud server for authentication, everything is done between the key and the website the user is logging into. Even better, FIDO2 / U2F also uses a counter like Yubico OTP to help detect key cloning. ==If a website or service supports FIDO2 / U2F for the authentication, it is highly recommended that you use it over any other form of 2FA.==

## General Recommendations
When configuring second factor authentication, keep in mind that it is only as secure as your weakest authentication method you use. Thus, it is important that you only use the best 2FA method available. For instance, if you are already using TOTP, you should explicitly disable Email and SMS 2FA. Likewise, if you are already using FIDO2 / U2F, you should not be using Yubico OTP or TOTP on your account.

You should always have backups for your second factor authentication. Hardware security keys can get lost, stolen, or simply stop working over time. Thus, it is highly recommended that you have a pair of hardware security keys with the same access to your accounts instead of just one. When using TOTP with an authenticator app, be sure to back up your recovery keys, the app itself, or copy the seed to another instance of the app on a different phone.

When buying a security key, it is important that you change the default credentials, setup password protection for the key, and enable touch confirmation if your key supports such feature. Products such as the [Yubikey](#yubikey) have multiple interfaces with seperate credentials for each one of them, so you should go over each interface and set up protection as well.

If you have to use Email for 2FA, make sure that the email account itself is secured with a proper 2FA method. Likewise, if you use SMS 2FA, use a carrier who will not switch your phone number to a new SIM card without account access or use a dedicated VOIP number from a provider with similar security to avoid a [SIM swap](https://en.wikipedia.org/wiki/SIM_swap_scam) attack.

## Hardware Security Keys

### YubiKey

!!! recommendation

    ![YubiKeys](/assets/img/multi-factor-authentication/yubikey.png)

    The **Yuibkey** was one is one of the most popular security keys. Some Yubikey models have a wide range of features such as for [Universal 2nd Factor (U2F)](https://en.wikipedia.org/wiki/Universal_2nd_Factor), [FIDO2 WebAuthn](https://en.wikipedia.org/wiki/WebAuthn), [Yubico OTP](https://developers.yubico.com/OTP/), [PIV](https://en.wikipedia.org/wiki/FIPS_201), [OpenPGP](https://developers.yubico.com/PGP/), [TOTP and HOTP](https://developers.yubico.com/OATH/) authentication. One of the benefits of these models is that one key can do most things.

    The [comparison table](https://www.yubico.com/store/compare/) shows the features and how the Yubikeykeys compare. We highly recommend that you select keys from the Yubikey 5 Series.

    Yubikeys can be programmed using the [Yubikey Manager](https://www.yubico.com/support/download/yubikey-manager/) or [Yubikey Personalization Tools](https://www.yubico.com/support/download/yubikey-personalization-tools/). For managing TOTP codes, the user can use the [Yubico Authenticator](https://www.yubico.com/products/yubico-authenticator/). All of Yubico's clients are open source.

    For models which support HOTP and TOTP, there 2 slots in the OTP interface which could be used for HOTP and 32 slots to store TOTP secrets. These secrets are stored encrypted on the key and never expose them to the devices they are plugged into. Once a seed is given to the Yubico Authenticator, it will only give out the six-digit codes, but never the actual seed. This model helps limit what an attacker can do if they compromise one of the devices running the Yubico Authenticator and make the Yubikey resistant to a physical attacker.

    [Visit yubico.com](https://www.yubico.com){ .md-button .md-button--primary } [Privacy Policy](https://www.yubico.com/support/terms-conditions/privacy-notice){ .md-button }

!!! attention
    The firmware of Yubikeys are not open source and are not updatable. If you want features in newer firmware versions, or if there is a vulnerability in the firmware version you are using, you would need to purchase a new key.

### Nitrokey / Librem Key

!!! recommendation

    ![Nitrokey](/assets/img/multi-factor-authentication/nitrokey.jpg){ align=right }

    **Nitrokey** has a security key capable of [FIDO2 WebAuthn](https://en.wikipedia.org/wiki/WebAuthn) called the **Nitrokey FIDO2**. For PGP support, you need to purchase one of their other keys such as the **Nitrokey Start**, **Nitrokey Pro 2** or the **Nitrokey Storage 2**.

    The [comparison table](https://www.nitrokey.com/#comparison) shows the features and how the Nitrokeys compare. The **Nitrokey 3** listed there will have a combined feature set.

    Nitrokeys can be configured using the [Nitrokey app](https://www.nitrokey.com/download).

    For the models which support HOTP and TOTP, there are 3 slots for HOTP and 15 for TOTP. Nitrokeys which can act as a password manager can store 16 different credentials and encrypt them using the same password as the OpenPGP interface.

    The Nitrokey Pro 2, Nitrokey Storage 2, and the upcoming Nitrokey 3 supports system integrity verification for laptops with the [Coreboot](https://www.coreboot.org/) + [Heads](https://osresearch.net/) firmware. Purism's [Librem Key](https://puri.sm/products/librem-key/) is a rebranded NitroKey Pro 2 with similar firmware and can also be used for the same purposes.

    The Nitrokey has an open source firmware, unlike the Yubikey. The firmware on modern NitroKey models (except the **NitroKey Pro 2**) are updatable.

    [Visit nitrokey.com](https://www.nitrokey.com){ .md-button .md-button--primary } [Privacy Policy](https://www.nitrokey.com/data-privacy-policy){ .md-button }

!!! warning

    Nitrokeys do **not** store HOTP/TOTP secrets in an encrypted format and are vulnerable to physical attacks.

!!! attention

    Resetting the OpenPGP interface on a Nitrokey will also make the password database [inaccessible](https://docs.nitrokey.com/pro/factory-reset.html).

!!! attention

    The Nitrokey app, while compatible with Librem Keys, requires `libnitrokey` version 3.6 or above to recognize them. Currently, the package is outdated on Windows, macOS, and most Linux distributions' repository, so you will likely have to compile the Nitrokey app yourself to get it working with the Librem Key. On Linux, you can obtain an up-to-date version from [Flathub](https://flathub.org/apps/details/com.nitrokey.nitrokey-app).

## Authenticator Apps

Authenticator Apps implement a security standard adopted by the Internet Engineering Task Force (IETF) called **Time-based One-time Passwords**, or **TOTP**. This is a method where websites share a secret with you which is used by your authenticator app to generate a six (usually) digit code based on the current time, which you enter while logging in for the website to check. Typically these codes are regenerated every 30 seconds, and once a new code is generated the old one becomes useless. Even if a hacker gets one six-digit code, there is no way for them to reverse that code to get the original secret, or otherwise be able to predict what any future codes might be.

We highly recommend that you use mobile TOTP apps instead of desktop alternatives as Android and IOS have better security and app isolation than most desktop operating systems.

### Aegis Authenticator

!!! recommendation

    ![Aegis logo](/assets/img/multi-factor-authentication/aegis.png){ align=right }

    **Aegis Authenticator** is a free, secure and open source app to manage your 2-step verification tokens for your online services.

    [Visit getaegis.app](https://getaegis.app){ .md-button .md-button--primary }

    **Downloads:**
    - [:pg-f-droid: F-Droid](https://f-droid.org/en/packages/com.beemdevelopment.aegis)
    - [:fontawesome-brands-google-play: Google Play](https://play.google.com/store/apps/details?id=com.beemdevelopment.aegis)
    - [:fontawesome-brands-github: GitHub](https://github.com/beemdevelopment/Aegis)

### Ravio OTP

!!! recommendation

    ![Ravio OTP logo](/assets/img/multi-factor-authentication/ravio-otp.png){ align=right }

    **Ravio OTP** is a native, lightweight and secure time-based (TOTP) & counter-based (HOTP) password client built for iOS.

    [Visit website](https://github.com/raivo-otp/ios-application){ .md-button .md-button--primary }
    
    **Downloads:**
    - [:fontawesome-brands-app-store-ios: App Store](https://apps.apple.com/us/app/raivo-otp/id1459042137)

## More places to setup 2FA

### Windows

Yubico has a dedicated [Credential Provider](https://docs.microsoft.com/en-us/windows/win32/secauthn/credential-providers-in-windows) that adds Challenge-Response authentication for the username + password login flow for local Windows accounts. If you have a Yubikey with Challenge-Response authentication support, it is highly recommended that you follow this [guide](https://support.yubico.com/hc/en-us/articles/360013708460-Yubico-Login-for-Windows-Configuration-Guide) and set up second factor authentication for your Windows computer.

### macOS

macOS has [native support](https://support.apple.com/guide/deployment/intro-to-smart-card-integration-depd0b888248/web) for authentication with smart cards (PIV). If you have a smartcard or a hardware security key that supports the PIV interface such as the Yubikey, we highly recommend that you follow your smartcard/hardware security vendor's documentation and set up second factor authentication for your macOS computer.

A detailed guide on how to set this up with a Yubikey can be found [here](https://support.yubico.com/hc/en-us/articles/360016649059).

After your smartcard/security key is set up, we highly recommend running `sudo defaults write /Library/Preferences/com.apple.loginwindow DisableFDEAutoLogin -bool YES` in the terminal to prevent the 2FA bypass when the computer boots up.

### Linux 

!!! warning
    If the hostname of your system changes (such as due to DHCP), you would be unable to login. It is vital that you setup a proper hostname for your computer before following this guide.

The `pam_u2f` module on Linux can provide two factor authentication for user login on most popular Linux distributions. If you have a hardware security key that supports U2F, we highly recommend that you follow the [Yubico's documentation](https://support.yubico.com/hc/en-us/articles/360016649099-Ubuntu-Linux-Login-Guide-U2F) and setup second factor authentication for your Linux machine. This guide does **not** apply to Qubes OS.

### Qubes OS

Qubes OS has support for Challenge-Response authentication with Yubikeys. If you have a Yubikey with Challenge-Response authentication support, we highly recommend that you follow [Qubes OS's documentation](https://www.qubes-os.org/doc/yubikey/) and setup second factor authentication for your Qubes OS machine.

### SSH

#### Hardware security keys
SSH 2FA could be set up using multiple different authentication methods that are popular with hardware security keys. We recommend that you check out Yubico's [documentation](https://developers.yubico.com/SSH/) on how to set this up.

#### Time-based One-time Password (TOTP)
SSH 2FA can also be set up using TOTP. We recommend that you follow Digital Ocean's [documentation](https://www.digitalocean.com/community/tutorials/how-to-set-up-multi-factor-authentication-for-ssh-on-ubuntu-16-04) on how to set this up.

### Keepass Databases
Keepass databases could be secured using Challenge-Response or HOTP as a second factor authentication. We recommend that you follow Yubico's [documentation](https://support.yubico.com/hc/en-us/articles/360013779759-Using-Your-YubiKey-with-KeePass) on how to set this up.


