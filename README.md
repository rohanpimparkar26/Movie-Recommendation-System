Content-Based Movie Recommendation System

This project is a content-based movie recommendation system built in Python. It suggests movies to a user based on their similarity to a movie they have already watched. The core logic relies on Natural Language Processing (NLP) techniques—specifically TF-IDF (Term Frequency-Inverse Document Frequency) and Cosine Similarity—to find movies with similar plots, genres, cast, and crew.

This script is designed to run in a Google Colab environment, as it automatically downloads the required dataset.

Dataset

This system uses the TMDB 5000 Movie Dataset from Kaggle. It consists of two main files:

tmdb_5000_movies.csv: Contains high-level movie details like title, budget, genres, keywords, and overview.

tmdb_5000_credits.csv: Contains cast and crew information for each movie.

The script automatically downloads and merges these two files on their common id column to create a single, comprehensive DataFrame for analysis.

Project Workflow & How It Works

The project follows a clear, step-by-step machine learning pipeline:

1. Data Collection & Loading

The script uses !wget to download the movies and credits CSV files directly into the (Colab) environment.

The two files are loaded into pandas DataFrames.

Critical Fix: The credits.csv file's movie_id column is programmatically renamed to id to match the movies.csv file, ensuring a successful merge.

2. Data Preprocessing & Feature Engineering

This is the most crucial phase, where we clean and structure the data to be "understandable" by the model.

Handling Missing Data: The overview column (which contains the movie plot) has some missing values, which are filled with an empty string ''.

Parsing Stringified JSON: Features like genres, keywords, cast, and crew are stored as stringified JSON lists in the CSV. A helper function using Python's ast.literal_eval is used to safely parse these strings into actual Python lists.

Feature Extraction:

Cast: The top 3 actors are extracted from the cast list.

Director: The 'Director' is extracted from the crew list.

Genres & Keywords: All genres and keywords are extracted.

Token Cleaning: To ensure "Chris Evans" and "Chris Hemsworth" are treated as two distinct people (and not just "Chris"), all names and keywords have their spaces removed (e.g., chrisevans).

3. Creating the "Content Soup"

To perform content-based filtering, we combine all relevant text features into a single string (a "soup") for each movie. This soup represents the movie's entire content profile.

[keywords] + [cast] + [director] + [genres] + [overview]

A typical soup string might look like this:
"sciencefiction action chrisevans scarlettjohansson jamescameron ...[plot of the movie]..."

4. Vectorization (TF-IDF)

Machines cannot read text. The TfidfVectorizer from scikit-learn is used to convert the text-based "soup" column into a numerical matrix.

TF-IDF (Term Frequency-Inverse Document Frequency) analyzes this matrix. It gives a higher weight to words that are frequent in one movie's description but rare across all other movies.

For example, the word "magic" might have a high TF-IDF score for Harry Potter but a low score for The Dark Knight.

This process results in a large matrix where each row is a movie and each column is a unique word, with the cell value being the TF-IDF score.

5. Calculating Similarity (Cosine Similarity)

With all movies converted into numerical vectors, we can now calculate how "similar" they are.

Cosine Similarity is a metric used to measure the cosine of the angle between two vectors. In simple terms, it measures the similarity in orientation, not magnitude.

A score of 1 means the movies are identical in content.

A score of 0 means they are completely dissimilar.

We calculate the cosine similarity of every movie against every other movie, resulting in a large (4803 x 4803) similarity matrix.

6. The Recommendation Function

The final recommend(title) function performs these steps:

Takes a movie title as input.

Finds the index of that movie in our DataFrame.

Uses that index to get its row of scores from the cosine_sim matrix.

Sorts these scores in descending order, pairing them with their movie indices.

Retrieves the top 5 highest-scoring movies (skipping the first one, which is the movie itself).

Prints the titles of the 5 recommended movies.

How to Use

This script is built for Google Colab.

Create a new Colab notebook.

Copy the entire content of the movie_recommender_system.py file into a single code cell.

Run the cell.

The script will automatically download the data, process it, build the model, and print example recommendations at the end.

Example Output

Running the script will produce the following test cases at the bottom of the cell:

--- Running Demonstration Test Cases ---

--- Top 5 Recommendations for 'The Dark Knight Rises' ---
1. The Dark Knight (Similarity: 0.7042)
2. Batman Begins (Similarity: 0.6416)
3. Gangs of Wasseypur (Similarity: 0.5489)
4. The Prestige (Similarity: 0.5377)
5. Interstellar (Similarity: 0.5312)

--- Top 5 Recommendations for 'Avatar' ---
1. Alien (Similarity: 0.4851)
2. Aliens (Similarity: 0.4820)
3. Galaxy Quest (Similarity: 0.4770)
4. Moonraker (Similarity: 0.4700)
5. Space Cowboys (Similarity: 0.4687)

...and so on.
