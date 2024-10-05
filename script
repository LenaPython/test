#!/bin/bash

SUPERUSER=root
OCTET=({0..255})

username=$(id -nu)
if [ "$username" != "$SUPERUSER" ]; then
    echo "Must have root access"
    exit 1
fi

trap 'echo "Ping exit (Ctrl-C)"; exit 1' 2

IP_ADDR="$1"
INTERFACE="$2"

if [[ -z "$IP_ADDR" ]]; then
    echo "\$IP_ADDR must be passed as first positional argument" >&2
    exit 1;
fi

if [[ -z "$INTERFACE" ]]; then
    echo "\$INTERFACE must be passed as second positional argument" >&2
    exit 1
fi

ip='^((25[0-5]|(2[0-4]|1[0-9]|[1-9]|)[0-9])\.?\b){2,4}$'

if ! [[ $IP_ADDR =~ $ip ]]; then
    echo "Wrong format" >&2
    exit 1
fi

inteface_array=$(ls /sys/class/net)
if [[ ! $inteface_array =~ $INTERFACE ]]; then
    echo "Interface doesn't exists" >&2
    exit 1
fi

PREFIX=$(cut -s -d. -f1,2 <<< "$IP")

tmp="$(cut -s -d. -f3 <<< "$IP_ADDR")"
SUBNET="${tmp:-${OCTET[@]}}"

tmp="$(cut -s -d. -f4 <<< "$IP_ADDR")"
HOST="${tmp:-${OCTET[@]}}"

for SUBNET in $SUBNET; do
    for HOST in $HOST; do
        echo "[*] IP : ${PREFIX}.${SUBNET}.${HOST}"
        arping -c 3 -i "$INTERFACE" "${PREFIX}.${SUBNET}.${HOST}" 2> /dev/null

    done
done
