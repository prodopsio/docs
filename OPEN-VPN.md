# Open VPN Operation instructions
![OpenVPN](https://pngimage.net/wp-content/uploads/2018/06/openvpn-png-8.png)


So you have your OpenVPN server ready to work with, in this doc we'll go through what's required for authentication, security and general maintenance.

## Initial Setup
* Initial configuration is done by SSHing into the host: `ssh -i <key> openvpnas@<IP>` and going through the [setup wizard](https://openvpn.net/vpn-server-resources/amazon-web-services-ec2-tiered-appliance-quick-start-guide/)
* If you kept all the defaults, your admin user should be `openvpn` and the password can be set with `sudo passwd openvpn` on the host
* When the wizard is complete the server should be up and running, the admin panel should be accessible at `<IP>:943`
* Once you have a user configuration you'll be able to connect and access the private resources through the VPN


## Creating a user
1. Go to <IP>:943 and login with the admin credentials created in the initial setup
1. Under **User Management** click `User Permissions`
1. Start typing a new username on the next available space under **Username** column
1. Tick relevant permissions (**admin** - for administration access, and **Allow Auto-login** for quick client access - recommended)
1. Under **More Settings** set a password for the user
1. Save the settings and click **Update Running Server** for the configuration to take place immediately
1. Download your configuration from the server by logging in as the newly created user at `https://<IP>` and downloading a profile from the options below. (If you previously enabled auto-login for the user, you should be able to download an `autologin` profile.


## Connecting to the server
1. Install an OpenVPN compatible client like [OpenVPN](https://openvpn.net/community-downloads/) or [TunnelBlick](https://tunnelblick.net/).
   I personally prefer to `brew install openvpn` (on macOS) which installs the CLI version of OpenVPN. (OpenVPN connect is a GUI option available also for Android, IOS, etc).
1. Once the client is installed you need to load your downloaded profile into it. If you're using the CLI version of open vpn, we'll be using it directly from the command line
1. try `openvpn` in your console, you may need to `export PATH=$(brew --prefix openvpn)/sbin:$PATH` if it was installed with brew
1. Connect by running `sudo openvpn <file.ovpn>` (send to background by `CTRL+z` then `bg %1`, bring back to forground with `fg`)
* Using TunnelBlick or OpenVPN-Connect is more self-explanatory throught he applications' GUI so I'll keep this section brief


## Test your VPN connection
* If everything worked you should be accessible to you private resources
* Try SSHing into one of them or check the open port with either `telnet <privateIP> 22` or `nc -vv <priavetIP> 22`


## Setting SSL
It's preferable to set an SSL certificate for your VPN server to encrypt HTTP communication.
OpenVPN sets his own certificate by default which most browsers tend to label as *Untrested*
1. Generate a certificate e.g with [lets encrypt](https://letsencrypt.org/)
1. Go to your VPN server admin page at `https://<IP>:943/admin` and select webserver in the menu
1. You'll see the details of the current default certificate, replace it by uploading your new certificate in the bottom section

## Security / Access control
It's recommeneded that you keep your VPN safe and monitored as this is the one gateway separating connections from the outsideworld directly to private resources (other than probably loadbalancers)
* Keep ports `22` & `943` only accessible to a specific IP (if at all) as you don't normally need them open.
* Ports `443` & `1194` are used for client's access. If you know a specific IP range and addresses it's good to lock these also down to where they are required rather than `0.0.0.0/0`
* Make sure all users have passwords and only relevant personel have admin settings allowed
* MFA is recommended, here are a few ways: 1. [DUO](https://duo.com/docs/openvpn), 2. [Authy](https://www.authy.com/integrations/openvpn/), 3. [manual](https://medium.com/we-have-all-been-there/using-google-authenticator-mfa-with-openvpn-on-ubuntu-16-04-774e4acc2852)


## Purchasing a license
By deafult OpenVPN gives you two concurrent access slots, so if a third user required a login while two others are having open connections, the third will be rejected.
Purchasing and installing a certificate is a very easy process. [ Pricing ](https://openvpn.net/pricing/) is around **$15 per user per year**.


