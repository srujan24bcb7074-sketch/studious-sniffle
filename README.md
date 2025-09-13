n8n RAG Chatbot with Local LLMs and Pinecone
This repository contains an n8n workflow that implements a Retrieval-Augmented Generation (RAG) chatbot. The workflow automatically ingests documents from a Google Drive folder, processes them, stores them in a Pinecone vector database, and uses a local AI model via Ollama to answer questions based on the document content.

Overview
This workflow provides a powerful, automated pipeline for creating a knowledgeable chatbot based on your own documents. It has two main components:

Document Ingestion Pipeline: This part of the workflow watches a specific Google Drive folder for new files. When a file is added, it's downloaded, its text is extracted, split into chunks, converted into vector embeddings using a local Ollama model, and finally stored in a Pinecone index for efficient searching.

Chat Pipeline: This component provides a chat interface. When you send a message, an AI agent uses the information stored in the Pinecone vector store to find the most relevant context and generate a helpful, conversational response using another local Ollama chat model.

Features
Automated Document Processing: Automatically triggers when new files are added to a specified Google Drive folder.

Local AI Models: Utilizes Ollama to run embedding and language models locally, ensuring data privacy and control.

Efficient Vector Storage: Leverages Pinecone for scalable and fast semantic search over your documents.

Conversational Memory: Remembers the context of the conversation for more natural interactions.

Easy to Use: Interact with your documents through a simple chat interface.

Customizable: Easily swap out models, vector stores, or document sources.

Workflow Explained
The workflow is composed of several connected nodes, each performing a specific task.

1. Document Ingestion Flow
Google Drive Trigger: Monitors a specific folder in Google Drive for newly created files.

Download file: Downloads the file that triggered the workflow.

Extract Document Text: Extracts the raw text content from the downloaded file.

Pinecone Vector Store1 (Insert Mode): This is the final node in the ingestion chain. It orchestrates the process of turning the text into vectors and saving them.

Embeddings Ollama1: Creates vector embeddings from the text chunks using the nomic-embed-text:v1.5 model.

Default Data Loader & Recursive Character Text Splitter: The text is loaded and broken down into smaller, 100-character chunks to ensure it fits within the model's context limit.

The processed and embedded data is then inserted into the n8n Pinecone index.

2. Chat & Retrieval Flow
When chat message received: This is the starting point for user interaction. It provides a webhook URL to receive chat messages.

AI Agent: The core of the chat logic. It receives the user's input and coordinates with other components to generate a response.

Ollama Chat Model: Uses the llama3.2:latest model to understand the user's query and generate the final response.

Simple Memory1: Retains the history of the current conversation to provide contextually aware answers.

Vector Store Tool: The agent's tool for searching for information.

Pinecone Vector Store: Searches the n8n index for documents relevant to the user's question.

Embeddings Ollama: Converts the user's question into a vector embedding to perform the similarity search in Pinecone.

Prerequisites
Before you can use this workflow, you need to have the following set up:

n8n: An active n8n instance (cloud or self-hosted).

Ollama: Ollama running locally or on a server accessible by your n8n instance. You must have the following models pulled:

llama3.2:latest

nomic-embed-text:v1.5

Pinecone Account: A free or paid Pinecone account with an API key and an index created (e.g., named n8n).

Google Account: A Google account to set up the Google Drive trigger.

Setup Instructions
Import Workflow:

Copy the JSON data for the workflow.

In your n8n canvas, select Import from Clipboard and paste the JSON.

Configure Credentials:

Google Drive:

In both the Google Drive Trigger and Download file nodes, select your Google Drive credentials from the dropdown or create a new set by following the on-screen instructions.

Pinecone:

In both the Pinecone Vector Store and Pinecone Vector Store1 nodes, select your Pinecone credentials or create new ones using your Pinecone API key.

Ollama:

In the Ollama Chat Model, Embeddings Ollama, and Embeddings Ollama1 nodes, select your Ollama API credentials. This will typically just be the Base URL of your Ollama service (e.g., http://localhost:11434).

Configure Nodes:

Google Drive Trigger: In the Folder To Watch field, select the specific Google Drive folder you want to monitor for new documents.

Pinecone Nodes: In both Pinecone nodes, ensure the Index Name is set to the name of your index (e.g., n8n).

Activate the Workflow:

Save the workflow.

Click the Activate toggle in the top-right corner. The workflow is now live.

How to Use
Adding Documents
Navigate to the Google Drive folder you configured in the Google Drive Trigger.

Upload a text file (e.g., .txt, .md, .pdf).

The workflow will automatically run. You can check the "Executions" tab in n8n to see its progress. Once it completes successfully, your document's content is ready to be queried.

Chatting with the Bot
Go to the When chat message received node and open the Chat view.

Type your question about the content of the documents you've uploaded.

The AI agent will retrieve the relevant information from Pinecone and use the Ollama model to generate an answer for you.
