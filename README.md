# rust-web-crawler
Programmer Information:
- **Name**: Morgan Bryant
- **GT ID**: 903680910
- **Email**: mbryant49@gatech.edu
- **Development Machine** MacBook Pro M4, 16gb RAM

### Key Features:
This project is both a webcrawler and search tool in one. Since it is written in `Rust`, it is as fast as possible on the raw computing side. Additionally, we use the `tokio` async package to crawl multiple web pages at once. It is intended to be ran as a CLI program only in its current state, but could have a UI implemented in the future. The main time cost is from the `politeness` wait call as to not overwhelm the servers. This call waits one second per url crawled.

### Analysis:
#### Data & Storage Design:
For each URL crawled, we store a formatted JSON with the URL & 20 keywords in the `data/meta` directory. We also store a compressed `(.gz)` version of the HTML pages in the `data/pages` directory. The JSON files are named with the URL hash to ensure no duplicates. Each keyword is chosen based on the frequency of it being stated in the HTML. When the program is finished, a CSV file, `crawled_data.csv`,  is created with the columns: `url, doc_id, keywords`. Everything is stored on the local disk in the `data` directory.
#### Pros:
- Host side is fast and effiecent in terms of CPU usage.
- Uses `tokio` async package to crawl multiple web pages simultaneously.
- Uses `serde` to serialize and deserialize data to and from CSV files.
- Fairly lightweight storage and memory foot print (depending on # of crawled URLS).
- Has an indexer/search function if trying to find certain keywords in the crawled data.
- Each URL has 20 keywords extracted from the text of the page.
- Has a `crawled_data.csv` file that contains the crawled data in a CSV format to improve utility. Such as viewing all data at once or importing into a spreadsheet software like MS Excel.
- The program is fairly modular and can be easily modified for a specific use case such as filtering out/in URLS or only capturing certain keywords.
#### Cons:
- Requires an understanding of `Rust` and `Cargo` to modify and tailor to a specific use case
- The `politeness` wait call is currently causing an increase in runtime.
   - This can be removed if the user is willing to risk being blocked by the server or is given explicit permission to crawl
- Certain URLS may cause errors and break the crawler.
- Certain websites may be skipped if they have a `robots.txt` file that disallows crawling or if a `robots.txt` file is not found.
- No GUI, as it is Command Line Interface (CLI) use only at this time.
- This is a student project, and may encounter other errors after more strenous use/testing.

#### Lessons Learned:
This project is the first one of this size that I have attempted in `Rust`. Albeit, I do think I did a fairly good job. This project has taught me how to use the `tokio` and `serde` packages more effectively. Previously, I have used them for my personal website & other projects. I also greatly over-estimated the storage footprint of the crawled data. Even though it is just single line JSON files & compressed HTML files, I assumed it would atleast be a few gigabytes, not just a mere few megabytes. Luckliy, through my own developement for my personal website, I have experence with `reqwest`, `tokio`, and `serde`. I have also greatly improved my comfortability with `Rust` and `Cargo` including using multiple `.rs` files to organize my code. Previously, I have only used `python` for web-crawling using prebuilt packages in a Kali-Linux environment. Git & Github have also helped me tremendously in developing this project as I have had to reset & rebuild several times due to various errors with my code & dependcies. 

I originally tried to use `Apache Lucene`, but found that I would rather write my own just for the practice using `Rust`.

Classes like CS-2340, CS-4400, Junior Design have all helped me with these large scale projects involving internet/API calls & data storage. 


### Project Structure:
- **Source Code**:
  - `src/crawler.rs`: Implements the crawling logic.
  - `src/query.rs`: Provides search functionality.
  - `src/storage.rs`: Manages storage of pages and metadata.
  - `src/parser.rs`: Extracts text and links from HTML.
  - `src/url_utils.rs`: Normalizes URLs.
  - `src/robots.rs`: Handles `robots.txt` rules.
  - `src/keywords.rs`: Extracts keywords from text.
  - `data/`: Main directory for storing crawled data.
  - `data/meta`: Contains the URLS & Keywords for crawled data.
  - `data/pages`: Contains `.gz` compressed version of the HTML pages
  - `data/crawled_data.csv`: Contains the crawled data in CSV format.
  - `target/release`: Contains the compiled **RELEASE** Binary
  - `target/debug`: Contains the compiled **DEBUG** Binary
  - `Cargo.toml`: Contains all cargo packages & other information used to compile the program.
  - `Cargo.lock`: Contains the lock file for the cargo packages used in the project to ensure that the same versions are used across compilations.

### Usage:
```bash
Filesystem-based crawler & search tool

Usage: fs_search_crawler [OPTIONS] --seed <SEED>

Options:
      --seed <SEED>              Seed URL to start crawling
      --output-dir <OUTPUT_DIR>  Directory to store pages and index [default: ./data]
      --max-pages <MAX_PAGES>    Maximum pages to crawl [default: 1000]
      --query <QUERY>            Query term (skip crawl if provided)
  -h, --help                     Print help
  -V, --version                  Print version
```
#### To Crawl:
```bash
./target/release/fs_search_crawler \
  --seed "https://example.com" \
  --output-dir "./data" \
  --max-pages 10
  ```
#### To Query (after crawling):
```bash
./target/release/fs_search_crawler \
  --seed "" \
  --output-dir "./data" \
  --query "example"
  ```
#### To Query & Export to CSV:
```bash
./target/release/fs_search_crawler \
  --seed "" \
  --output-dir "./data" \
  --query "example" > example.csv
```

### Benchmarks:
```bash
./target/release/fs_search_crawler \
  --seed "https://sandbox.oxylabs.io/h" \
  --output-dir "./data" \
  --max-pages 25000
  ```
Took about 1 hour to crawl 25,000 pages.

Storage Output:
- `crawled_data.csv`: 737 KB
- `data/meta`: 12.7 MB
- `data/pages`: 36.9 MB
- TOTAl: 49.6 MB

#### Predictions(*)
| URL COUNT | TIME (Days) | TIME (Hours) | TIME (MINUTES) | TIME (SECONDS) | SIZE      | KEYWORDS       |
|-----------|--------------|-------------|----------------|----------------|-----------|----------------|
| 1*        | 0            | 0           | 0              | 1              | 0.01 KB   | 20             |
| 10,000*   | 0            | 0.25        | 15.00          | 900            | 0.20 MB   | 200,000        |
| 25,000    | 1/24         | 1           | 60             | 3,600          | 50 MB     | 500,000        |
| 10 mill*  | 16.7         | 400         | 24,000         | 1,440,000      | 29.84 GB  | 200,000,000    | 
| 1 billion*| 1666.7       | 10,000      | 600,000        | 11,538,461     | 1.937 TB  | 20,000,000,000 |


### Images:
Beginning of the crawl:
![25k_Search](https://github.com/Morgan-Bryant/rust-web-crawler/blob/main/25k_Search.png)
Results of the crawl:
![Index_CSV](https://github.com/Morgan-Bryant/rust-web-crawler/blob/main/Index_CSV.png)
Query Call:
![Mario_Test_Query](https://github.com/Morgan-Bryant/rust-web-crawler/blob/main/Mario_Test_Query.png)
Query Results:
![ResultCSV_1](https://github.com/Morgan-Bryant/rust-web-crawler/blob/main/ResultCSV_1.png)
![ResultCSV_2](https://github.com/Morgan-Bryant/rust-web-crawler/blob/main/ResultCSV_2.png)

