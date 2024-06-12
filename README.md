# Install Guidelines
In this repository, you can find the install guidelines for a custom kmapp modules implementation.

## Requirements on local machine
As this is a fully containerized application, there are not many requirements for your local machine. The application only requires:

1. Docker>=24.0.1
2. Python>=3.9

## Setup

1. Clone the repository to your local machine or extract zip folder
2. Make sure to add all missing environment variables and rename the ENV file to .env
      
Values that need to be added:


| Environment Variable  | Description |
  | -----------|---------------|
| OPENAI_API_KEY | Key to OpenAI API |

Other keys and variables:
| Environment Variable  | Description |
  | -----------|---------------|
| PUBLIC_MEILISEARCH_KEY | Key will be created upon execution of the frontend container, no need to fill it out. |
| AWS_ACCESS_KEY | AWS access key to store pdf files after splitting. Only necessary if AWS bucket is used as file storage.|
| AWS_SECRET_KEY | AWS secret key to store pdf files after splitting. Only necessary if AWS bucket is used as file storage.|
| MINIO_ACCESS_KEY | Local alternative to AWS S3 bucket storing the files. Minio access key to store pdf files after splitting. The key can be created or retrieved from the local minio console at localhost:9991/access-keys (or your port specified) or a save username can be specified.|
| MINIO_SECRET_KEY | Minio secret key to store pdf files after splitting. The key can be created or retrieved from the local minio console at localhost:9991/access-keys (or your port specified) or a safe password can be specified.|

3. In the root directory of the project modules, run the *scripts/init_repo.sh* script. It generates the following additional folder structure, to persist dbs and logs on your local machine.

```bash
sudo chmod +x scripts/init_repo.sh && sudo ./scripts/init_repo.sh
```
 ```  
       -services
             --meilisearch
               ------meili_data
             --minio
               ------data
             --qdrant
             --rabbitmq
                 ----lib
                 ----log
```
   
4. Build the application
   ```bash
   docker-compose build 
   ```
   
   The building process takes a few minutes the first time. It can occur that some services need a larger model (e.g. Spacy's sentence tokenizer for the Enlgish language) which will be downloaded. If the internet connection is too slow, some services might run into a time-out. Restarting the building process will fix these issues.

   Alternatively, upon request, pre-built images will be provided to skip the re-building process.
6. Start the application
   ```bash
   docker-compose up
   ```
   
## Initializing your own document collections
You can use the send_local.py script to set up your meilisearch and qdrant index with some documents! Replace <your_test_folder_containing_test_files> with the path to your local test folder containing your test pdfs. 

```bash
send_local.py <your_test_folder_containing_test_files>
```

Using the `send_local.py`script requires `pika` as Python client for rabbitmq to be installed. Install it using pip

```bash
pip3 install pika
```


## Using meilisearch and qdrant snapshots


If you want to initialize the project with a meilisearch snapshot, paste a snapshpot file accordingly.
   
    -services
        --meili_data
           ----snapshots
               ----data.ms.snapshot    
       --qdrant
       --rabbitmq
           ----lib
           ----log

To keep the system persistent, a qdrant snapshot from the same state of indexed documents needs to be uploaded. This needs to be triggered by a python script.

Execute 
````
python3 scripts/restore_qdrant_from_snapshot.py <path_to_your_snapshot_folder>
````
   

## Start the application
`sudo docker-compose build && sudo docker-compose up`

## Access the UI
via ```localhost:8001/search```

### Example

![Screenshot 2024-03-18 at 09 55 59](https://github.com/kmapp-dev/modules/assets/62940341/059f6265-a839-4ef9-96ca-6fc168332ebb)




