# Overview

This ansible role is used to deploy a mail stack that is composed of several open
source components: postfix, dovecot, sieve, opendkim, opendmarc and spf.

## Postfix
Postfix is an open source mail transfer agent (MTA). It handles receiving mail
sent to a user at a particular domain and also helps with mail delivery by
providing SMTP support.

In this project, Postfix has been configured to only allow mail to be sent by
authenticated users over TLS using SASL. It supports TLS but does not require it
for mail being received from other servers. This is to increase the likelihood of
mail delivery.

User accounts are searched for in an LDAP server. Postfix has been configured to
support virtual domains, so it is possible in the future to enhance this project
to support multiple domains, in addition to multiple users.

All outgoing messages are signed with a DKIM key. See the opendkim section below
for more details.

Postfix is published under the IBM License v1.0. More information is available
at http://www.postfix.org

## Dovecot
Dovecot is an open source IMAP and POP3 email server. In this project, Dovecot
has been configured to only provide IMAP access over a TLS connection.

Dovecot has been configured to use the maildir format. Authentication is only
allowed over TLS using SASL. There is support for the '+' separator in email
addresses to allow for unlimited dynamic email addresses (e.g.: john+comast@j.com
is routed to john@j.com).

Maximum mail attachment size has been set to 50mb. The postmaster@domain.com
account has been set as an alias to the initial account that is created. All
account information comes from the specified LDAP server.

Portions of Dovecot are published under the MIT license and other portions of
Dovecot are published under the LGPLv2.1 license. More information is available
at http://dovecot.org/doc/COPYING.

## Sieve
Sieve is a scripting language for server-side mail filtering. It can be used to
automatically move messages to specific folders. A good use of Sieve is to move
suspected spam messages to the Junk folder to eliminate clutter in the inbox.

Currently there is no configuration interface for sieve scripts and a default
script is provided.

## OpenDKIM
DKIM is a cryptographic measure for validating a domain name identity associated
with an email. This project is configured to sign outgoing messages and publish
the corresponding public key in DNS for receiving servers to verify the authenticity
of the message.

More information about DKIM is available at http://www.dkim.org and more
information about the OpenDKIM project is available at http://www.opendkim.org.
License information for OpenDKIM is available at http://www.opendkim.org/license.html.

## SPF
SPF is a framework for publishing which IP addresses are allowed to send email
for a particular domain. A strict policy has been defined that ensures that only
your IP is an accepted sender for messages for your domain. Similarly, incoming
messages are checked for SPF headers. At this time, messages are not rejected
for failing SPF checks but mail headers are added to indicate failures.

This project uses postfix-policyd-spf-python for SPF validation. More information
about it can be found at https://launchpad.net/pypolicyd-spf/. The project is
licensed under Apache v2.0 and GNU GPL v2.

## OpenDMARC
OpenDMARC is a framework for binding the domain name of your mail server with the
domain name verified by SPF and DKIM. It also specifies how receivers of mail from
your domain should enforce message authentication and provides comprehensive
reporting.

More information about DMARC is available at https://dmarc.org and more information
about the OpenDMARC project is available at http://www.trusteddomain.org/opendmarc.html.
License information for OpenDMARC is similar to OpenDKIM.

## Rspamd
Rspamd is a spam filtering system. We have not yet tuned it to learn good email
(ham) from bad (spam) but this will come in a future release.

More information about Rspamd is available at https://rspamd.com and more
information about the license for Rspamd is available at
https://github.com/vstakhov/rspamd/blob/master/LICENSE.

# Dependencies
The mail role is dependent on the openldap role and the letsencrypt roles. It is
also dependent on the tunnel role for port forwarding and the provision role for
Diffie Helman keys for Postfix as well as key generation for opendkim. This role
also relies on the provisioning role for the proper creation of MX, SPF, DKIM and
DMARC records, all of which are essential for mail delivery and receipt.

# Variables
```yaml
- vars:
  domain: domain.com
  tls_cert_file_location: /etc/letsencrypt/live/domain.com/fullchain.pem
  tls_key_file_location: /etc/letsencrypt/live/domain.com/privkey.pem
  ldap_ip: 127.0.0.1
  ldap_service_searchbase: ou=services,dc=domain,dc=com
  ldap_user_searchbase: ou=people,dc=domain,dc=com
  ldapadminpassword: change_this_password
```
