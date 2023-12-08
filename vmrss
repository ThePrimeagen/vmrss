#!/usr/bin/env bash

if [ $# -eq 0 ]; then
    echo "Usage: $0 <pid>"
    exit 1
fi

function print_vmrss() {
    declare -a arr
    arr=("$1" 0)
    total=0

    while [ ${#arr[@]} -gt 0 ]; do

        # remove last element
        space=${arr[${#arr[@]}-1]}
        unset arr[${#arr[@]}-1]
        pid=${arr[${#arr[@]}-1]}
        unset arr[${#arr[@]}-1]

        [ -d "/proc/$pid" ] || continue

        GREP_OPTS=${GREP_OPTS:-"
          --color=auto
          --exclude-dir={.bzr,CVS,.git,.hg,.svn,.idea,.tox}
          "}
        mem=$(grep $GREP_OPTS VmRSS /proc/$pid/status \
          | grep $GREP_OPTS -o '[0-9]\+' \
          | awk '{print $1/1024}')
        #Add decimals to total
        total=$(echo $mem+$total | bc)

        # name of process
        name=$(ps -p $pid -o comm=)

        printf "%${space}s%s($pid): $mem MB\n" '' "$name"

        # get children
        children=$(pgrep -P $pid)

        # add children to array
        for child in $children; do
            arr+=("$child" $((space+2)))
        done
    done
    printf "Total: $total MB\n"
}

# check VMRSS_MONITOR = 1
if [ ! -z "$VMRSS_MONITOR" ]; then
    while true; do
        if ps -p $1 > /dev/null
        then
            print_vmrss $1
            sleep 0.5
        else
            break
        fi
    done
    print_vmrss $1
else
    print_vmrss $1
fi
