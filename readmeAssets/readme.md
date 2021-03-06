# Troubleshooting Odd Developer Setup Issues

## Remote Data help if your npm start has a CORS issue.

If you need to your local copy of TeamAssist to see the data from a non-localhost service, you MAY need to launch browser in a particular way and set up. Although the CORS flag is on in the `npm start` script, we just wanted you to not get stuck here.

To execute a remote service from your web browser that is not on your local machine you will need to tell Chrome it is okay. This is basically enabling Cross-Origin Resource Sharing (CORS).

### Disable Web Security in Chrome

First, shutdown any running instance of Chrome and open a terminal window and execute the following:

### Mac

```
$ open -a Google\ Chrome --args --disable-web-security --user-data-dir
```

### Windows

```
C:\> "C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" --disable-web-security --user-data-dir
```

### Install Allow-Control-Allow-Origin Plugin

1. Install the plugin from the [Chrome Web Store](https://chrome.google.com/webstore/detail/allow-control-allow-origi/nlfbmbojpeacfghkpbjhddihlkkiljbi)

