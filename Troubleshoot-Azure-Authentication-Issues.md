This page documents known issues with Azure Authentication with possible fixes/workarounds as available.

## SSL Error on localhost page (ERR_SSL_PROTOCOL_ERROR)

When using the `Code Grant` method users may see an SSL error when trying to log into their account. This flow opens up an external web page to `localhost` which then normally prompts the user to log in via the standard Microsoft authentication prompts. The URL for this page will look something like this `http://localhost:50055/signin?nonce=...`

Some browsers may be set up to automatically redirect all `http` links to `https`, which will break this process as the local server serving the web page does not support https. If the link in the address bar starts with `https` then you will get an SSL error and the page will not load. In that case here are some workarounds to fix the issue:

### Change URL to http

First you can try just manually changing the URL from `https://...` to `http://...`. The browser may change it back to `https` though, in which case you will need to try one of the other options

### Disable HSTS (HTTP Strict Transport Security)

For Edge/Chrome browsers you can disable [HSTS](https://www.chromium.org/hsts/) for localhost by:

1. Open Edge/Chrome and in the address bar type `edge://net-internals/#hsts` (or `chrome://net-internals/#hsts` for Chrome)
2. Scroll to the bottom of the page and in the `Delete domain security policies` section enter `localhost` and press `Delete`

Once that is done you should be able to log in and not have the browser redirect your `localhost` links automatically to `https`

## URLs to whitelist:
A handful of features within ADS require network communication to work, such as adding an azure account. For this to work properly in a proxy environment, you must have the product correctly configured. 

If you are behind a firewall that needs to allow specific domains used by ADS, here's the list of hostnames you should allow communication to go through: 

- https://management.azure.com 
- https://login.microsoftonline.com 

The URLs to whitelist can sometimes vary on a case-by-case basis.  In order to verify you aren’t blocking any URLs from going through, go to Help > Toggle Developer Tools and select the Network tab.  Here you will see any URLs that are getting blocked that you may need to whitelist to successfully add your account. 

### Use the Device Code method

You can also use the Device Code method to log in. This will provide you with a code and a URL to enter which can then be used to login.

1. Open Azure Data Studio and open the Settings page (`CTRL+,` or run the `Open Settings (UI)` command)
2. Type `Azure auth` into the search bar 
3. Unselect the `Auth: Code Grant` box
4. Select the `Auth: Device Code` box
5. Attempt to log in again

### Unable to add Account / Token Expired

1. Select the account icon in the bottom left
2. Remove azure account
3. Add azure account using the connection pane