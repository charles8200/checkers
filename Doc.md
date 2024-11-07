# Checkers Project  

This document outlines the testing outcomes for the Checkers project, highlighting the seamless integration and functionality of the custom protocol developed for a Cosmos SDK-based application. All specified requirements and functionalities have been successfully implemented and tested.  

## Test Cases  

### Test Case 1: Successful Chain Initialization  

Follow these steps to verify successful initialization and operation of the blockchain:  

1. **Clone the Repository:**  

    ```bash  
    git clone https://github.com/charles8200/checkers.git  
    cd chain-minimal  
    git checkout -b main  
    ```  

2. **Run the Chain:**  

    ```bash  
    make install  
    make init  
    minid start  
    ```  

    **Outcome:** The chain runs successfully, confirmed by log messages indicating node initialization and block finalization:  

    ```  
    ...  
    1:12AM INF P2P Node ID ID=13b481265b25b655b10fd77fea0f33d2061675f1 ...  
    1:12AM INF Finalized block block_app_hash=... height=57 module=state  
    ...  
    ```  

### Test Case 2: Custom Protocol Buffers and Keeper Implementation  

1. **Requirements:**  
   - **Message Service Name:** `CheckersTorram`  
   - **RPC Method Name:** `CheckersCreateGm`  
   - **Message Request Type:** `ReqCheckersTorram`  
   - **Message Response Type:** `ResCheckersTorram`  

2. **Implementation Details:**  
   - **Proto File Setup:**  

     Located in `proto/charles8200/checkers/v1/tx.proto`, the message types and services are correctly defined. Confirmed presence of `ReqCheckersTorram`, `ResCheckersTorram`, and `CheckersTorram`.  

   - **Keeper File Verification:**  

     In `keeper/msg_server.go`, the `CheckersCreateGm` function handles custom game logic, storing additional data such as game start and end times.  

   - **Proto Type Adjustments:**  

     ```protobuf  
     message StoredGame {  
       string board = 1;  
       string turn = 2;  
       string black = 3 [(cosmos_proto.scalar) = "cosmos.AddressString"];  
       string red = 4 [(cosmos_proto.scalar) = "cosmos.AddressString"];  
       string start_time = 5;  // New field for storing game start time  
       string end_time = 6;    // New field for storing game end time  
     }  
     ```  

   - **Module and CLI Configuration:**  
     - **Codec.go** for message registration:  
       ```go  
       ...  
       +   &ReqCheckersTorram{},  
       ...  
       +   msgservice.RegisterMsgServiceDesc(registry, &_CheckersTorram_serviceDesc)  
       ...  
       ```  
     - **Module/Module.go** for service registration:  
       ```go  
       + checkers.RegisterCheckersTorramServer(cfg.MsgServer(), keeper.NewMsgServerImpl(am.keeper))  
       ```  

     - **Module/autocli.go** for CLI command integration:  
       ```go  
       + Service: checkersv1.CheckersTorram_ServiceDesc.ServiceName,  
       ...  
       +               RpcMethod: "CheckersCreateGm",  
       ```  

### Test Case 3: Game Creation via CLI and Exporting Stored Game Data  

1. **Requirements:**  
   - Game Creation via CLI  
   - Exporting Stored Game Data using `minid export`  

2. **Implementation Details:**  

    First, list Alice and Bob's addresses as created by `make init`:  

    ```bash  
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


## Conclusion  
The Checkers project has been thoroughly tested and all functionalities have been implemented successfully. From chain initialization to custom protocol implementation and game creation via CLI, each step has been validated. The project's integration with the Cosmos SDK exemplifies its robust capability in handling decentralized application logic. These successful outcomes confirm the readiness of the application for deployment and further development. Further enhancements could explore additional gameplay functionalities or user interface improvements to expand its use case and engagement.  