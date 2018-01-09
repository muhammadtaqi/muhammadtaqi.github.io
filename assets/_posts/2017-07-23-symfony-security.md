---
layout: post
title: "Understanding the Symfony Security Component"
date: 2017-07-23
categories: symfony
comments: true
published: true
---

## Authentication Process
Being authenticated simply means that the Symfony application is, at least to some degree, aware of who is currently using it. (A specific user? An anonymous user? An api client?).

1. You submit credentials and Firewall is triggered, initializing security process.
2. Authentication listeners try to perform authentication using the current Request object (The Providers find the user and encoder helps in encrypt/decrypt of the credentials).
3. Assuming a listener successfully authenticated a token (not a user, sometimes an import distinction), the authentication process is now over.

![Authentication process]({{ site.raw_static_url }}/symfony-security/001.jpg)

a. Users

First we need to stores the users and information need to login. So, you will need create your own user class in your Symfony app in `Namespace/Bundle/Entity/User.php` that must implement `AdvancedUserInterface` or `UserInterface` from namespace `Symfony\Component\Security\Core\User`

![Authentication process]({{ site.raw_static_url }}/symfony-security/002.jpg)

b. Providers

* A class that takes a “username” and loads the information for that user, returns a user object for that username.
* Might be from database
* Might be from filesystem
* Might be from an external user directory like ldap, maybe something like OAuth, or some other external service that provides information about users.
![Authentication process]({{ site.raw_static_url }}/symfony-security/003.jpg)
![Authentication process]({{ site.raw_static_url }}/symfony-security/004.jpg)

c. Firewall

* During every request a call to `Symfony\Component\Security\Http\Firewall::onKernelRequest`. It will check IF authentication should occur or not. If authentication is supposed to occur the firewall will then proceed with the authentication process.

* This is done by listeners registered with the firewall. Authentication occurs when one of the AuthenticationListeners on the firewall actually decides to perform authentication. If none of the authentication listeners attempt to actually perform authentication, then no authentication occurs because none was required for this request. If one of the authentication listeners attempts to perform authentication and it succeeds then the firewall’s job is done and it skips the rest of its listeners.

* Firewall Listener Interface: `Symfony\Component\Security\Http\Firewall\ListenerInterface`, Authentication Listener Base Class: `Symfony\Component\Security\Http\Firewall\AbstractAuthenticationListener`

* If one of the authentication listeners attempts to perform authentication and it fails, no more authentication attempts will occur. Whichever authentication failure handler is configured to handle failure for that listener will take over. Usually this means there is a bug in the application or that the credentials on the request were incomplete/incorrect.

* The end result of a successful authentication should be an authentication.


d. Encoder

- **UserPasswordEncoder**
  What their name says they are. Just a class which knows how to encode/hash/encrypt the password of a specific User type.


## Authorization
