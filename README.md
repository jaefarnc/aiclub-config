# aiclub-config

Walks through the process of setting up ssh into the AI-Club Machine from outside the d-link network  

Before Anything else, tmux and start your container. Normal Port Maps are fine.

## Tor Setup
    1. # get the tor-expert-bundle and store it in folder B220032CS_Jaefar, edit torrc with a webtunnel bridge
    2. apt update && sudo apt upgrade
    3. apt-get install libevent-2.1-7 libssl3 zlib1g liblzma5 libzstd1
    4. Setup go and web tunnel
        a. go
            - cd B220032CS_Jaefar
            - wget https://go.dev/dl/go1.20.5.linux-amd64.tar.gz ( latest go )
            - tar -C /usr/local -xzf go1.20.5.linux-amd64.tar.gz
            - export PATH=$PATH:/usr/local/go/bin ( paste in ~/.profile and ~/.bashrc )
            - source ~/.bashrc && source ~/.profile ( reload shell )
            - go version ( to check if installed )
        b. webtunnel
            - cd B220032CS_Jaefar
            - git clone --single-branch --depth 1 https://gitlab.torproject.org/tpo/anti-censorship/pluggable-transports/webtunnel.git
            - cd webtunnel/main/client 
            - WEBTUNNEL_VERSION = 3b6faa48163782c1e5420bcb4b068cd38c401ea7
            - git reset --hard ${WEBTUNNEL_VERSION} && go build -ldflags="-s -w" -o /usr/local/bin/webtunnel
    5. cd /workspace/B220032CS_Jaefar/tor-expert-bundle/tor
    6. ./tor -f torrc
## Autossh
    1. apt install autossh openssh-client openssh-server netcat-openbsd
    2. nano ~/.ssh/config
        - # Add the VPS configs. Make sure the ssh port is 2222, and netcat is configured
    3. nano /etc/ssh/sshd_config
        - PermitRootLogin yes
        - Port 2222
    4. service ssh restart
    5. passwd root
    6. autossh test0-azvm-reverse
## Automate Keeping tor and autossh alive
    1. cd /workspace/B220032CS_Jaefar && git clone https://github.com/jaefarnc/scraping-twitter.git
    2. cp scraping-twitter/extssh.sh ./
    3. rm -r scraping-twitter
    4. chmod +x extssh.sh
    5. nano extssh.sh
        - change working directory to /workspace/B220032CS_Jaefar/tor-expert-bundle/tor
        - change autossh test0-azvm to autossh test0-azvm-reverse
    6. ./extssh.sh
    

## Getting access to the ai club machine from your network
    1. ssh test0-azvm ( ssh into your vps )
    2. ssh -p 6000 root@localhost  
    Voila, you're good to go.




