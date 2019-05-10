# Summary

The German UD is converted from the content head version of the [universal
dependency treebank v2.0 (legacy)](https://github.com/ryanmcd/uni-dep-tb).


# Introduction

The German UD conforms to the UD guidelines, but there are some exceptions.

# Morphology in release 2.0

Universal POS tags were assigned manually, while LEMMA and XPOSTAG were
predicted by TreeTagger (first for release 1.4; see Changelog below).
Morphological features were assigned using rules based on the values of the
other columns (UPOSTAG, XPOSTAG, LEMMA, FORM, DEPREL). Gender, number and
case of nouns and their det/amod children are based on the (manual) syntactic
annotation, e.g. nsubj => nominative. They should have high precision but
lower recall because we did not add them where the context did not provide
enough clues (morphological analyzer / lexicon was not used at this stage).

# Morphology in release 2.2

The XPOS and FEATS have been updated using the mate parser with Tueba-D/Z. The mate parser model was trained on the first 80% of Tueba-D/Z as converted by an updated version of the TuebaUdConverter that improved the morphological tag extraction. Tueba-D/Z tokens containing hyphens were split into multiple tokens to align with the UD tokenization.

During tagging, `` '' and -- were temporarily normalized to " and - and some new spellings were normalized back to old spellings to be compatible with the Tueba-D/Z model. Tueba-D/Z is fairly homogenous and somewhat dated, so the tagger does not perform as well on user-generated content as on older newspaper text. A detailed analysis is below.

After incorporating the automatic mate POSTAG and FEATS, the UD sentences were aligned with Tiger using the mapping in #13 and LEMMA (for content words) and POSTAG and FEATS (for all tokens) were updated with gold values from Tiger. The only exception the feature Voice, whose value is passed through from mate since it's not annotated on the aux in Tiger.

Ordinal numbers split into two tokens in UD (e.g., "3 .") were rejoined as in Tiger and compounds were reanalyzed in order to provide consistent FEATS for all subparts. If any subpart of a compound was tagged as NE by the mate parser, the deprel flat was used, otherwise compound.

Mate model analysis:

The accuracy on Tueba-D/Z test data for XPOSTAG is 98.3% and for FEATS as an unanalyzed string is 91.7%. Analyzing the individual morphological tags, the results are:

           Acc.    Prec. Rec.  F1
Case       0.95580 0.996 0.911 0.952
Definite   0.99653 0.986 0.987 0.986
Foreign    0.99835 0.646 0.714 0.678
Gender     0.96150 0.982 0.929 0.955
Mood       0.99554 0.984 0.959 0.972
NumType    0.99988 0.991 0.998 0.995
Number     0.97852 0.995 0.966 0.980
Person     0.99481 0.986 0.966 0.976
Polarity   0.99998 0.999 0.999 0.999
Poss       0.99993 0.995 0.997 0.996
PronType   0.99734 0.998 0.988 0.993
Reflex     0.99991 0.996 0.992 0.994
Tense      0.99567 0.983 0.962 0.972
VerbForm   0.99492 0.993 0.965 0.978
Voice      0.99934 0.934 0.967 0.950

Case and Gender are not unexpectedly the least accurate of the frequent features. Foreign could potentially be removed, although when inspecting the UD instances the precision seems relatively high.

# Changelog

2019-05-15 Dan Zeman
  * Fixed numerous bugs found by the new validator.

2018-11-15 Dan Zeman
  * Fixed punctuation using udapy -s ud.FixPunct

2018-04-15 Adriane Boyd
  * Fixed morphology (retagged with Mate, see above), re-synchronized with original Tiger data where applicable.

2017-11-20 Dan Zeman
  * Fixed: copula is AUX.
  * Fixed: capitalization of multi-word tokens at sentence beginning.

2017-04-13 Dan Zeman

Removed duplicate sentences from the training data. They were too long to believe that they were naturally
occurring duplicates.

2017-03-01 v2.0
  * Converted to UD v2 guidelines (Dan Zeman)

2016-08-21 Dan Zeman

Added sentence ids.
Added LEMMA and XPOSTAG predicted by TreeTagger with a German model supplied with the tagger and available in Treex
(http://ufal.mff.cuni.cz/treex, commit 50ad1fe0b9907ac382cbcda0a0f102602abc21a0). The UPOSTAGs from the original data
(assigned manually) were not modified. Some features were also added if they could be derived from the information
already present. Features that need a lexicon and/or disambiguation, such as Gender, Number and Case, have only been
added if they can be deduced from the (manually annotated) dependency structure, plus a few heuristics (e.g. form
equal to lemma often but not always means singular).

The work was done mainly using the HamleDT::DE::FixUD block, see
https://github.com/ufal/treex/blob/master/lib/Treex/Block/HamleDT/DE/FixUD.pm

2015-11-08 Wolfgang Seeker

Removed sentences from test due to overlap with dev
(sent-no. 6, 8, 79, 80, 88, 108, 109, 118, 152, 154, 164, 167, 190, 191, 195, 206, 215, 220, 229, 247, 295, 346, 451)
Removed sentences from dev due to overlap with train
(sent-no. 616)



###############################################################################
LEGACY README FILE BELOW
###############################################################################

===================================
Universal Dependency Treebanks v2.0
===================================

This directory contains treebanks for the following languages:
  English, German, French, Spanish, Swedish, Korean, Japanese, Indonesian,
  Brazilian Portuguese, Italian and Finnish.

A description of how the treebanks were generated can be found in:

  Universal Dependency Annotation for Multilingual Parsing
  Ryan McDonald, Joakim Nivre, Yvonne Quirmbach-Brundage, Yoav Goldberg,
  Dipanjan Das, Kuzman Ganchev, Keith Hall, Slav Petrov, Hao Zhang,
  Oscar Tackstrom, Claudia Bedini, Nuria Bertomeu Castello and Jungmee Lee
  Proceedings of ACL 2013

A more detailed description of each relation type in our harmonized scheme is
included in the file universal-guidelines.pdf.

Each treebank has been split into training, development and testing files.

Each file is formatted according to the CoNLL 2006/2007 guidelines:

  http://ilk.uvt.nl/conll/#dataformat

The treebank annotations use basic Stanford Style dependencies, modified
minimally to be sufficient for each language and be maximally consistent across
languages. The original English Stanford guidelines can be found here:

  http://nlp.stanford.edu/software/dependencies_manual.pdf


==============================================================================
Version 2.0 - What is new
==============================================================================

1. Added more data for German, Spanish and French.
2. Added Portuguese Brazilian, Indonesian, Japanese, Italian and Finnish.
3. New content-head versions for 5 languages (see below).
4. A number of bug fixes in the harmonization process.

=====================
Standard dependencies
=====================

In release 2.0 we include two sets of dependencies. The first is standard
Stanford dependencies, which correspond roughly to the output of the
Stanford converter for English with the copula as head set to true. In
general, these are content-head dependency representations with two major
exceptions: 1) adpositions are the head in adpositional phrases, and 2) copular
verbs are the head in copluar constructions.

This data is in the std/ directory and contains all languages but Finnish.

Version 1.0 of the data is only standard.

==========================
Content head dependencies
==========================

In order to converge to a more uniform multilingual standard, in particular
for morphologically rich languages, this release also includes a beta version
of content-head dependencies for five languages: German, Spanish, Finnish,
French and Swedish. Here the content word is always the head of a phrase.

=============================================================================
Language Specific Information
=============================================================================

====================
English dependencies
====================

Note that the English dependencies are based on the original Penn Treebank data
automatically converted with the Stanford Dependency Converter. Instructions for
how to do this with corresponding scripts are included in the English directory.

====================
Finnish dependencies
====================

Finnish data is in the ch/fi directory and was produced by researchers at
the University of Turku. In that directory there are specific README and
LICENSE files for that data. Two things to note. First, the Finnish data is
only content-head. This is due to difficulties in automatically converting the
data to standard format from its original annotations. Second, we have included
a test set in the release, but this is not the real test set, just a subset of
the training. The true test set for this data is blind (as per the wishes of
the researchers at Turku). The unannotated test data is included as well as
instructions for obtaining scores on predictions.

=============================================================================
Other Information
=============================================================================

================================
Fine-grained part-of-speech tags
================================

In the CoNLL file format there is a coarse part-of-speech tag field (4) and a
fine-grained part-of-speech tag field (5). In this data release, we use the
coarse field to store the normalized universal part-of-speech tags that are
consistent across languages. The fine-grained field contains potentially richer
part-of-speech information depending on the language, e.g., a richer tag
representation for clitics.

=========================
Licenses and terms-of-use
=========================

For the following languages

  German, Spanish, French, Indonesian, Italian, Japanese, Korean and Brazilian
  Portuguese

we will distinguish between two portions of the data.

1. The underlying text for sentences that were annotated. This data Google
   asserts no ownership over and no copyright over. Some or all of these
   sentences may be copyrighted in some jurisdictions.  Where copyrighted,
   Google collected these sentences under exceptions to copyright or implied
   license rights.  GOOGLE MAKES THEM AVAILABLE TO YOU 'AS IS', WITHOUT ANY
   WARRANTY OF ANY KIND, WHETHER EXPRESS OR IMPLIED.

2. The annotations -- part-of-speech tags and dependency annotations. These are
   made available under a CC BY-NC-SA 3.0 non commercial license. GOOGLE MAKES
   THEM AVAILABLE TO YOU 'AS IS', WITHOUT ANY WARRANTY OF ANY KIND, WHETHER
   EXPRESS OR IMPLIED. See attached LICENSE file for the text of CC BY-NC-SA.

Portions of the German data were sampled from the CoNLL 2006 Tiger Treebank
data. Hans Uszkoreit graciously gave permission to use the underlying
sentences in this data as part of this release.

For English, Italian, Finnish and Swedish, please see licences included in
these directories or the following sources.

Finnish - http://bionlp.utu.fi/fintreebank.html
Swedish - http://stp.lingfil.uu.se/~nivre/swedish_treebank/
Italian - http://medialab.di.unipi.it/wiki/ISDT

We are greatful to researchers at those institutes who provided us data, in
particular:

Maria Simi and company from the University of Pisa.
  Converting Italian Treebanks: Towards an Italian Stanford Dependency Treebank
  Bosco, Cristina and Montemagni, Simonetta and Simi, Maria
  Proceedings of LAW VII \& ID

Filip Ginter and company from the University of Turku.
  Building the essential resources for Finnish: the Turku Dependency Treebank
  Haverinen, Katri and Nyblom, Jenna and Viljanen, Timo and Laippala,
  Veronika and Kohonen, Samuel and Missil{\"a}, Anna and Ojala, Stina and
  Salakoski, Tapio and Ginter, Filip
  Language Resources and Evaluation, 2013

Joakim Nivre and company from Uppsala University.

And Chris Manning and Marie-Catherine de Marneffe from Stanford and Ohio.
  Generating typed dependency parses from phrase structure parses
  MC De Marneffe, B MacCartney, CD Manning,
  Proceedings of LREC, 2006

Any use of the data should reference the above plus:

  Universal Dependency Annotation for Multilingual Parsing
  Ryan McDonald, Joakim Nivre, Yvonne Quirmbach-Brundage, Yoav Goldberg,
  Dipanjan Das, Kuzman Ganchev, Keith Hall, Slav Petrov, Hao Zhang,
  Oscar Tackstrom, Claudia Bedini, Nuria Bertomeu Castello and Jungmee Lee
  Proceedings of ACL 2013

=======
Contact
=======

ryanmcd@google.com
joakim.nivre@lingfil.uu.se
slav@google.com


=== Machine-readable metadata (DO NOT REMOVE!) ================================
Data available since: UD v1.0
License: CC BY-NC-SA 3.0 US
Includes text: yes
Genre: news reviews wiki
Lemmas: automatic
UPOS: converted from manual
XPOS: automatic
Features: automatic
Relations: converted from manual
Contributors: Petrov, Slav; Seeker, Wolfgang; McDonald, Ryan; Nivre, Joakim; Zeman, Daniel; Boyd, Adriane
Contributing: here
Contact: zeman@ufal.mff.cuni.cz
===============================================================================
(Original treebank contributors: Quirmbach-Brundage, Yvonne; LaMontagne, Adam; Souček, Milan; Järvinen, Timo; Radici, Alessandra)
