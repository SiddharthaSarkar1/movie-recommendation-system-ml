# Movie Recommendation System

This project is a content-based movie recommendation system that suggests similar movies based on their plot, genre, and tagline. It leverages Natural Language Processing (NLP) with TF-IDF vectorization and provides recommendations through a REST API built with FastAPI. The system is also integrated with The Movie Database (TMDB) to fetch rich movie metadata like posters, ratings, and release dates.

## Features

- **Content-Based Recommendations:** Uses TF-IDF to analyze movie content (overview, genres, tagline) and find similar movies.
- **Hybrid Approach:** Combines local TF-IDF data with real-time movie information from the TMDB API.
- **RESTful API:** A robust API built with FastAPI to serve movie recommendations and details.
- **Multiple Recommendation Types:**
  - TF-IDF similarity based on text content.
  - Genre-based recommendations.
- **Dockerized:** Comes with a `Dockerfile` for easy setup, deployment, and scalability.

## How It Works

The recommendation engine is built in two main stages: offline processing and online serving.

### 1. Offline Processing (Data Preparation & Model Training)

The `notebooks/movie_recommendation_system.ipynb` notebook handles the entire offline process:

1.  **Data Loading:** The initial dataset (`movies_metadata.csv`) is loaded.
2.  **Data Cleaning:** Unnecessary columns are dropped, and null values in critical fields like `title` and `overview` are handled.
3.  **Feature Engineering:** A new feature `tags` is created by combining the `overview`, `genres`, and `tagline` of each movie. This consolidated text field serves as the basis for our content analysis.
4.  **Text Preprocessing:** The `tags` text is cleaned by converting it to lowercase, removing stop words, and applying lemmatization to normalize the words.
5.  **Vectorization:** A TF-IDF (Term Frequency-Inverse Document Frequency) matrix is created from the preprocessed `tags`. This matrix represents each movie as a numerical vector, capturing the importance of each word in the context of all movies.
6.  **Saving Artifacts:** The processed DataFrame (`df.pkl`), TF-IDF vectorizer (`tfidf.pkl`), TF-IDF matrix (`tfidf_matrix.pkl`), and a title-to-index mapping (`indices.pkl`) are saved as pickle files.

### 2. Online Serving (FastAPI Application)

The `main.py` script sets up a FastAPI server to provide recommendations:

1.  **Loading Artifacts:** On startup, the API loads the pre-computed pickle files into memory.
2.  **API Endpoints:** It exposes several endpoints to interact with the recommendation model and the TMDB API.
3.  **Recommendation Logic:** When a user requests a recommendation for a movie, the API computes the cosine similarity between that movie's TF-IDF vector and all other movies in the dataset. It then returns the movies with the highest similarity scores.
4.  **TMDB Integration:** The API fetches additional data (like posters, ratings, and genre-based suggestions) from TMDB to enrich the user experience.

## API Endpoints

The following are the primary endpoints available:

-   `GET /health`: Health check endpoint.
-   `GET /home`: Fetches a list of movies for the home screen (e.g., popular, trending).
-   `GET /movie/search?query={movie_title}`: Searches for a movie on TMDB and returns details, TF-IDF recommendations, and genre-based recommendations.
-   `GET /recommend/tfidf?title={movie_title}`: Gets content-based movie recommendations for a given title.
-   `GET /recommend/genre?tmdb_id={tmdb_id}`: Gets genre-based recommendations for a given TMDB movie ID.
-   `GET /movie/id/{tmdb_id}`: Fetches detailed information for a specific movie from TMDB.

## Setup and Usage

### Prerequisites

-   Python 3.8+
-   Docker
-   A TMDB API Key

### 1. Environment Variables

Create a `.env` file in the `movie-recommendation-system` directory and add your TMDB API key:
