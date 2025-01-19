# datamuse-cli

A command line interface for the Datamuse API.

## Installation

```bash
pip install datamuse-cli
```

## Datamuse API page

https://www.datamuse.com/api/

```
What is it?
The Datamuse API is a word-finding query engine for developers. You can use it in your apps to find words that match a given set of constraints and that are likely in a given context. You can specify a wide variety of constraints on meaning, spelling, sound, and vocabulary in your queries, in any combination.

What is it good for?
Applications use the API for a wide range of features, including autocomplete on text input fields, search relevancy ranking, assistive writing apps, word games, and more. The following examples illustrate the kinds of queries you can make:

In order to find...	...use https://api.datamuse.com…
words with a meaning similar to ringing in the ears	/words?ml=ringing+in+the+ears
words related to duck that start with the letter b	/words?ml=duck&sp=b*
words related to spoon that end with the letter a	/words?ml=spoon&sp=*a
words that sound like jirraf	/words?sl=jirraf
words that start with t, end in k, and have two letters in between	/words?sp=t??k
words that are spelled similarly to hipopatamus	/words?sp=hipopatamus
adjectives that are often used to describe ocean	/words?rel_jjb=ocean
adjectives describing ocean sorted by how related they are to temperature	/words?rel_jjb=ocean&topics=temperature
nouns that are often described by the adjective yellow	/words?rel_jja=yellow
words that often follow "drink" in a sentence, that start with the letter w	/words?lc=drink&sp=w*
words that are triggered by (strongly associated with) the word "cow"	/words?rel_trg=cow
suggestions for the user if they have typed in rawand so far	/sug?s=rawand

How can I use it?
You can access most of the features of the API at the URL api.datamuse.com/words, with the query parameters described below. An additional endpoint, api.datamuse.com/sug, is useful as a backend for an autocomplete function on search input fields. This is a strictly read-only service and an API token is not required. The service supports both HTTP and HTTPS requests.


/words endpoint
This endpoint returns a list of words (and multiword expressions) from a given vocabulary that match a given set of constraints.

In the table below, the first four parameters (rd, sl, sp, rel_[code], and v) can be thought of as hard constraints on the result set, while the next three (topics, lc, and rc) can be thought of as context hints. The latter only impact the order in which results are returned. All parameters are optional.

Query parameters
ml	Means like constraint: require that the results have a meaning related to this string value, which can be any word or sequence of words. (This is effectively the reverse dictionary feature of OneLook.)
sl	Sounds like constraint: require that the results are pronounced similarly to this string of characters. (If the string of characters doesn't have a known pronunciation, the system will make its best guess using a text-to-phonemes algorithm.)
sp	Spelled like constraint: require that the results are spelled similarly to this string of characters, or that they match this wildcard pattern. A pattern can include any combination of alphanumeric characters and the symbols described on that page. The most commonly used symbols are * (a placeholder for any number of characters) and ? (a placeholder for exactly one character). Please be sure that your parameters are properly URL encoded when you form your request.
rel_[code]	Related word constraints: require that the results, when paired with the word in this parameter, are in a predefined lexical relation indicated by [code]. Any number of these parameters may be specified any number of times. An assortment of semantic, phonetic, and corpus-statistics-based relations are available. At this time, these relations are available for English-language vocabularies only.

[code] is a three-letter identifier from the list below.
[code]	Description	Example
jja	Popular nouns modified by the given adjective, per Google Books Ngrams	gradual → increase
jjb	Popular adjectives used to modify the given noun, per Google Books Ngrams	beach → sandy
syn	Synonyms (words contained within the same WordNet synset)	ocean → sea
trg	"Triggers" (words that are statistically associated with the query word in the same piece of text.)	cow → milking
ant	Antonyms (per WordNet)	late → early
spc	"Kind of" (direct hypernyms, per WordNet)	gondola → boat
gen	"More general than" (direct hyponyms, per WordNet)	boat → gondola
com	"Comprises" (direct holonyms, per WordNet)	car → accelerator
par	"Part of" (direct meronyms, per WordNet)	trunk → tree
bga	Frequent followers (w′ such that P(w′|w) ≥ 0.001, per Google Books Ngrams)	wreak → havoc
bgb	Frequent predecessors (w′ such that P(w|w′) ≥ 0.001, per Google Books Ngrams)	havoc → wreak
hom	Homophones (sound-alike words)	course → coarse
cns	Consonant match	sample → simple
v	Identifier for the vocabulary to use. If none is provided, a 550,000-term vocabulary of English words and multiword expressions is used. (The value es specifies a 500,000-term vocabulary of words from Spanish-language books. The value enwiki specifies an approximately 6 million-term vocabulary of article titles from the English-language Wikipedia, updated monthly.) Please contact us to set up a custom vocabulary for your application.
topics	Topic words: An optional hint to the system about the theme of the document being written. Results will be skewed toward these topics. At most 5 words can be specified. Space or comma delimited. Nouns work best.
lc	Left context: An optional hint to the system about the word that appears immediately to the left of the target word in a sentence. (At this time, only a single word may be specified.)
rc	Right context: An optional hint to the system about the word that appears immediately to the right of the target word in a sentence. (At this time, only a single word may be specified.)
max	Maximum number of results to return, not to exceed 1000. (default: 100)
md	Metadata flags: A list of single-letter codes (no delimiter) requesting that extra lexical knowledge be included with the results. The available metadata codes are as follows:
Letter	Description	Implementation notes
d	Definitions	Produced in the defs field of the result object. The definitions are from Wiktionary and WordNet. If the word is an inflected form (such as the plural of a noun or a conjugated form of a verb), then an additional defHeadword field will be added indicating the base form from which the definitions are drawn.
p	Parts of speech	One or more part-of-speech codes will be added to the tags field of the result object. "n" means noun, "v" means verb, "adj" means adjective, "adv" means adverb, and "u" means that the part of speech is none of these or cannot be determined. Multiple entries will be added when the word's part of speech is ambiguous, with the most popular part of speech listed first. This field is derived from an analysis of Google Books Ngrams data.
s	Syllable count	Produced in the numSyllables field of the result object. In certain cases the number of syllables may be ambiguous, in which case the system's best guess is chosen based on the entire query.
r	Pronunciation	Produced in the tags field of the result object, prefixed by "pron:". This is the system's best guess for the pronunciation of the word or phrase. The format of the pronunication is a space-delimited list of Arpabet phoneme codes. If you add "&ipa=1" to your API query, the pronunciation string will instead use the International Phonetic Alphabet. Note that for terms that are very rare or outside of the vocabulary, the pronunciation will be guessed based on the spelling. In certain cases the pronunciation may be ambiguous, in which case the system's best guess is chosen based on the entire query.
f	Word frequency	Produced in the tags field of the result object, prefixed by "f:". The value is the number of times the word (or multi-word phrase) occurs per million words of English text according to Google Books Ngrams.
The API makes an effort to ensure that metadata values are consistent with the sense or senses of the word that best match the API query. For example, the word "refuse" is tagged as a verb ("v") in the results of a search for words related to "deny" but as a noun ("n") in the results of a search for words related to "trash". And "resume" is shown to have 2 syllables in a search of synonyms for "start" but 3 syllables in a search of synonyms for "dossier". There are occasional errors in this guesswork, particularly with pronunciations. Metadata is available for both English (default) and Spanish (v=es) vocabularies.
qe	Query echo: The presence of this parameter asks the system to prepend a result to the output that describes the query string from some other parameter, specified as the argument value. This is useful for looking up metadata about specific words. For example, /words?sp=flower&qe=sp&md=fr can be used to get the pronunciation and word frequency for flower.


/sug endpoint
This resource is useful as a backend for “autocomplete” widgets on websites and apps when the vocabulary of possible search terms is very large. It provides word suggestions given a partially-entered query using a combination of the operations described in the “/words” resource above. The suggestions perform live spelling correction and intelligently fall back to choices that are phonetically or semantically similar when an exact prefix match can't be found. Here is a Wikipedia search box that demonstrates this endpoint in action:   

The endpoint produces JSON output similar to the /words resource and is suitable for widgets such as JQuery Autocomplete, used in the above demo.

Query parameters
s	Prefix hint string; typically, the characters that the user has entered so far into a search box. (Note: The results are sorted by a measure of popularity. The results may include spell-corrections of the prefix hint or semantically similar terms when exact matches cannot be found; that is to say, the prefix hint will not necessarily form a prefix of each result.)
max	Maximum number of results to return, not to exceed 1000. (default: 10)
v	Identifier for the vocabulary to use. Equivalent to the v parameter in /words.

Interpreting the results
For both /words and /sug, the result of an API call is always a JSON list of word objects, like so:

$ curl "https://api.datamuse.com/words?ml=ringing+in+the+ears&max=4" | python -mjson.tool
[  
   {  
      "word":"tinnitus",
      "score":57312
   },
   {  
      "word":"ring",
      "score":50952
   },
   {  
      "word":"cinchonism",
      "score":50552
   },
   {  
      "word":"acouasm",
      "score":48952
   }
]
Each list item is an object that contains the matching vocabulary entry ("word") and some metadata, currently just an integer score. An empty list ([]) will be returned if no words or phrases are found that match your constraints. Note that popular multiword expressions like "hot dog" are included in the default vocabulary, and these will appear as space-delimited strings.

For queries that have a semantic constraint, results are ordered by an estimate of the strength of the relationship, most to least. Otherwise, queries are ranked by an estimate of the popularity of the word in written text, most to least. At this time, the "score" field has no interpretable meaning, other than as a way to rank the results.
```