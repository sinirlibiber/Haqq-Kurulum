#!/bin/bash
GREEN='\033[0;32m'
RED='\033[0;31m'
NC='\033[0m' # No Color
PASWD='PW'
DELAY=300 #Delay time in seconds
ACC_NAME=walletname
NODE=http://localhost:26657
CHAIN=haqq_54211-2
PROJECT=haqqd
TOKEN_NAME=aISLM
for (( ;; )); do
        JAIL=$(${PROJECT} q staking validator $( echo "${PASWD}" | ${PROJECT} keys show ${ACC_NAME} --bech val -a) | grep jailed:);
        if [[ ${JAIL} == *"false"* ]]; then
            echo -e "${GREEN}${JAIL} \n"
        else
            echo -e "${GREEN}${JAIL} \n"
            echo -e $( echo "${PASWD}" | ${PROJECT} tx slashing unjail --chain-id ${CHAIN} --from ${ACC_NAME} --gas=auto --fees=1000${TOKEN_NAME} -y) \n;
            sleep 1
        fi
        for (( timer=${DELAY}; timer>0; timer-- ))
        do
                printf "* sleep for ${RED}%02d${NC} sec\r" $timer
                sleep 1
        done
done
