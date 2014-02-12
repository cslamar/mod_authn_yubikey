# Installation and Build Instructions

### Requirements
* httpd-devel
* curl-devel

```
apxs \
-DYK_PACKAGE=\\\"mod_authn_yubikey\\\" \
-DYK_PACKAGE_VERSION=\\\"0.1\\\" -I. -Wc -c -lcurl \
mod_authn_yubikey.c libykclient.c libykclient.slo mod_authn_yubikey.slo \
&& su -c "apxs -i mod_authn_yubikey.la"
```

#### Sample Conf
/etc/httpd/conf.d/yubi.conf
```
LoadFile /usr/lib64/libcurl.so.4
LoadModule authn_yubikey_module modules/mod_authn_yubikey.so

AuthYubiKeyRequireSecure Off

<Location />
	AuthType Basic
	AuthBasicProvider yubikey
	AuthName “Login"
	AuthYubiKeyTimeout 30
	AuthYubiKeyTmpFile /var/www/tmp/ykTmpDb
	AuthYubiKeyUserFile /var/www/ykUserDb
	#AuthYubiKeyRequireSecure On
	AuthYubiKeyRequireSecure Off
	AuthYubiKeyExternalErrorPage Off
	Require valid-user
</Location>
```
* AuthYubiKeyTmpFile - A file that is in a directory that writeable by apache
* AuthYubiKeyUserFile - The basic auth file that contians the user and password info

#### Generating the AuthYubiKeyUserFile file
```
htpasswd -csb <AuthYubiKeyUserFile> <first_12_digits_of_the_yubikey> <username>:<password>
```

#### Example of the `htpasswd` comand
```
htpasswd -csb /var/www/ykUserDb asdfqwerzxcv cslamar:superSecretPassword
```
