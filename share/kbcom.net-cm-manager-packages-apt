#!/bin/bash

# From config
#CONFIG_CM_MANAGER_SERVER

GLOBAL_FOLDER_BASE=`mktemp -d`

GLOBAL_FILE_PACKAGES="$GLOBAL_FOLDER_BASE/packages"
GLOBAL_FILE_FILES="$GLOBAL_FOLDER_BASE/files"
GLOBAL_FILE_SERVICES="$GLOBAL_FOLDER_BASE/services"

GLOBAL_FILE_OUTPUT="$GLOBAL_FOLDER_BASE/output"
GLOBAL_RESULT_STATUS="UNCHANGED"

print_error()
{
 echo "ERROR"
 echo "$GLOBAL_RESULT_STATUS"
 echo
 echo "Output:"
 cat "$GLOBAL_FILE_OUTPUT"
 exit 1
}

print_result()
{
 echo "$GLOBAL_RESULT_STATUS"
 echo
 echo "Output:"
 cat "$GLOBAL_FILE_OUTPUT"
 exit 0
}

apt_result()
{
 local LOCAL_RESULT

 if [ "$1" != "0" ]
 then
  print_error
 fi

 LOCAL_RESULT=`cat "$GLOBAL_FILE_OUTPUT" | tail -n 1 | cut -f 1 -d ' '`

 if [ "$LOCAL_RESULT" != "0" ]
 then
  GLOBAL_RESULT_STATUS="CHANGED"
 fi

 LOCAL_RESULT=`cat "$GLOBAL_FILE_OUTPUT" | tail -n 1 | cut -f 3 -d ' '`

 if [ "$LOCAL_RESULT" != "0" ]
 then
  GLOBAL_RESULT_STATUS="CHANGED"
 fi
}


curl -k --negotiate -u : $CONFIG_CM_MANAGER_SERVER > "$GLOBAL_FILE_PACKAGES"

if [ ! -f "$GLOBAL_FILE_PACKAGES" ]
then
 echo "$GLOBAL_FILE_PACKAGES does not exists." >> "$GLOBAL_FILE_OUTPUT"
 print_error
fi

GLOBAL_PACKAGES_STATUS=`sed -n '1{p;q;}' "$GLOBAL_FILE_PACKAGES"`

if [ "$GLOBAL_PACKAGES_STATUS" != "OK" ]
then
 echo "kbcom-cm: packages is not OK." >> "$GLOBAL_FILE_OUTPUT"
 print_error
fi

GLOBAL_PACKAGES_PURGE=`sed -n '2{p;q;}' "$GLOBAL_FILE_PACKAGES"`
GLOBAL_PACKAGES_INSTALL=`sed -n '3{p;q;}' "$GLOBAL_FILE_PACKAGES"`

echo "apt-get -qq -y purge $GLOBAL_PACKAGES_PURGE" >> "$GLOBAL_FILE_OUTPUT"
apt-get -qq -y purge $GLOBAL_PACKAGES_PURGE >> "$GLOBAL_FILE_OUTPUT" 2>&1
apt_result "$?"

echo "DEBIAN_FRONTEND=noninteractive apt-get -qq -y install $GLOBAL_PACKAGES_PURGE" >> "$GLOBAL_FILE_OUTPUT"
DEBIAN_FRONTEND=noninteractive apt-get -qq -y install $GLOBAL_PACKAGES_INSTALL >> "$GLOBAL_FILE_OUTPUT" 2>&1
apt_result "$?"

print_result
