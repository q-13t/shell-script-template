```sh
#!/bin/bash

# set IFS
OIFS="$IFS"
IFS=$'\n'

function print_help {
    echo "Usage: ${0} [-h | --help] [-v | --verbose]"
    echo ""
    echo "Example: ${0} --help"
    echo ""
    echo "Flags:"
    echo "  -h | --help:        print this help message and exit"
    echo "  -v | --verbose:     print verbose output"
}

# single letter options followed by a colon for argument (no colon for no argument)
OPTIONS=rvclhs:f:d:
# multi letter options followed by a colon for argument (no colon for no argument)
LONGOPTIONS=search:,remove,verbose,check,list,help,file:,dir: 

PARSED=$(getopt --options=$OPTIONS --longoptions=$LONGOPTIONS --name "$0" -- "$@")
eval set -- "$PARSED"

# parse options and set variables
while true; do
    case "$1" in
        -v | --verbose)
        verbose=1
        shift
        ;;
        -h | --help)
            print_help
            exit 0
            ;;
        --)
            shift
            break
            ;;
        *)
            echo "Invalid option: $1" >&2
            echo "Run ${0} -h for help"
            exit 1
            ;;
    esac
done

duration=$(ls ./ | wc -l ) # expects a number 
done=1 # expects a number

# prints current progress
function already_done {
    echo -ne "Progress: [ "
    for _ in $(seq $done); do 
        echo -ne "▇"
    done
}

# fills remaining spaces
function remaining {
    for _ in $(seq $(( $duration - $done ))); do 
        echo -ne " "
    done
}

# prints percentage
function percentage {
    echo -ne "| $(( $done * 100 / $duration ))% ]" 
}

# clean line
function clean_line {
    echo -ne "\r"
}

# funciotn with proggress bar 
for file in $(ls ./); do
    if [ $verbose ]; then
        already_done; remaining; percentage;
    fi
   
    # print using -ne flag
    echo -ne "\t$1\n"

    done=$((done + 1))
    if [[ $done -lt $duration && $verbose ]]; then
        clean_line
    fi
done

# reset IFS
IFS="$OIFS"

exit 0
```

# Sample usage:
```cli
$ ./tmp.sh -h
Usage: ./tmp.sh [-h | --help] [-v | --verbose]

Example: ./tmp.sh --help

Flags:
  -h | --help:        print this help message and exit
  -v | --verbose:     print verbose output

```
