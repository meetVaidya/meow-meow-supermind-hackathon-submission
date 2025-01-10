# Langflow Project: Team Meow Meow

This Langflow project implements a conversational question-answering (Q&A) system that leverages a vector database (Astra DB) for efficient information retrieval. The system combines embeddings, prompt engineering, and a large language model (Groq) to provide intelligent responses to user queries.

#Project Link: https://www.findcoder.io/projects/supermind-hacks/67811c268015401a98b29580

## Overview

The core functionality of this flow is to:

1.  **Receive User Input:** Get the user's question via the "Chat Input" node.
2.  **Generate Embeddings:** Transform the user's query into a numerical vector representation using NVIDIA's embedding model. This allows for semantic search.
3.  **Vector Search (Astra DB):** Query the Astra DB vector database using the generated embedding to find relevant information.
4.  **Extract Relevant Context:** Retrieve relevant document chunks from Astra DB based on the vector search results.
5. **Load Local Context:** Load information from the local file (social_media_engagement_data_1000.csv), split into chunks using a specific overlap and size, and generate embeddings using NVIDIA.
6.  **Prompt Engineering:** Craft a dynamic prompt using the extracted context and user's question to guide the language model.
7.  **Generate Response (Groq):** Send the crafted prompt to the Groq language model to generate a concise and informative response.
8.  **Display Output:** Return the generated response to the user via the "Chat Output" node.

### Data Description

The data used for analysis is in a CSV format with the following columns:

*   **platform:** Social media platform (e.g., Facebook, Twitter, Instagram).
*   **post\_id:** Unique identifier for each post.
*   **post\_type:** Type of content (e.g., text, image, video, link).
*   **post\_content:** Text content of the post.
*   **post\_timestamp:** Time when the post was made.
*   **likes:** Number of likes.
*   **comments:** Number of comments.
*   **shares:** Number of shares.
*   **impressions:** Number of times the post was displayed.
*   **reach:** Number of unique users who saw the post.
*   **engagement\_rate:** Percentage of users that engaged with the post out of all who saw it.
*   **audience\_age:** Age group of the target audience for the post.
*   **audience\_gender:** Gender of the target audience for the post.
*   **audience\_location:** Geographic location of the target audience for the post.
*   **audience\_interests:** Interests of the target audience for the post.
*    **campaign_id**: Identifier for the campaign a post belongs to.
*   **sentiment**: A polarity analysis value given for text content of the post.
*   **influencer\_id**: Identifier for influencer which posted the content

### Required Sample Output
"The reach is highest among the 21-27 age group (average reach: 2940) and lowest among the 48-65 age group (average reach: 1040), with the 21-27 age group having 182% more reach than the 48-65 age group. The 28-35 age group has a moderate reach (average reach: 1720), while the 36-47 age group has an average reach of 2060."

## Flow Components

Here's a breakdown of the individual nodes and their roles:

### 1. Chat Input

*   **Type:** User Input
*   **Description:** Receives the user's question. In the image, a sample question is "Which seasons or holidays generally..." is given
*   **Output:** The text of the user's message.

### 2. NVIDIA Embeddings (Query)

*   **Type:** Embedding Generation
*   **Description:** Converts the user's query into a numerical vector using NVIDIA's `baai/bge-m3` model.
*   **Parameters:**
    *   **Model:**  `baai/bge-m3` 
*   **Output:**  The embedding vector representation of the user's query.

### 3. DS Astra DB (Query)

*   **Type:** Vector Database Interaction
*   **Description:** Queries the Astra DB database based on the generated embedding.
*   **Parameters:**
    *   **Astra DB Application Token:**  (Hidden)
    *   **Database:** `final`
    *   **Collection:** `text`
*   **Input:** Embedding Vector
*    **Output**: Results of vector search based on the query and embedded local documents

### 4. Parse Data

*   **Type:** Text manipulation
*   **Description:** Formats the context into a readable format.
*   **Input:** Receives retrieved text
*  **Output**: Formatted text data

### 5. File

*   **Type:** Data loading
*   **Description:** Loads the local data (social_media_engagement_data_1000.csv).
*   **Parameters:**
   *   **Path:**  `social_media_engagement_data_1000.csv`
*   **Output:** Content from the given file path as data.

### 6. Split Text

*   **Type:** Data splitting
*   **Description:** Splits input text into smaller chunks with the given specifications for processing.
*   **Parameters:**
   *   **Chunk Overlap:** 200
    *   **Chunk Size:** 1000
*   **Input:**  Content from the given file path
*   **Output:** List of Text Chunks

### 7. NVIDIA Embeddings (Local Context)
*   **Type:** Embedding Generation
*   **Description:** Converts the local text data into numerical vectors using NVIDIA's `baai/bge-m3` model.
*   **Parameters:**
    *   **Model:**  `baai/bge-m3`
*   **Output:** Embedding vectors

### 8. DS Astra DB (Ingest Context)

*   **Type:** Vector Database Interaction
*   **Description:** Ingests the local content to AstraDB.
*   **Parameters:**
    *   **Database:** `final`
    *   **Collection:** `text`
*   **Input:** List of Text Chunks, Embedding Vector
*    **Output**: The status of uploading the context to the Vector Database

### 9. Prompt

*   **Type:**  Prompt Engineering
*   **Description:** Generates a dynamic prompt based on a template, the retrieved context, and the user's question.
*   **Parameters:**
    *   **Template:** `(context) -- Given the context about (question)`.
*   **Inputs:**
    *   **context:** Relevant context retrieved from Astra DB
    *   **question:** User query from chat input
*   **Output:**  The dynamically created prompt to pass to Groq.

### 10. Groq

*   **Type:**  Large Language Model
*   **Description:** Generates a response to the dynamic prompt.
*   **Parameters:**
    *   **Model:** `llama-3-70b-versatile`
    *   **Temperature:** `0.9`
*   **Input:**  The dynamically created prompt
*   **Output:** The text of the generated response from the Groq model.

### 11. Chat Output

*   **Type:** Output
*   **Description:** Displays the text response from the Groq model to the user interface.
*   **Input:**  The generated text response.
*   **Output:** Display on User interface.

## Prerequisites

*   Langflow environment is set up.
*   API keys for Astra DB and Groq.
*  Nvidia API access is available.
*  Local file `social_media_engagement_data_1000.csv`

##
## Start asking questions to the chatbot.
