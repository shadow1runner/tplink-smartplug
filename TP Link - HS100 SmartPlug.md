# TP-Link Smart Plug HS100(EU) Hacking

## Links

* https://github.com/softScheck/tplink-smartplug
* https://github.com/softScheck/tplink-smartplug/blob/master/tplink-smarthome-commands.txt
* https://www.softscheck.com/en/reverse-engineering-tp-link-hs110/#TP-Link%20Smart%20Home%20Protocol

## Disabling cloud connection altogether

### Get OEM configured settings

$ ./tplink_smartplug.py -t 10.0.0.21 -j '{"cnCloud":{"get_info":null}}'  

Sent:

```json
{"cnCloud":{"get_info":null}}
```

Received:

```json
{
    "cnCloud": {
        "get_info": {
            "username": "#removed#",
            "server": "n-devs.tplinkcloud.com",
            "binded": 1,
            "cld_connection": 1,
            "illegalType": 0,
            "tcspStatus": 1,
            "fwDlPage": "",
            "tcspInfo": "",
            "stopConnect": 0,
            "fwNotifyType": 0,
            "err_code": 0
        }
    }
}
```

where `"server": "n-devs.tplinkcloud.com"` is the original value

### Disable cloud connection altogether: Set Server URL to fake uri; needs a reboot -> no more cloud stuff going on local control (TCP :9999) and kasa app will still work

$ ./tplink_smartplug.py -t 10.0.0.21 -j '{"cnCloud":{"set_server_url":{"server":"local.local"}}}'
$ ./tplink_smartplug.py -t 10.0.0.21 -j '{"system":{"reboot":{"delay":1}}}'

Interestingly though, it still shows the original server afterwards, even though cloud connection does _not_ work anymore

$ ./tplink_smartplug.py -t 10.0.0.21 -j '{"cnCloud":{"get_info":null}}'

Sent:

```json
{"cnCloud":{"get_info":null}}
```

Received:  

```json
{
    "cnCloud": {
        "get_info": {
            "username": "#removed#",
            "server": "n-devs.tplinkcloud.com",
            "binded": 1,
            "cld_connection": 1,
            "illegalType": 0,
            "tcspStatus": 1,
            "fwDlPage": "",
            "tcspInfo": "",
            "stopConnect": 0,
            "fwNotifyType": 0,
            "err_code": 0
        }
    }
}
```

## Re-Enabling cloud connection altogether

### Reset Server URL to original value (for Alexa); needs a reboot

$ ./tplink_smartplug.py -t 10.0.0.21 -j '{"cnCloud":{"set_server_url":{"server":"n-devs.tplinkcloud.com"}}}'
$ ./tplink_smartplug.py -t 10.0.0.21 -j '{"system":{"reboot":{"delay":1}}}'
