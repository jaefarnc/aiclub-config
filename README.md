# aiclub-config

Walks through the process of setting up ssh into the AI-Club Machine from outside the d-link network  

Before Anything else, tmux and start your container. Normal Port Maps are fine.  

    WORK_DIR_T = /workspace/B220032CS_Jaefar  
    RSHELL_HOST_CONFIG = test0-azvm-reverse 
    STD_HOST_CONFIG = test0-azvm
    # The host configs are to be written into ~/.ssh/config
    # For example,
    Host $RSHELL_HOST_CONFIG
        
## Tor Setup
    0. tmux
    1. # get the tor-expert-bundle and store it in folder $WORK_DIR, edit torrc with a webtunnel bridge
    2. apt update && sudo apt upgrade
    3. apt-get install libevent-2.1-7 libssl3 zlib1g liblzma5 libzstd1
    4. Setup go and web tunnel
        a. go
            - cd $WORK_DIR_T
            - wget https://go.dev/dl/go1.20.5.linux-amd64.tar.gz ( latest go )
            - tar -C /usr/local -xzf go1.20.5.linux-amd64.tar.gz
            - export PATH=$PATH:/usr/local/go/bin ( paste in ~/.profile and ~/.bashrc )
            - source ~/.bashrc && source ~/.profile ( reload shell )
            - go version ( to check if installed )
        b. webtunnel
            - cd $WORK_DIR_T
            - git clone --single-branch --depth 1 https://gitlab.torproject.org/tpo/anti-censorship/pluggable-transports/webtunnel.git
            - cd webtunnel/main/client 
            - WEBTUNNEL_VERSION = 3b6faa48163782c1e5420bcb4b068cd38c401ea7
            - git reset --hard ${WEBTUNNEL_VERSION} && go build -ldflags="-s -w" -o /usr/local/bin/webtunnel
    5. cd $WORK_DIR_T/tor-expert-bundle/tor
    6. ./tor -f torrc
## Autossh
    0. tmux
    1. apt install autossh openssh-client openssh-server netcat-openbsd
    2. nano ~/.ssh/config
        - # Add the VPS configs. Make sure the ssh port is 2222, and netcat is configured
    3. nano /etc/ssh/sshd_config
        - PermitRootLogin yes
        - Port 2222
    4. service ssh restart
    5. passwd root
    6. autossh $RSHELL_HOST_CONFIG
## Automate Keeping tor and autossh alive ( optional )
    0. # close tmux session 1 and tmux attach-session -t 0
    1. cd $WORK_DIR_T && git clone https://github.com/jaefarnc/scraping-twitter.git
    2. cp scraping-twitter/extssh.sh ./
    3. rm -r scraping-twitter
    4. chmod +x extssh.sh
    5. nano extssh.sh
        - change working directory to $WORK_DIR_T/tor-expert-bundle/tor
        - change autossh test0-azvm to autossh $RSHELL_HOST_CONFIG
    6. ./extssh.sh
    7. CTRL+b+d
## Getting access to the ai club machine from your network
    1. ssh $STD_HOST_CONFIG ( ssh into your vps )
    2. ssh -p 6000 root@localhost  
    Voila, you're good to go.




