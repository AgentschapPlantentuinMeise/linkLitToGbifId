# linkLitToGbifId

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
