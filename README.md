distance - Utilities for comparing sequences
===============================================

This package provides helpers for computing similarities between arbitrary sequences. Included metrics are Levenshtein, Hamming, Jaccard, and Sorensen distance, plus some bonuses. All distance computations are implemented in pure Python, and most of them are also implemented in C.


Installation
------------

If you don't want or need to use the C extension, just unpack the archive and run, as root:

	# python setup.py install

For the C extension to work, you need the Python source files, and a C compiler (typically Microsoft Visual C++ 2010 on Windows, and GCC on Mac and Linux). On a Debian-like system, you can get all of these with:

	# apt-get install gcc pythonX.X-dev

where X.X is the number of your Python version.

Then you should type:

	# python setup.py install --with-c

Note the use of the `--with-c` switch.


Usage
-----

A common use case for this module is to compare single words for similarity:

	>>> distance.levenshtein("lenvestein", "levenshtein")
	3
	>>> distance.hamming("hamming", "hamning")
	1

If there is not a one-to-one mapping between sounds and glyphs in your language, or if you want to compare not glyphs, but syllables or phonems, you can pass in tuples of characters:

	>>> t1 = ("de", "ci", "si", "ve")
	>>> t2 = ("de", "ri", "si", "ve")
	>>> distance.levenshtein(t1, t2)
	1

Comparing lists of strings can also be useful for computing similarities between sentences, paragraphs, etc.:

	>>> sent1 = ['the', 'quick', 'brown', 'fox', 'jumps', 'over', 'the', 'lazy', 'dog']
	>>> sent2 = ['the', 'lazy', 'fox', 'jumps', 'over', 'the', 'crazy', 'dog']
	>>> distance.levenshtein(sent1, sent2)
	3

Hamming and Levenshtein distance can be normalized, so that the results of several distance measures can be meaningfully compared. Two strategies are available for Levenshtein: either the length of the shortest alignment between the sequences is taken as factor, or the length of the longer one. Example uses:

	>>> distance.hamming("fat", "cat", normalized=True)
	0.3333333333333333
	>>> distance.nlevenshtein("abc", "acd", method=1)  # shortest alignment
	0.6666666666666666
	>>> distance.nlevenshtein("abc", "acd", method=2)  # longest alignment
	0.5

`jaccard` and `sorensen` return a normalized value per default:

	>>> distance.sorensen("decide", "resize")
	0.5555555555555556
	>>> distance.jaccard("decide", "resize")
	0.7142857142857143

As for the bonuses, there is a `fast_comp` function, which computes the distance between two strings up to a value of 2 included. If the distance between the strings is higher than that, -1 is returned. This function is of limited use, but on the other hand it is quite faster than `levenshtein`. There is also a `lcsubstrings` function which can be used to find the longest common substrings in two sequences.

Finally, two convenience iterators `ilevenshtein` and `ifast_comp` are provided, which are intended to be used for filtering from a long list of sequences the ones that are close to a reference one. They both return a series of tuples (distance, sequence). Example:

	>>> tokens = ["fo", "bar", "foob", "foo", "fooba", "foobar"]
	>>> sorted(distance.ifast_comp("foo", tokens))
	[(0, 'foo'), (1, 'fo'), (1, 'foob'), (2, 'fooba')]
	>>> sorted(distance.ilevenshtein("foo", tokens, max_dist=1))
	[(0, 'foo'), (1, 'fo'), (1, 'foob')]

`ifast_comp` is particularly efficient, and can handle 1 million tokens without a problem.

For more informations, see the functions documentation (`help(funcname)`).

Have fun!


Changelog
---------

20/11/13:
* Switched back to using the to-be-deprecated Python unicode api. Good news is that this makes the
C extension compatible with Python 2.7+, and that distance computations on unicode strings is now
much faster.
* Added a C version of `lcsubstrings`.
* Added a new method for computing normalized Levenshtein distance.
* Added some tests.

12/11/13:
Expanded `fast_comp` (formerly `quick_levenshtein`) so that it can handle transpositions.
Fixed variable interversions in (C) `levenshtein` which produced sometimes strange results.

10/11/13:
Added `quick_levenshtein` and `iquick_levenshtein`.

05/11/13:
Added Sorensen and Jaccard metrics, fixed memory issue in Levenshtein.
