#!/bin/bash

# setup config file and table names
config_file="$HOME/.$(basename "${0}").conf"
table_name1="actions"
table_name2="errors"
table_name3="objects"
table_name4="relationships"
runtype="run"

# report function might be useful to be consistent with other apps
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

# define how to connect to the database
_connect_db(){
    mysql -h ${host_name} -u ${user_name} -p${password} -D ${database} --reconnect
}

# read configuration file or create one
if [[ -f "${config_file}" ]] ; then
    . "${config_file}"
else
    echo -e "No configuration file. Restarting."
    touch "${config_file}"
    $(basename "${0}") -e
fi

# option for resetting config file
if [[ "${runtype}" = "reset" ]] ; then
 report -q -n "Reseting the configuration will clear ${config_file}. Please enter [Y] to confirm: "
  read reset_response
   if [[ "${reset_response}" = "Y" || "${reset_response}" = "y" ]] ; then
    report -d "Clearing ${config_file}."
     echo -n "" > "${config_file}"
      runtype="edit"
    else
       report -d "Reset aborted. Exiting."
    exit 0
    fi
fi

# Ask about username, password, database settings
if [ -z "${host_name}" ] ; then
    report -q -n "Enter the host location for the MySQL server: "
    read host_name
fi
if [ -z "${user_name}" ] ; then
    report -q -n "Enter the user name for the MySQL server: "
    read user_name
fi

if [ -z "${password}" ] ; then
    report -q -n "Enter the password for this user on the MySQL server: "
    read password
fi
if [ -z "${database}" ] ; then
    report -q -n "Enter the of the database you want to use on MySQL server: "
    read database
fi

# write to configuration file
        echo "#$(basename "${0}") config file" > "${config_file}"
        echo "${host_name}" >> "${config_file}"
        echo "${user_name}" >> "${config_file}"
        echo "${password}" >> "${config_file}"
        echo "${database}" >> "${config_file}"
        
# give user a summary of what they've chosen
        report -d "Summary: user name: ${user_name}, password: ${password}, database: ${database}"
report -q "Press enter if settings look correct"
read

#Check to see if MySQL server connection is working
_test_sql(){
db_errors=$(mysql -h ${host_name} -u ${user_name} -p${password} 2>&1)
if [[ "${db_errors}" = *"1045"* ]] ; then
echo "Error connecting with MySQL server. Sorry."
set db_errors=1
else
echo "Connection to MySQL server seems to be ok."
fi
}

#run the SQL test
_test_sql

#Check to see if tables have already been created, if not, create them
_create_tables(){
table_errors=$(mysql -h ${host_name} -u ${user_name} -p${password} -D ${database} -e "
DESCRIBE ${table_name1};" 2>&1)
if [[ "${table_errors}" = *"1146"* ]] ; then
echo "Tables have not been set up" 
mysql -h ${host_name} -u ${user_name} -p${password} -D ${database} -e "CREATE TABLE ${table_name1} (action_type varchar(30) NOT NULL, job_id MEDIUMINT(8) NOT NULL AUTO_INCREMENT, technician varchar(128) NOT NULL,  computer_name varchar(128) NOT NULL, time_started DATETIME NOT NULL, time_ended DATETIME NOT NULL, PRIMARY KEY (job_id));
CREATE TABLE ${table_name2} (job_id MEDIUMINT(8) NOT NULL, timestamp DATETIME, error_text VARCHAR(8000), PRIMARY KEY (job_id));
CREATE TABLE ${table_name3} (digitization_status VARCHAR(50) NOT NULL, item_id VARCHAR(128) NOT NULL, qc_status VARCHAR(50) NULL, object_type VARCHAR(50) NOT NULL, file_path VARCHAR(256) NULL, media_id VARCHAR(20) NULL, title VARCHAR(256) NULL, series VARCHAR(256), PRIMARY KEY (item_id));
CREATE TABLE ${table_name4} (item_id VARCHAR(128) NOT NULL, relationship_type VARCHAR(128) NOT NULL, PRIMARY KEY (item_id));"
    echo "Table set up complete!"
elif [[ "${db_errors}" -ne "1" ]] ; then
    echo "Tables are already set up! B-)"
else 
    echo "You've got database problems. :'("
fi 
}

_create_tables
#create a new record when the user starts the digitization (pulls unique ID from vrecord, recording directory, and technician name)

#reads output of vrecord, looks for/parses error messages

#changes digitization_status in table to correct status (digitized w/ errors or w/o errors)

#changes QC status to "awaiting QC"

#Now, onto the QC script...
