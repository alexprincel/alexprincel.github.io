# Introduction

**Context**

In the course of my work, I've frequently had to collect data from various sources only to have to store it again for later analysis. While there are many different tools and languages for this job and many different means to store the resulting data, my usual *modus operandi* is to use Python and store the resulting work in CSV files. I wanted to know if there are more efficient ways to perform this type of task using Python's `pandas` package, so I investigated and thought I'd share it here. In this context, I define efficient mostly by execution time, with bonus points for lower disk usage.

By the end of this article, I hope to be able to provide some guidance on how to answer the following two questions:

- What is the most efficient way to read from a file into a `pandas` dataframe ?
- What is the most efficient way to write a `pandas` dataframe to a file ?

*Note: During the writing process of this article, I have read about several other means of saving data to disk that may be more efficient and / or have other interesting features (e.g. disk space usage, execution time, etc). I have not covered those here but intend to do so in future articles.*

**Dataset used**

To perform comparisons, I have used the Kaggle powerlifting training dataset as it is slightly larger and has features with different data types. While this exercise is interesting and had some fruitful conclusions, you should be aware that this remains a contrived example with limitations, some of which include:

- no comparison of performance as the dataset size grows;
- no data point has very long values (e.g. big integers or strings with hundreds of characters);
- no feature is of either date, time or date time type.

Those limitations were not addressed in this current article but may be in subsequent ones.

# Comparing pandas I/O for usual data file types

In order to perform the comparison of I/O performance, I have first loaded the full dataset into a `pandas` dataframe and exported it to the following formats:

- Comma-separated values (CSV)
- JavaScript Object Notation (JSON)
- Hierarchical Data Format 5 (HDF5)
- Pickle (Python `pickle` object serialization module)

Each file type was then tested with the `testit` module for both read / write execution time over an average of five executions to smooth any bump in computer performance. 

The following visualization illustrates some clear winners and some clear losers as far as execution time is concerned. The full table follows immediately.

<img src="/images/art_01_results.svg">

---
Execution times
---
Read (ms) | Write (ms) | File Extension
:---: | :---: | :---:
1,102 | 5,460 | CSV
277 | 542 | HDF5
9,637 | 1,369 | JSON
176 | 370 | Pickle

While this exercise is probably unfair to JSON, given that its primary design purpose is not to store large tabular data but rather to allow for flexible and hierarchical data formats, it does distinguish itself clearly as non-viable for storing dataframe-type data.

CSV reading and writing, interestingly, doesn't fare as bad as JSON but is significantly slower than the same operations from / to HDF5 and Python pickle files. So far, performance-wise pickle comes out in first place followed very closely by HDF5.


Other considerations
---
While speed is definitely a significant factor when data set sizes increase, other considerations may come into play.

- **(Human) Readability:** By design, CSV and JSON are human-readable. This can bring interesting benefits, notably as the ability to peek at the contents using a text editor.

- **Familiarity:** While one shouldn't always refrain from using optimal solutions to account for varying levels of technological knowledge, it may sometimes make sense to use a data format that is more well-known as it allows others to make use of it without additional training and is more likely to behave well with other tools and systems used in-house.

- **Portability:** Here, not OS-wise but rather system or tool-wise. Admittedly, `pickle` is the reason for this bullet. While very efficient speed-wise, to my knowledge it is Python-specific and is not guaranteed to behave well even between versions of Python itself.

- **Security:** Again, this item is geared towards `pickle`. There are security considerations with regards to unpickling malicious pickle files. In applications where there may be files coming from external sources, this may be a deal breaker. For other applications, this may be a non-issue.

# Conclusion

Definitely, this article only scratches the surface of different Python file I/O techniques when working with dataframes. Some use cases may open up certain possibilities not available in other cases (e.g. because of data size or data types) and there are a host of packages and tools for this job that where unexplored. I'll briefly mention Dask here, which I haven't extensively tested but that delivers promising results efficiency-wise. Also, even within the parameters explored in this article, some techniques were not used here that may be used to positively affect the loading speed, such as specifying data types up-front.

However, with the parameters that I have tested here, it seems like HDF5 is a reasonable data format for efficient I/O on dataframes and it is one I will definitely consider in the future.

Hopefully, this article will help some to open up to other data formats and/or give them a bit more thought.

