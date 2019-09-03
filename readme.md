# Overview
Wanted to be able to enable / disable the OpenVPN client my DD-WRT router by executing commands remotely from another machine / device.

This assumes the OpenVPN config was already done via the web UI.

`openvpn` startup command by checking the `top` command after enabling the client via the webui.

`openvpn` has no built-in stop command. Standard practice seems to be using a kill command to end the process.

Basic checks added to see if the openvpn process has actually started / stopped as expected. Very rudimentary but it works. All error checking can be stripped by removing everything after and including the first semi-colon in the command strings.

# Commands

## Start
`openvpn --config /tmp/openvpncl/openvpn.conf --route-up /tmp/openvpncl/route-up.sh --route-pre-down /tmp/openvpncl/route-down.sh --daemon; CODE=$?; sleep 2; CHECK="$(pidof openvpn)"; if [ -z "$CHECK" ]; then echo "VPN NOT started. Error code $CODE"; else echo "VPN running..."; fi`


## Stop
`killall openvpn; sleep 2; PROC_CHECK="$(pidof openvpn)"; if [ -z "$PROC_CHECK" ]; then echo "VPN Stopped"; else echo "VPN still running";  fi`


## Syncing the Web-UI with the daemon
To flip the OpenVPN Enable / Disable setting in the web ui use this.

```
nvram set openvpncl_enable="0"
nvram commit
```

Where openvpncl_enable="0" would Disable the VPN in the UI and openvpncl_enable="1" would enable it. Not this does not actualy start / stop the OpenVPN service itself. It only toggles the config / UI.

Thus the complete commands to have the GUI match the daemon state would be something like...

**EDIT:** This does not work as expected since Disabling the VPN in the UI causes the .conf file to be destroyed and it's ony recreated when the VPN is started via the UI.  Thus this would have to start from a state of having the VPN Enabled in the UI and could break at some point. More research needed.

### Start (w/sync)
```
openvpn --config /tmp/openvpncl/openvpn.conf --route-up /tmp/openvpncl/route-up.sh --route-pre-down /tmp/openvpncl/route-down.sh --daemon; CODE=$?; sleep 2; CHECK="$(pidof openvpn)"; if [ -z "$CHECK" ]; then echo "VPN NOT started. Error code $CODE"; else nvram set openvpncl_enable="1"; nvram commit; echo "VPN running..."; fi
```
