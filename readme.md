# Overview
Wanted to be able to enable / disable the OpenVPN client my DD-WRT router by executing commands remotely from another machine / device.

This assumes the OpenVPN config was already done via the web UI.

`openvpn` startup command by checking the `top` command after enabling the client via the webui.

`openvpn` has no built-in stop command. Standard practice seems to be using a kill command to end the process.

Basic checks added to see if the openvpn process has actually started / stopped as expected. Very rudimentary but it works.

# Start
`openvpn --config /tmp/openvpncl/openvpn.conf --route-up /tmp/openvpncl/route-up.sh --route-pre-down /tmp/openvpncl/route-down.sh --daemon; CODE=$?; sleep 2; CHECK="$(pidof openvpn)"; if [ -z "$CHECK" ]; then echo "VPN NOT started. Error code $CODE"; else echo "VPN running..."; fi`


# Stop
`killall openvpn; sleep 2; PROC_CHECK="$(pidof openvpn)"; if [ -z "$PROC_CHECK" ]; then echo "VPN Stopped"; else echo "VPN still running";  fi`
