# Rasa Haystack DB Demo



This repo is a bare-bones chat bot project using [Rasa](https://rasa.com/) in combination with [Haystack](https://github.com/deepset-ai/haystack) along with SqliteDB. 
While Rasa is used for the whole flow of the dialogue and intent management, 
Haystack is used to answer the long tail of "knowledge queries" that can be answered by searching an answer in a document corpus. 
This example repo sketches how to use the _fallback intent_ or an dedicated _knowledge base intent_ to offload such queries to haystack.

After initializing a plain rasa project (`rasa init`) changes were made to
- domain.yml
- config.yml 
- data/nlu.yml
- data/rules.yml
- actions/actions.py

More details can be found at [this page](https://haystack.deepset.ai/usage/chatbots)!

## Get Started

- Start the Haystack REST API and a demo DocumentStore, RASA along with SQLiteDB via Docker:
```
git clone https://github.com/athenasaurav/rasa_haystack_db.git
cd rasa_haystack_db
docker-compose build
docker-compose up -d
``` 
- This will automatically train a model in this repository with `rasa train`  

# Interacting with RASA

- To Interact with rasa we can use either of the following endpoints.

## Using Default REST

- Send POST request to ```http://ip:5005/webhooks/rest/webhook```
- Request format should be like :
```
{
    "message" : "hi",
    "sender": "sender"
}
```
- You will receive response like this:
```
{
    "message" : "Hi! How can i help you?",
    "sender": "sender"
}
```

## Using custom REST channel named senderid

- Send POST request to ```http://ip:5005/webhooks/senderid/webhook```
- Request format should be like :
```
{
    "message" : "hi",
    "sender": "sender"
}
```
- You will receive response like this:
```
{
    "message" : "Hi! How can i help you?",
    "sender": "sender"
}
```
## Customizing Custom REST Url

To customize the rasa custom url we need to make following changes in Three files. 

Step 1) Customize the rest url name in ```MyIO.py``` in ```actions/connector```

```
nano actions/connector/MyIO.py
```

Step 2) Make these following changes in the ```MyIO.py``` file. For Ex: If you want the custom REST url should be ```/webhooks/example/webhook``` instead of ```/webhooks/senderid/webhook```

---> Change Line no. 12, Class name from ```class senderidInput(InputChannel):``` to ```class exampleInput(InputChannel):```
---> Change Line no. 15, Return name from ```return "senderid"``` to ```return "example"```
---> Change Line no. 104, collector form ```collector = senderidOutput()``` to ```collector = exampleOutput()```
---> Change Line no. 130, Class name from ```class senderidOutput(CollectingOutputChannel):``` to ```class exampleOutput(CollectingOutputChannel):```
---> Change Line no. 133, Return name from ```return "senderid"``` to ```return "example"```

Save this changes. 

NOTE : Please Check your doing it all from Root Directory.

Step 4) Copy the Newly edited ```MyIO.py``` from ```actions/connector``` to ```backend/connector```

```
rm backend/connector/MyIO.py

cp actions/connector/MyIO.py backend/connector/MyIO.py

```
Step 5) Update the name in the ```backend/credentials.yml```
```
nano backend/connector/credentials.yml
```
Change the Following : Line no 9 : ```connector.MyIO.senderidInput:``` to ```connector.MyIO.exampleInput:```

Step 6) Save and Exit. Voila Your Custom REST API Endpoint is ready to be deployed. 

Step 7). Rebuild and Restart the docker

```
docker-compose down
docker-compose build
docker-compose up -d
```


