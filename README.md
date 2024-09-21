## Description of the `linkLitToGbifId.ipynb`
# Script: Collect, Filter, and Process Cited Scientific Data

#### 1. **Objective**
To systematically collect, filter, and process cited scientific data from the Global Biodiversity Information Facility (GBIF) literature API. The focus is on obtaining literature that has cited GBIF data and ensuring only relevant and peer-reviewed sources are included.

#### 2. **Data Sources**
- **GBIF API:** The GBIF literature API provides access to a database of biodiversity-related literature thast cite GBIF. This includes journals, working papers, books, and book sections.

#### 3. **Python Libraries:**
  - `requests`: For making HTTP requests to the GBIF API.
  - `json`: For parsing and writing JSON data.
  - `tqdm`: For providing a progress bar during data retrieval.
  - `os`: For managing file and directory operations.
  - `zipfile`: For extracting and processing ZIP files.
  - `csv`: For handling CSV files.
  - `sys`: For adjusting system settings to handle large CSV files.
- **Storage:**
  - Local storage on the D: drive to manage large data files, including downloaded ZIP files, output CSVs, and error logs.

#### 4. **Procedure**

##### 4.1 **Define the API Endpoint and Parameters**
- **API Endpoint:** `https://api.gbif.org/v1/literature/search`
- **Parameters:**
  - `contentType`: Filters to "literature".
  - `literatureType`: Includes "JOURNAL", "WORKING_PAPER", "BOOK", and "BOOK_SECTION".
  - `relevance`: Filters literature that is "GBIF_CITED".
  - `peerReview`: Ensures only peer-reviewed literature is included.
  - `limit`: Sets the number of records per request to 10.
  - `offset`: Starts the search from the first record.

##### 4.2 **Data Collection**
1. **Fetch Initial Data:**
   - Make an initial API request to determine the total number of available records.
   - If the initial request fails or the count is unavailable, terminate the process.
2. **Iterative Data Retrieval:**
   - Continuously request data from the API in batches of 10 records.
   - Filter records that contain the `gbifDownloadKey`, indicating that the literature has associated GBIF data downloads.
   - Update the offset parameter to fetch the next batch until all data is retrieved.

##### 4.3 **Data Storage**
- Save the filtered records with `gbifDownloadKey` to a JSON file named `filtered_gbif_entries.json` for further processing.

##### 4.4 **Increase CSV Field Size Limit**
- Increase the field size limit for CSV processing to handle large entries, setting it to the maximum allowable size.

##### 4.5 **Loading and Saving Processed Records**
Because the files are so large it is likely that the process will be interupted and will have to be restarted. Hence the need for a skip file.
1. **Load Processed DOIs:**
   - Load the previously processed DOIs from a skip file to avoid reprocessing.
2. **Save Processed DOIs:**
   - Append each processed DOI to the skip file to keep track of completed entries.
3. **Load Downloaded Keys:**
   - Load previously downloaded keys to avoid duplicate downloads.

##### 4.6 **Data Download and Processing**
1. **Directory Management:**
   - Ensure that all necessary directories for storing downloads, logs, and outputs exist.
2. **Download Data:**
   - Download data files associated with each `gbifDownloadKey` and save them as ZIP files in the specified directory.
3. **Unzip and Extract:**
   - Unzip downloaded files and check for the presence of relevant data (e.g., `occurrence.txt` or CSV files).
4. **Filter and Save Relevant Data:**
   - Filter records for preserved specimens and append them to the output CSV file.
   - Include columns such as `gbifID`, `year`, `countryCode`, `gbifDownloadKey`, and `doi`.
5. **Error Handling:**
   - Log errors encountered during download or processing to an error log file for review.

##### 4.7 **Data Cleanup**
- After successful extraction and processing, delete the downloaded ZIP files and extracted contents to conserve storage space.

#### 5. **Outputs**
- **Filtered Entries File:** `filtered_gbif_entries.json` containing all relevant entries with `gbifDownloadKey`.
- **Output CSV File:** `output_data.csv` with filtered and processed data of preserved specimens.
- **Error Log File:** `error_log.txt` documenting any errors encountered during processing.

## Description of the `GBIFLitTopicsAnalysis.ipynb`
#Script: Topic Analysis and Network Visualization of GBIF Literature

#### 1. **Objective**
To analyze and visualize the thematic topics present in the literature that reference specimens in GBIF.
It uses the GBIF Literature API to extract topics associated with each Digital Object Identifier (DOI) and constructs a network graph representing the co-occurrence of these topics.

#### 2. **Materials and Tools**
- **Python Libraries:**
  - `requests`: For querying the GBIF Literature API.
  - `pandas`: For handling and processing CSV data.
  - `networkx`: For constructing and analyzing the topic co-occurrence network.
  - `matplotlib`: For visualizing the topic network graph.
  - `tqdm.notebook`: For providing progress bars during data processing.

- **Data Input:**
  - **CSV File (`allDOIs.csv`)**: A CSV file containing a list of DOIs that reference GBIF data. This file is used as the source for querying the literature API.

#### 3. **Procedure**

##### 3.1 **Data Acquisition**
- **Querying the GBIF Literature API:**
  - A function `query_gbif_literature(doi)` is defined to fetch literature data from the GBIF API using a given DOI.
  - The function returns the JSON response if the request is successful; otherwise, it prints an error message.

##### 3.2 **Data Extraction and Processing**
1. **Extract Topics:**
   - The function `extract_topics(data)` processes the API response to extract thematic topics associated with each literature entry.
   - Topics are extracted if they exist in the results; otherwise, a message is printed indicating the absence of topics.

2. **Read DOIs from CSV:**
   - The script reads the `allDOIs.csv` file using `pandas` to obtain a list of DOIs for further processing.

3. **Topic Count and Co-occurrence Analysis:**
   - The script iterates through each DOI, querying the GBIF API and extracting topics.
   - It maintains two dictionaries:
     - `topic_counts`: Tracks the frequency of each unique topic.
     - `topic_cooccurrences`: Tracks how often pairs of topics co-occur within the same literature entry.

##### 3.3 **Network Graph Construction**
1. **Create Network Graph:**
   - A network graph `G` is created using the `networkx` library.
   - **Nodes:** Represent unique topics. The size of each node is proportional to the count of the topic in the literature.
   - **Edges:** Represent co-occurrences between topics. The weight of each edge corresponds to the number of times the topics co-occurred.

2. **Add Nodes and Edges:**
   - Nodes are added to the graph with attributes such as size and count.
   - Edges are added between topics based on their co-occurrence frequency.

##### 3.4 **Network Graph Export**
- The constructed topic network is saved in the `GraphML` format (`topic_network.graphml`). This format allows for further analysis and visualization using various graph tools.

##### 3.5 **Network Visualization (Optional)**
1. **Visualize Network:**
   - The graph is visualized using `matplotlib`.
   - The size of each node in the visualization is scaled according to its count attribute.
   - Nodes are colored, and edges are drawn with varying thickness based on their weights.

2. **Plot Configuration:**
   - A spring layout is used to arrange the nodes for better visualization.
   - Node size, font size, and colors are customized for readability.

#### 4. **Outputs**
- **Network Graph File:** `topic_network.graphml` containing the constructed topic network with nodes and edges.
- **Visual Plot:** A visual representation of the topic network is optionally displayed, showing the structure and connections between topics.
