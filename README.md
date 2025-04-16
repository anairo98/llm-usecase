# LLM-Usecase
Deploy a Large-Language-Model (LLM) by using ollama, anythingllm and milvus as a Vector Database on Red Hat OpenShift. 

![alt text](image.png)

# Project Components

- **Milvus**: Is an open-source, high-performance vector database designed for managing and searching large-scale vector data. It is particularly useful in AI applications such as similarity search, recommendation systems, and retrieval-augmented generation (RAG) for text and images. 
- **Ollama**: Is a lightweight, extensible framework designed for building and running large language models on local machines. It provides a simple API for creating, running, and managing models, and includes a library of pre-built models that can be easily integrated into various applications. 
- **Anythingllm**: Is an all-in-one AI application designed to run large language models (LLMs) locally on your desktop. It allows you to chat with documents, use AI agents, and leverage various AI tools without needing complex setups or cloud resources. 
- **Minio**: Is an open-source, high-performance object storage system. It is capable of working with unstructured data such as photos, videos, log files, backups, and container images with the maximum supported object size being 50TB. 

# Prerequisites
- A Kubernetes cluster, something like OpenShift, Minikube, or any other managed Kubernetes services
- *kubectl* or *oc* CLI tools installed and configured

# Manual 

## Deploy Milvus, the Vector Database, in OpenShift

Deploy Milvus as a Standalone Database. 
Original Repository: [Milvus Repository](https://github.com/rh-aiservices-bu/llm-on-openshift/tree/main/vector-databases/milvus)

1. Create a new namespace/ project in OpenShift
2. Deploy Milvus by using the yaml-manifest file *milvus_manifest*
3. Additionally you can deploy a User Interface for the Milvus vector database byusing the *attu_manifest.yaml* file

> All resources should be created succesfully and al pods should run without any errors

4. When the attu Pod is deployed and everything is running, you can click on the route to the attu website. Login with the Enter *http://HOSTNAME:PORT* and the Milvus credntials 
    - There is only one User created, it is the *root* user with the password *Milvus* 
    - The User Credentials can be looked up in the original repository (linked above)

## Deploy Minio Instance

1. Create a new namespace/ project in Openshift for the Minio instance
2. create Deployment from the manifests file *minio_manifest.yaml*
    - In Openshift: *+Add* by *Import Yaml*

## Deploy Ollama Instance

1. Create a new namespace/ project in Openshift for the Ollama instance
2. Create the Pod in the namespace by using the container image from the following Repository. [RH Repository](quay.io/rh-aiservices-bu/ollama-ubi9) 
    - In Openshift: *+Add* by *Container Images* 
3. Go into the terminal of the ollama pod and use the *ollama* cli tool to check for the models inside the pod 
    - **Ollama Model Repository**: [Model Repo](https://ollama.com/search) 

> The size of the models is defined by the number of parameters of the model. *1b= 1 billion model parameters*. Thereby smaller models require less storage and computing power

```
## List all models:
ollama list

## pull a new model from the repo
ollama pull
```

## Deploy GUI with *anythingllm*

1. Create a new namespace/project in Openshift for the AnytingLLM instance
2. Create the Pod by using the manifest file *anythingllm_manifest.yaml* or create the pod from the image from the repository [RH Repository](https://quay.io/repository/rh-aiservices-bu/anythingllm-workbench?tab=tags&tag=1.7.5)
    	- In Openshift: *+Add* by *Container Images*

3. Access AnythingLLM by using the link provided by the route
    1. Select *Ollama* as the LLM provider
    2. Enter the Ollama Base URL, http link where the ollama model is running > http://HOSTNAME:PORT
    ![Ollama Base URL](ollama_svc.png)

    > Maybe this could take a moment, until the drop-down for selecting an ollama model is working - Give it a sec! 

    3. Select the right Ollama Model, which is present inside the ollama Service (Keep in mind that you need to pull images into the Pod in OpenShift first)
    ![anythingllm](anythingllm.png)
    4. Set up your user account with an password, email-address and so on ...
    5. Create your own Workspace and give it a name
    6. Go to Settings and set some configs...
        - Set the Embedding Model (One of the pulled ollama models)
        - Set the Vector Database (You need to enter the address, where the milvus service is running http://HOSTNAME:PORT and the credentials to login into Milvus)
        - Customize the Appearance of the User Interface

> If you deployed the pod by using the image from the official Red Hat repository, you need to set the target port to 8888 (The target Port 8888 is configured to be the target port in the configs and the specific Dockerfile of the official repository - link above) 




