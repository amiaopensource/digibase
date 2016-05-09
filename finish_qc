#!/bin/bash

#report function might be useful to be consistent with other apps
report(){
        local RED="$(tput setaf 1)"   # Red      - For Warnings
        local GREEN="$(tput setaf 2)" # Green    - For Declarations
        local BLUE="$(tput setaf 4)"  # Blue     - For Questions
        local NC="$(tput sgr0)"       # No Color
        local color=""
        local startmessage=""
        local endmessage=""
        local echoopt=""
        local log_message=""
        OPTIND=1
        while getopts ":qdwstn" opt; do
            case "${opt}" in
                q) color="${BLUE}" ;; # question mode, use color blue
                d) color="${GREEN}" ;; # declaration mode, use color green
                w) color="${RED}" ; log_message="Y";; # warning mode, use color red
                s) startmessage+=([$(basename "${0}")] ) ;; # prepend scriptname to the message
                t) startmessage+=($(_get_iso8601) '- ' ) ;; # prepend timestamp to the message
                n) echoopt="-n" ;; # to avoid line breaks after echo
            esac
        done
        shift $(( ${OPTIND} - 1 ))
        message="${1}"
        echo $echoopt "${color}${startmessage[@]}${message}${NC}"
}

report -q -n "Did the video file for ${id} pass QC? (Y or N) "
read qc_answer

if [[ "${qc_answer}" = "y" ]] || [[ "${qc_answer}" = "Y" ]]; then
    mysql -h ${host_name} -u ${user_name} -p${password} << EOF 
USE ${database};
INSERT INTO ${table_name3} (qc_status), VALUES(passed qc)
EOF
fi

if [[ "${qc_answer}" = "n" ]] || [[ "${qc_answer}" = "N" ]]; then
    mysql -h ${host_name} -u ${user_name} -p${password} << EOF 
USE ${database};
INSERT INTO ${table_name3} (qc_status), VALUES(failed qc)
fi

echo "QC status changed."

