# Movie Recommender System

This project is a movie recommendation system that suggests similar movies based on a user's selection. It uses content-based filtering, leveraging movie genres, keywords, and overviews to find similarities. The frontend is built with Streamlit, and it fetches movie plot and poster details from the OMDB API.

## Project Structure

```
.
├── src/
│   ├── app.py             # Main Streamlit application
│   ├── recommend.py       # Core recommendation logic
│   ├── preprocess.py      # Data preprocessing and model training
│   ├── omdb_utils.py      # Utility for fetching data from OMDB API
│   ├── movies.csv         # Dataset of movies
│   ├── config.json        # Configuration file (e.g., API keys)
│   ├── requirements.txt   # Python dependencies
│   ├── df_cleaned.pkl     # Saved preprocessed DataFrame
│   ├── tfidf_matrix.pkl   # Saved TF-IDF matrix
│   ├── cosine_sim.pkl     # Saved cosine similarity matrix
│   ├── preprocess.log     # Log file for preprocessing script
│   ├── recommend.log      # Log file for recommendation logic
│   └── .streamlit/
│       └── config.toml    # Streamlit configuration
└── training/
    └── movie_reco.ipynb   # Jupyter notebook for experimentation (if any)
```

## Setup

1.  **Clone the repository (if applicable)**
2.  **Create a virtual environment (recommended):**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows use `venv\Scripts\activate`
    ```
3.  **Install dependencies:**
    Navigate to the `src` directory and run:
    ````bash
    pip install -r requirements.txt
    ````
4.  **OMDB API Key:**
    You need an API key from [OMDB API](http://www.omdbapi.com/apikey.asp).
    Once you have the key, create a `config.json` file in the `src` directory with the following content:
    ````json
    {
        "OMDB_API_KEY": "YOUR_API_KEY"
    }
    ````
    Replace `"YOUR_API_KEY"` with your actual OMDB API key.

## Running the Application

1.  **Preprocess Data:**
    Before running the application for the first time, or if `movies.csv` has changed, you need to run the preprocessing script. Navigate to the `src` directory and execute:
    ````bash
    python preprocess.py
    ````
    This will generate `df_cleaned.pkl`, `tfidf_matrix.pkl`, and `cosine_sim.pkl`.

2.  **Run the Streamlit App:**
    Navigate to the `src` directory and run:
    ````bash
    streamlit run app.py
    ````
    This will open the application in your web browser.



## How it Works

1.  **Data Preprocessing ([`src/preprocess.py`](d:\Degree\MachineLearning\film-reco\src\preprocess.py)):**
    *   Loads movie data from [`src/movies.csv`](d:\Degree\MachineLearning\film-reco\src\movies.csv).
    *   Selects relevant columns (`genres`, `keywords`, `overview`, `title`).
    *   Combines these text fields into a single corpus for each movie.
    *   Cleans the text: removes special characters, converts to lowercase, tokenizes, and removes stop words using NLTK.
    *   Applies TF-IDF (Term Frequency-Inverse Document Frequency) vectorization to convert the cleaned text data into numerical vectors.
    *   Computes the cosine similarity matrix from the TF-IDF matrix to find the similarity score between all pairs of movies.
    *   Saves the cleaned DataFrame, TF-IDF matrix, and cosine similarity matrix to disk as `.pkl` files for later use.

2.  **Recommendation ([`src/recommend.py`](d:\Degree\MachineLearning\film-reco\src\recommend.py)):**
    *   Loads the preprocessed DataFrame (`df_cleaned.pkl`) and the cosine similarity matrix (`cosine_sim.pkl`).
    *   When a user selects a movie, the system finds its index in the DataFrame.
    *   It retrieves the similarity scores for that movie from the cosine similarity matrix.
    *   Sorts these scores in descending order to get the most similar movies.
    *   Returns the top N recommended movies.

3.  **User Interface ([`src/app.py`](d:\Degree\MachineLearning\film-reco\src\app.py)):**
    *   Uses Streamlit to create an interactive web interface.
    *   Populates a dropdown menu with movie titles from the loaded DataFrame.
    *   When the user selects a movie and clicks "Recommend", it calls the [`recommend_movies`](d:\Degree\MachineLearning\film-reco\src\recommend.py) function.
    *   For each recommended movie, it calls [`get_movie_details`](d:\Degree\MachineLearning\film-reco\src\omdb_utils.py) from [`src/omdb_utils.py`](d:\Degree\MachineLearning\film-reco\src\omdb_utils.py) to fetch the plot and poster image from the OMDB API using the API key from [`src/config.json`](d:\Degree\MachineLearning\film-reco\src\config.json).
    *   Displays the recommended movies along with their posters and plots.
