# How to Setup an Eigenlayer Operator

## System Requirement
![Screenshot_1](https://github.com/user-attachments/assets/91ebbc59-8134-4118-8162-8e9ae827344f)


## 1. Install Dependecies
```console
# Update & Install Packages
sudo apt update & sudo apt upgrade -y
sudo apt install ca-certificates zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev curl git wget make jq build-essential pkg-config lsb-release libssl-dev libreadline-dev libffi-dev gcc screen unzip lz4 -y

# Install Docker
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
docker version

# Install Docker-Compose
VER=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep tag_name | cut -d '"' -f 4)

curl -L "https://github.com/docker/compose/releases/download/"$VER"/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
docker-compose --version

# Docker Permission to user
sudo groupadd docker
sudo usermod -aG docker $USER

# Install Go
sudo rm -rf /usr/local/go
curl -L https://go.dev/dl/go1.22.4.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
echo 'export PATH=$PATH:$(go env GOPATH)/bin' >> $HOME/.bash_profile
source .bash_profile
go version
```

## 2. Install EigenLayer CLI
```console
curl -sSfL https://raw.githubusercontent.com/layr-labs/eigenlayer-cli/master/scripts/install.sh | sh -s -- v0.12.0-beta2

export PATH=$PATH:~/bin

eigenlayer --version
```

## 3. Create Eigenlayer wallet
### Create a new Key

1- Create operator address
```console
eigenlayer operator keys create --key-type ecdsa opr
```
2- Enter a password and press Enter
* Best symbol for password is `*` , you can choose a password like this: `***123ABCabc123***`

3- Save your wallet private key

![image](https://github.com/user-attachments/assets/2fb1071a-04fd-4ceb-9d08-9cd3de1c877a)

4- `Ctrl+C+Enter+Enter` & save your wallet info

![image](https://github.com/user-attachments/assets/985845ab-9510-4afd-aa1b-76cca556094e)

#

### Optional: Import an old key

> Replace `PRIVATEKEY`
```console
eigenlayer operator keys import --key-type ecdsa opr PRIVATEKEY
```
> Enter a password and press Enter
> 
> Best symbol for password is `*` , you can choose a password like this: `***123ABCabc123***`
#

##  Fund your Eigen wallet
You’ll need at least 1 Holesky ETH to cover the gas cost of the operator registration. Make sure to send at least 1 ETH to your Eigenlayer operator’s address

## 4. Config & register operator
```console
eigenlayer operator config create
```
* `operator address`: Your Eigenlayer ETH address
* `earnings address`: press `Enter`
* `ETH rpc url`: https://ethereum-holesky-rpc.publicnode.com
* `network`: holesky
* `signer type`: local_keystore
* `ecdsa key path:`: /root/.eigenlayer/operator_keys/opr.ecdsa.key.json

## 5. Edit metadata.json
1- Open `metadata.json` edit menu
```console
nano metadata.json
```

2- Complete your details in `metadata.json`
* `logo`: Must be raw link to your operator favorite logo
* Logo only supports `.png` file less than 1MB in size
* Create a repositry in github and upload `.png` there
* To get your logo raw link:
  * **1)** Navigate to the file in your repository
  * **2)** Click on the file name to view its contents
  * **3)** In the address bar of your browser
  * **4)** replace `blob` with `raw` in the URL
  * **5)** Press Enter to load the raw content view
  * **6)** Copy the updated URL from the address bar and paste in front of `logo` field in `metadata.json`
* My example `metadata.json` file in github: https://github.com/0xmoei/chainbase-testnet/blob/main/metadata.json
* `Ctrl+X+Y+Enter` to save `metadata.json`

3- Upload `metadata.json` to your github repository and get its **raw link** like previous step
* You can create a metadata.json file in github with the contents of your file or you must download it from VPS using Termius or Mobaxterm ssh clients

4- Open `operator.yaml` edit menu
```console
nano operator.yaml
```

5- Add your metadata.json raw url in github in front of `metadata-url`

![Screenshot_158](https://github.com/user-attachments/assets/af8a4fe3-a5fe-48ff-a198-7e3e808ff07d)


## 6. Run Eigenlayer holesky Node
```console
eigenlayer operator register operator.yaml

# Check status
eigenlayer operator status operator.yaml
```

![image](https://github.com/user-attachments/assets/6b30b646-dc18-4088-a199-bcb4e01c367a)
