# Checkers Game  

Welcome to the Checkers Blockchain project! This project demonstrates how to build a simple blockchain-based checkers game using the Cosmos SDK v0.50. The blockchain allows players to create, store, and query checkers games on-chain, leveraging the modular capabilities of the Cosmos SDK.  

## Table of Contents  

- [Introduction](#introduction)  
- [Prerequisites](#prerequisites)  
- [Project Structure](#project-structure)  
- [Setup and Installation](#setup-and-installation)  
- [Module Overview](#module-overview)  
- [Development Workflow](#development-workflow)  
- [Usage](#usage) 

## Introduction  

This project demonstrates a simple implementation of a checkers game on a blockchain using the Cosmos SDK v0.50. It highlights the Cosmos SDK’s capability to handle custom logic through modules and offers a showcase of a decentralized application.  

## Prerequisites  

Before you begin, ensure you have the following installed:  

- [Go](https://golang.org/) v1.21 or later  
- [Docker](https://www.docker.com/)  
- [Git](https://git-scm.com/)  

## Project Structure  

This project is structured as follows:  

- **chain-minimal/**: The minimal application using Cosmos SDK.  
- **checkers-minimal/**: The module where the checkers logic resides.  

## Setup and Installation  

To set up the project, follow these steps:  

1. **Clone the Repo**:  
    ```bash  
    git clone https://github.com/charles8200/checkers.git
    cd chain-minimal  
    ```  

2. **Initialize the Chain**:  
    ```bash  
    make install  
    chmod +x ./scripts/init.sh  
    make init  
    ```  

5. **Run the Chain**:  
    ```bash  
    minid start  
    ```  

## Module Overview  

The Checkers module includes:  

- **Game Storage**: Stores game state, including board configuration and player details.  
- **Message Handling**: Processes game creation and state queries.  
- **Game Logic**: Implements the rules of checkers based on a basic 8x8 board game.  

## Development Workflow  

The development workflow includes:  

1. **Define Protobuf Messages** for new functionalities.  
2. **Compile Protobuf** and regenerate Go files.  
3. **Implement Keeper and Server Logic** for new messages and queries.  
4. **Add CLI Commands** for user interaction.  
5. **Run and Test** the chain functionality.  



## Usage  

To interact with the blockchain, use the following commands:  

- **Create a New Game**:  

    First list alice and bob's addresses as created by make init:

    ```  
    minid keys list --keyring-backend test  
    ```  

    This returns something like:

    ```  
    - address: mini16ajnus3hhpcsfqem55m5awf3mfwfvhpp36rc7d
        name: alice
        pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"A0gUNtXpBqggTdnVICr04GHqIQOa3ZEpjAhn50889AQX"}'
        type: local
       - address: mini1hv85y6h5rkqxgshcyzpn2zralmmcgnqwsjn3qg
        name: bob
        pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"ArXLlxUs2gEw8+clqPp6YoVNmy36PrJ7aYbV+W8GrcnQ"}'
        type: local
    ``` 

    With this information, you can send your first create game message:

    ```  
    minid tx checkers create id1 \
        mini16ajnus3hhpcsfqem55m5awf3mfwfvhpp36rc7d \
        mini1hv85y6h5rkqxgshcyzpn2zralmmcgnqwsjn3qg \
        --from alice --yes

    ```  

    This returns you the transaction hash as expected. To find what was put in storage, wait a bit and then stop the chain with CTRL-C. Now call up:

    ```  
    minid export | tail -n 1 | jq 
    ```  

    This should return something with:

    ```  
    "checkers": {
      "params": {},
      "indexedStoredGameList": [
        {
          "index": "id1",
          "storedGame": {
            "board": "*b*b*b*b|b*b*b*b*|*b*b*b*b|********|********|r*r*r*r*|*r*r*r*r|r*r*r*r*",
            "turn": "b",
            "black": "mini1czwdg0cvtym6h8t6hcw5kxdef5wl0kx8j8ft46",
            "red": "mini1s9uhyyjqy8ldldxltszx8xz4qpelcflp4rg2ap",
            "start_time": "2024-11-07 00:51:20.196917036 +0000 UTC m=+96.840866678",
            "end_time": ""
          }
        }
      ]
    },
    ``` 

- **Query an Existing Game**:  

    Now query your previously created game:
    ``` 
    minid start
    minid query checkers get-game id1

    ```  
    This returns:
    ```  
    Game:
        black: mini1czwdg0cvtym6h8t6hcw5kxdef5wl0kx8j8ft46
        board: '*b*b*b*b|b*b*b*b*|*b*b*b*b|********|********|r*r*r*r*|*r*r*r*r|r*r*r*r*'
        red: mini1s9uhyyjqy8ldldxltszx8xz4qpelcflp4rg2ap
        start_time: 2024-11-07 00:51:20.196917036 +0000 UTC m=+96.840866678
        turn: b
    ```  

    Try to get a non-existent game:
    ```  
    minid query checkers get-game id2
    ```  

    This should return:
    ```  
    {}
    ```  
