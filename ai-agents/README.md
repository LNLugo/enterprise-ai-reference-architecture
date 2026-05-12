 An end-to-end tutorial for developing a RAG Application from scratch

 Providing quick and context-aware customer support is crucial for most organizations. In this post, I’ll walk you through a simple implementation of a Retrieval-Augmented Generation (RAG) system designed for Fitness Passport, a corporate health and fitness program in Australia. I decided to build this after sifting through their FAQs endlessly when I wanted to check the location of their closest fitness facilities to my address.

The system design is simple. I built a scraper to get the FAQs and their respective answers, which were then vectorized and stored for retrieval in the chat application. Ideally, a RAG system works by retrieving relevant documents from a knowledgebase in response to a query. It then augments a Large Language Model’s (LLM) context with this retrieved information to generate more accurate and knowledge-grounded responses. I used an OpenAI API model as the LLM for this example. However, open source models can also be set up and run locally via, say, Ollama.

This article will be beneficial to a newbie in the data space that’s trying their hand at RAG systems. I’ll try to cover every aspect, from data scraping and embedding generation to model integration and local deployment, in a very simple way. By the way, I also write about the changes AI is bringing to traditional roles. I’ll be happy if you also read my earlier article on how AI is transforming the traditional customer service sector.

1. Introduction
As previously mentioned, this RAG app could potentially be used as a smart replacement for the FAQ pages that many companies have. The combination of especially referenced document retrieval with a generative language model, makes them a favourite for organisations compared to static FAQs. Such systems are particularly useful in domains where accuracy and context are paramount, like in the Fitness Passport example. This is because they bridge the gap between static document stores and dynamic natural language generation.

2. Core Components of the RAG System
Core components of a RAG system vary depending on complexity. However, the main ones are as follows:

Vector Database: A repository of documents that have been indexed and stored, typically as vector embeddings.
Query Processor and Retriever: The processor reformulates the input query, optimizing it for retrieval, while the retriever is the one that searches for relevant information in the vector database.
Embeddings model and LLM: The embedder converts the queries and documents to capture semantic meaning, while the LLM generates responses based on the queries and context.
The interoperability of the different components is depicted in the below flowchart:

Press enter or click to view image in full size

Flowchart showing the ingestion and retrieval process.
TLDR: The RAG pipeline converts user queries into vector embeddings, retrieves relevant information from a vector database, and feeds both the query and retrieved context to a language model to generate informed responses for the same user.

I’m done with the boring stuff, so let's jump in the code. I’ve split it up in several components:

a) Data Scraping (scraper.py)
This component extracts FAQs and their answers from Fitness Passport’s support page. The site’s category URLs are not dynamic, so it's quite simple to get the data. I used Python’s requests library for HTTP requests and BeautifulSoup for HTML parsing. In short, the script:

Fetches the main support page and locates category links. This can change, so it is always good practice to recheck.
For each category, the code finds individual FAQ article links.
Finally, the FAQ question and answer pairs are extracted from each article page and stored as a dictionary.

b) Data Handling (data_handler.py)
Once data is scraped, it needs to be ingested, cleaned, chunked, and prepared for retrieval. Below are the key steps in this data handling class:

ChromaDB and the SentenceTransformer model are initialised, and the JSON output in the previous step is loaded.
The answers are then split/chunked into paragraphs to create smaller, manageable chunks.
The chunks are then converted into vector embeddings using the all-MiniLM-L6-v2 sentence transformer model, and the results (vectors) appended to the question-answer pairs.
Results in 3 are stored in a persistent ChromaDB collection for querying.

c) Query Processing (utils.py)
This is where the conversion of user queries into embeddings happens. Two key things happen here:

Prompt Building: The build_prompt function takes a user query and relevant FAQ chunks to create a comprehensive prompt for the LLM. This includes instructions on how the model is to respond.
Response Formatting with References: Once the model produces a response, additional references to the source documents are appended for transparency.

d) Response Generation and Chatbot Interface (app.py)
This final bit integrates everything into a functional chatbot interface using Streamlit. Streamlit is a simple app, and the learning curve for the same is quite straight forward, so it can be your choice. To run, app.py simply type this command streamlit run app.py in the CLI of your running environment. I use VS Code to run my experiments. If you replicate the same, then you’ll be able to run as in the following screenshot.

agents_envis the environment that could be created as described here. You can choose your environment name and thereafter install the required packages in this requirements.txt file.

If the environment is properly setup, running app.py should open up a new tab/window in your default browser with such an interface.

Enter your OpenAI key, then the chat interface should open up. You can then interact as in this screen capture.

Remember, you can use any other open source, and it doesn’t have to be an OpenAI model where a key is needed. That should actually be it. It’s that simple to build a RAG system.

3. Summary of the code sections
If you didn’t have the time to go through all the sections, then the below pipeline summary is enough.

scraper.py: This module manages the end-to-end scraping process, from fetching the main page to extracting and cleaning FAQ content in the category pages. The output is a JSON file with question-answer pairs.
data_handler.py: Scraped data is ingested, cleaned, chunked, vectorised and results stored in a ChromaDB collection in this module.
utils.py: Two things happen here as queries are converted into embeddings. Prompt building where a structured prompt for the LLM is constructed, and references are added to the response.
app.py: Main app that stitches everything together. Data retrieval, query processing, and response generation are integrated into a cohesive chatbot interface.
4. Deployment Considerations
This app could actually be deployed. This simply means moving the app to an environment where other users are able to test it. This can be done locally (on your computer) or on the cloud.

Local Deployment: The entire system can be run locally in a Python virtual environment. All you need is to clone the repository, setup the environment as per the instructions, and spin up the app. To be sincere, this is all you need to minimise on costs if you have a laptop.

Cloud Deployment: For scaling, containerization would be an idea. This can be done using Docker and deployed on cloud platforms such as AWS, GCP, or Azure. Just make sure that your variables, like OPENAI_API_KEY, etc., are securely managed using cloud secrets managers.

5. Conclusion
That should be it for now. I detailed the entire end-to-end process of building a RAG system. There are nuances to it, especially if the documents to be processed are of varied complexities compared to the straightforward FAQs that we had here. However, RAG fundamentals remain the sam





