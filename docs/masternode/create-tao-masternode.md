This tool aims to help beginner/intermediate masternode owners.
It acts as a terminal wizard which helps the user generate a working configuration for docker-compose to run the node.

Using create-tao-masternode and docker-compose offers you benefits like:

- working "out of the box" docker-compose configuration
- auto-restarting your node on failure
- flexible configuration (storage, logging, ports)

!!! Important
    For security reasons, it is strongly recommended that you create a new user and grant them sudo privileges instead of 
    running your server as root. Create your new user account and sign in as that user before continuing.

## Prerequisites

  - Docker
  - Docker-compose
   

### Installation of Docker CE

To install Docker, first update the apt package index.
```
sudo apt update
sudo apt-get upgrade -y
sudo reboot now
```
Rebooting will disconnect you. You will need to need to wait a moment before reconnecting to give the server time to restart.

Next, install packages to allow apt to use a repository over HTTPS.
```
sudo apt install apt-transport-https ca-certificates curl software-properties-common dirmngr gnupg
```

Add Docker’s official GPG key.
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Verify that you now have the key with the fingerprint `9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`, by searching for the last 8 characters of the fingerprint.
```
apt-key fingerprint 0EBFCD88
```

Set up the stable Docker repository.
```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

Update the apt package index, then install the latest version of Docker CE.
```
sudo apt update

sudo apt install docker-ce
```

Once installed, add your current user to the Docker group.
```
sudo usermod -aG docker $(whoami)
```
You can check that you have been added to the Docker group by running
```
groups yourusername
```
(substitute your new user account name for "yourusername" above)
You should see both "sudo" and "docker" listed as groups you are part of.

You can also try
```
cat /etc/group | grep docker
```
and you should see your username listed as a member of that group. 

Optional: If you do not see yourself as a member of the docker group for some reason, try this command instead
```
sudo usermod -aG docker yourusername
```

!!! You need to log out and back into your account to finish this process.
    Until then, you will not be able to access the Docker deamon.


Next, verify that Docker CE is installed correctly by running the hello-world image:
```
docker run hello-world
```

This command downloads a test image and runs it in a container.
When the container runs, it prints an informational message starting by "Hello from Docker!" and exits.

### Installation of Docker-compose

To install docker-compose, start by downloading the executable.
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

Make it executable.
```
sudo chmod +x /usr/local/bin/docker-compose
```

Optional: install bash completion.
```
sudo curl -L https://raw.githubusercontent.com/docker/compose/1.23.2/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
```

Verify that docker-compose is correctly installed by running the version command.
```
docker-compose version
```

## Install Python
```
sudo apt install python3
sudo apt install python3-pip
```
Check to make sure you have Python installed and that it's at least the minimum required version 3.6 
```
python3 --version
```

## Update your path
Edit the profile file in /etc
```
sudo nano /etc/profile
```
scroll to the very bottom of the file and add the following. 
Make sure to replace any mention of "yourusername" with your actual account name:
```
export PATH=$PATH:/home/yourusername/bin:/home/yourusername/.local/lib/python3.6/site-packages:/home/yourusername/.local/bin
```
Hit CTRL-X to exit, type Y when prompted if you want to save, then press the Enter (PC) or Return (Mac) key on your keyboard to save

You should then log out and back in again to register the changes made to the path before continuing. 

## Installation of Create-Tao-Masternode 

Download the create-tao-masternode docker image from the official Tao repo
```
sudo curl -L https://github.com/Tao-Network/create-tao-masternode/tree/master/create_tao_masternode -o create-tao-masternode
```

Make it executable
```
sudo chmod +x create-tao-masternode
```
Move it to /usr/local/bin/ so it's in your path.
```
sudo mv create-tao-masternode /usr/local/bin/
```
Install it with pip3
```
pip3 install create-tao-masternode
```

Create a folder in your user account folder where the chain data will be stored 
```
mkdir /home/yourusername/taodata
```

You are now ready to create and run your masternode! 

## Usage

Simply run create-tao-masternode with the name of your masternode as argument. We will be using "taomasternode" as an example for the rest of this document, but make sure to give your own masternode a unique name! 

```
create-tao-masternode taomasternode
```

Follow the wizard by replying to the following questions:

- **Coinbase private key**:
  Your masternode coinbase account private key.
  A Tao node uses an account to be uniquely identified and to receive transaction fee.

!!! note "Important note:"
    We advise for security measures to use a fresh new account for your masternode.
    This is not the account which will receive the rewards.
    The rewards are sent to the account which will make the initial deposit.

- **Storage**:
  The storage location for your node data (chaindata).
  Either `docker volume` if you want to use the default docker volume location, or `host directory` if you want to define specific location on your filesystem (usefull when extending storage). You can press enter/return here and it will automatically select host directory, or you can type "host directory" without quotes and press enter/return. 
Using host directory is recommended over the docker volume. 

- **Chaindata**:
  The name of the docker volume that will be used or the path to the directory containing the chaindata, depending on your answer to the last question. You should enter the full path to the folder you created using the mkdir command just a moment ago. Easiest thing to do is to copy and paste it from where you typed it earlier. 
  Ex: /home/yourusername/taodata 
  
- **The volume already exists**:
  If you selected "docker volume", this will determine if the volume already exists or if it needs to be created.

- **Expose RPC**:
  If you want to expose or not port `8545`.
  It is the RPC api to your node.
  It should be only exposed if you have a specific reason to access the Tao JSON-RPC Protocol.
  The masternode owner is responsible of proxing and securing the RPC api as it should not be directly exposed to the internet. 
  Just press enter/return to continue at this prompt. 

- **Expose WebSocket**:
  If you want to expose or not port `8546`.
  It is the WebSocket api to your node.
  It should only be exposed if you have a specific reason to access the Tao Protocol via WebSocket.
  The masternode owner is responsible of proxing and securing the WebSocket api as it should not be directly exposed to the internet.
  Just press enter/return to continue at this prompt. 

- **Logging level**:
  Set the logging level of the Tao container to error, info or debug.
  Info or Error is usually a good logging level.
  Only use the debug level if you have a good reason to do so, it will generate a lot of output and increase ressource usage.
  Just press enter/return here. 


Once finished, you will get a folder named after your masternode (in our case "taomasternode") with two files. 

You can use, as an example,
```
cd /home/yourusername/taomasternode
ls -l -a
```
to view the contents. You should see two files:

- `.env`
  Which contains the configuration generated from your answers to the question.

- `docker-compose.yml`
  Which tells docker-compose how and which container run for your node.
  Your specific configuration will be read from the `.env` file.

Now that we have generated the correct initial configuration for docker-compose, we just need to start our node. Make sure to run the following commands in the "taomasternode" folder which was just created or you will get an error message that no configuration file was found. Just cd to the folder before running it. 

```
docker-compose up -d
```

You can check that your masternode is running with the `ps` sub-command.

```
docker-compose ps
```

For more docker-compose commands, use `docker-compose --help` or refer to their [documentation](https://docs.docker.com/compose/reference/overview/).


That should be it! You can view your node by visiting https://stats.tao.network and it should be visible and you should see it syncing the blockchain. 




The following steps can be safely ignored by most people. 

## Migrating from `taomn`

You can find a guide on migrating from `taomn` in the [wiki](https://github.com/Tao-Network/docs/wiki/Migrate-from-taomn-to-docker-compose-with-create-tao-masternode).

For the long time masternode runners who started with our older tool, `taomn`, here are the commands to achieve the same actions.

**taomn start**:
```
docker-compose up -d
```

**taomn stop**:
```
docker-compose stop
```

**taomn update**:
```
docker-compose pull
docker-compose up -d
```

**taomn remove**:
```
docker-compose down
```

## Troubleshooting

### error: could not access the docker daemon

If you have installed Docker, you probably forgot to add your user to the docker group.
Please run this, close your session and open it again.

```
usermod -aG docker $your_user_name
```
