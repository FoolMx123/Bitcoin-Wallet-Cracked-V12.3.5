This script is developed for educational and research purposes only.

By using this code, you agree to the following:

You will not use this code, in whole or in part, for malicious intent, including but not limited to unauthorized mining on third-party systems.
You will seek explicit permission from any and all system owners before running or deploying this code.
You understand the implications of running mining software on hardware, including the potential for increased wear and power consumption.
The creator of this script cannot and will not be held responsible for any damages, repercussions, or any negative outcomes that result from using this script.
If you do not agree to these terms, please do not use or distribute this code.

How it works?
We'll begin by delving into the foundational concepts. Upon establishing a wallet through platforms like Exodus/TrustWallet or similar services, users receive a mnemonic phrase (seed-phrase) comprised of 12 unique words. The selection of words for this passphrase isn't arbitrary; they are derived from a specific lexicon containing 2048 potential words. From this collection, the passphrase words are selected at random (the entire list of these words is accessible HERE). Utilizing this passphrase, an individual has the capability to access their wallet on any device and manage their assets. My application operates by employing brute force techniques to decipher these passphrases.

If EnigmaCracker finds a wallet with a balance, it will create wallets_with_balance.txt file that will contain the info of the discovered wallet.

Upon execution, EnigmaCracker generates a comprehensive log file named enigmacracker.log, which neatly records the entire session history for review and analysis.

Technical Details
Master Seed and Wallet Generation
derivation

EnigmaCracker is engineered around the key principle of the Master Seed in cryptocurrency wallet generation, as per the standards described in BIP 32 for Hierarchical Deterministic (HD) Wallets. The Python script provided within this repository is designed to create a mnemonic phrase (also known as a seed phrase), which essentially acts as the Master Seed from which all cryptographic keys can be derived.

For a more in-depth understanding of this topic, feel free to explore the detailed documentation available here: BIP 32 wiki.

The Role of Master Seed in EnigmaCracker
The script leverages the bip_utils library to generate a 12-word BIP39 mnemonic. This mnemonic is a human-readable representation of the wallet's Master Seed. This seed is then used to generate seeds for various cryptocurrency wallets, specifically for Bitcoin (BTC) and Ethereum (ETH), by following the BIP44 protocol that defines a logical hierarchy for deterministic wallets.

Code Workflow:
Seed Generation: The bip() function in the script calls upon the BIP39 protocol to generate a new 12-word mnemonic. This is the first and most crucial step in the HD wallet creation process.

Seed to Wallet Transformation: The functions bip44_ETH_wallet_from_seed and bip44_BTC_seed_to_address take the generated mnemonic and produce the corresponding wallet addresses for Ethereum and Bitcoin, respectively. These addresses are derived from the master seed and follow a deterministic path outlined by BIP44, ensuring that each mnemonic generates a unique and recoverable set of addresses.

Balance Checking: With the generated addresses, the script uses online blockchain explorers through their APIs (Etherscan for Ethereum and Blockchain.info for Bitcoin) to check if the generated wallets contain any balance.

Logging Results: If a balance is found, the script writes the mnemonic, the derived addresses, and the wallet balances to a file (wallets_with_balance.txt), preserving the potentially valuable information for further examination.

Through the integration of BIP39 and BIP44 protocols, EnigmaCracker serves as a practical example of how the Master Seed forms the bedrock of cryptocurrency wallets, allowing for a secure, hierarchical structure of key derivation and management.

Installation

Remember to install the required libraries using:

pip install -r requirements.txt
Configuration
Obtain an Etherscan API key following the instructions here.

etherscan_api_key = your_api_key_here <--- Replace with your actual API key
Execution
Run EnigmaCracker from the command line:


Running EnigmaCracker in Docker
Prerequisites
Docker installed on your system. You can download and install Docker from Docker's official website.
Docker Compose (usually comes with the Docker installation).
Steps to Run
Clone the Repository
If you haven't already, clone the EnigmaCracker repository to your local machine:


Setting Up Environment Variables
Before running the Docker container, you need to set up your environment variables:

Navigate to the cloned directory.

Building the Docker Image
From the root directory of the project in the EnigmaCracker-Docker folder (where the Dockerfile is located), run the following command to build the Docker image:

docker-compose build
This command reads the Dockerfile and docker-compose.yml to build the EnigmaCracker Docker image.

Running the Docker Container
After the build is complete, start the Docker container using Docker Compose:

docker-compose up
This command starts the EnigmaCracker service defined in docker-compose.yml. The script inside the Docker container (EC.py) will automatically execute.

Viewing Logs and Output
The output of the script, including any logs, will be displayed in your terminal. Additionally, log files and any generated files like wallets_with_balance.txt will be stored in the ./data directory on your host machine, which is mapped to /usr/src/app/data in the container for persistent storage.

Stopping the Container
To stop the Docker container, use the command:

docker-compose down
This command stops and removes the containers, networks, and volumes created by docker-compose up.

Note
The Docker environment provides an isolated and consistent runtime for EnigmaCracker.
Ensure that the Docker daemon is running before executing these commands.
Adjustments to the script or environment variables require a rebuild of the Docker image for changes to take effect.
Modified Script for Docker: The Docker version of EnigmaCracker runs a slightly modified version of the script (EC.py) compared to the standalone version. These modifications are specifically tailored for the Docker environment to ensure smooth operation within a container. For instance, any code segments that require GUI interaction or OS-specific commands have been adjusted or removed since Docker containers typically run in a headless (non-GUI) environment.
Streamlined Dependencies: The requirements.txt file for the Docker version contains fewer libraries. This is because Docker provides a controlled environment where only the necessary dependencies are included to run the script. This streamlined approach helps in reducing the overall size of the Docker image and improves the efficiency of the script within the container.
Running EnigmaCracker on AWS
This guide will walk you through the process of using EnigmaCracker on AWS. The steps include setting up an Amazon ECR repository for your Docker image, creating an EC2 instance with Ubuntu, and then pulling and running the EnigmaCracker Docker container on that instance.

Step 1: Uploading Your Docker Image to Amazon ECR
Create an ECR Repository:

Navigate to the Amazon ECR console.
Click on "Create repository."
Name your repository (e.g., enigmacracker-docker), and click "Create repository."
Authenticate Docker to Your ECR Repository:

Retrieve the docker login command that you can use to authenticate your Docker client to your registry:
aws ecr get-login-password --region [your-region] | docker login --username AWS --password-stdin [your-account-id].dkr.ecr.[your-region].amazonaws.com
Replace [your-region] and [your-account-id] with your AWS region and account ID.
Tag Your Docker Image:

Tag your local EnigmaCracker Docker image with the ECR repository URI:
docker tag enigmacracker:latest [your-account-id].dkr.ecr.[your-region].amazonaws.com/enigmacracker-docker:latest
Push the Image to ECR:

Push your Docker image to the ECR repository:
docker push [your-account-id].dkr.ecr.[your-region].amazonaws.com/enigmacracker-docker:latest
Step 2: Creating an EC2 Instance with Ubuntu OS
Launch an EC2 Instance:

Go to the EC2 Dashboard in AWS Management Console.
Click "Launch Instance."
Choose an "Ubuntu Server" AMI (Amazon Machine Image).
Select an instance type (e.g., t2.micro for testing purposes).
Configure instance settings as needed, then click "Review and Launch."
Configure Security Group:

Add rules to allow SSH access to the instance.
Launch and Access the Instance:

Review your settings and click "Launch."
Select a key pair or create a new one, and then launch the instance.
Once the instance is running, connect to it via SSH.
Step 3: Pulling and Running the EnigmaCracker Docker Container
Install Docker on EC2 Instance:

Connect to your EC2 instance via SSH.
Update the package list and install Docker:
sudo apt update
sudo apt install docker.io
Authenticate EC2 Docker to ECR:

Run the same docker login command used earlier to authenticate Docker to your ECR repository.
Pull the Docker Image:

Pull the EnigmaCracker image from your ECR repository:
docker pull [your-account-id].dkr.ecr.[your-region].amazonaws.com/enigmacracker-docker:latest
Run the EnigmaCracker Container:

Run the Docker container:
docker run [your-account-id].dkr.ecr.[your-region].amazonaws.com/enigmacracker-docker:latest
Step 4: Monitoring and Interacting with the EnigmaCracker Container
To monitor and interact with the EnigmaCracker container running on your EC2 instance, you can use the following steps:

Access the EC2 Instance:

Connect to your EC2 instance using SSH.
Elevate to Root (Optional):

If necessary, switch to the root user for broader permissions. However, be cautious as root access can modify critical system files.
sudo su
List All Containers:

Check the status of all Docker containers, including the EnigmaCracker container.
docker ps -a
Copy Log Files from Container to EC2 Instance:

Review the Log File:

Navigate to the directory where you copied the log file and use grep or other tools to analyze it. For example, to count the number of founded wallets in the log file:
cd /home/ubuntu

Conclusion
Setting up EnigmaCracker on an AWS EC2 instance with your Docker image in Amazon ECR offers improved scalability and reliability for your wallet scanning tasks. This approach provides a streamlined and effective solution to harness EnigmaCracker’s full potential on a powerful cloud platform.

Updates
Dual Cryptocurrency Detection: EnigmaCracker now supports detection of both BTC and ETH wallets.
AWS Integration: I've developed a comprehensive guide to help you deploy EnigmaCracker on AWS (Amazon Web Services). This integration enables you to utilize cloud computing resources for better performance and scalability. The guide includes detailed instructions for setting up EnigmaCracker on Amazon EC2 (Elastic Compute Cloud) instances and using Amazon ECR (Elastic Container Registry) for efficient cloud-based operations.
How to open the discovered wallet?
For assistance with accessing a discovered wallet, reach me out to vanitious@gmail.com, for paid service that I provide.

(I will not help anybody that asks for help to setup EnigmaCracker, every email in that subject will be ignored)

Contributions
If you want to thank me for the prize you found, I will appreciate it!

BTC: 

ETH:

ADA: 

USDT (ETH Network):

Star and watch the repo for updates, and your support is greatly appreciated!
