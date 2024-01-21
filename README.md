# Hangman-Transformer-Ngram
## Description
Hangman is a word-guessing game. The game randomly selects a secret word. The player keeps guessing letters in the word. If the player correctly guesses a letter that is in the word, all instances of the letter will show up in the word. Otherwise, the player has an incorrect letter guess. The player has 6 chances to guess incorrectly. If the player can guess the word before using up the 6 chances, they has a success. 

This repo applies two models, a Transformer BERT model and a N-gram model, to play the Hangman game. The N-gram model can reach 63.6% success rate. 

## Algorithm Description
### N-gram Model
I am using N-gram model to calculate the probability of a missing letter in a word. 

Steps:

a.	I build a dictionary. The first level key is the length of the n-gram, n. The second level key is the gram. The value is the count of the gram. 

b.	During the guess, I try to find out the probability of 26 letters, excluding the guessed one, conditioned on the current guessed word and return the letter with the highest probability.

c.	I scan the current guessed word with decreasing length of n-gram. 

d.	In the scanning window, if only one letter is missing, then calculate the probability of all available letters that could be this missing letter. 

e.	The probability of letter x is calculated as the count of n-gram with letter x divided by the count of n-gram with any letter.

f.	The probability of each available letter is calculated at each length n and weighted summed up. The weight aims to give more to probability inferred from longer gram and less to that from shorter gram. 

### BERT Model
BERT model is using a bidirectional training of Transformer to learn a masked word by its context on its left and right. Similarly, my task is to learn a missing character by its context of a word. 

Steps:

a.	Firstly, I transfer every word into a vector representing by ids of letters.

b.	I add special letter/token “.” as the mask token to be guessed.

c.	Using the mask method from BERT, I randomly masked 15% of the letters. 80% of them are masked by “.”; 10% of them are replaced by its true value; 10% of them are replaced by a random value.

d.	The masked sample is my input, the original sample is my output. 

e.	Position encoding is applied to identify letters’ position in a word.

f.	The BERT model contains Multi-head attention layer. Dropout of 0.1 is applied to avoid over-fitting. Layer normalization is applied to stabilize the learning process. 

g.	To train the model, l am using cross entropy loss function and Adam optimizer. I pick a relatively small batch size to improve the fitting ability. 

h.	After model is trained, I used it to predict a word’s missing letter. The model outputs the probability of any letter at any missing position. The letter with the largest probability and within the available letter set is picked.

## Reference
https://en.wikipedia.org/wiki/Hangman_(game)

https://keras.io/examples/nlp/masked_language_modeling/

https://github.com/ZavierYang/N-gram-model-for-Hangman-game

https://github.com/mattgalarneau/Hangman-NLP
