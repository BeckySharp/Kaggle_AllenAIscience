#################################################################################################
# Kaggle "The Allen AI Science Challenge" competition
# Oct 2015 - Feb 2016
# Kaggle username: "Cardal" 
#################################################################################################

This file explains how to run the ai2_Cardal.py code, which generates my solution for the
Kaggle AI2 competition.

1. System and Dependencies
   The code is written in python. I used Python 2.7.7 on Windows 7 Professional (8GB RAM) for 
   most of the executions. 
   The following python libraries are used by the script:
     numpy (1.8.1), scipy (0.14.0), pandas (0.14.0), sklearn (0.15.2), nltk (3.0.5)
   Since I couldn't get PyLucene to work on my system, I ran the parts that use Lucene on 
   a Linux machine (16GB RAM) with Python 2.6.6 and the following libraries:
     numpy (1.8.1), scipy (0.14.0), pandas (0.15.2), sklearn (0.16.0), nltk (3.1), lucene (pylucene-4.7.2-1)
   
2. Files
   Extract the archive to a directory on your system. 
   Copy the test set file to the "input" folder and change the settings as described below.
   
3. Resources
   The model uses several resources that were approved in the competition forum (in
   the "External Data Repository" thread).
   All files are supplied in the "corpus" folder, except the Wikipedia dumps and the pdf
   text books from www.ck12.org (see below).
   (a) AI2 - data downloaded from the AI2 web-site: http://allenai.org/data.html
       I extracted all the text from these datasets and organized it into a single
       corpus file at corpus/AI2_data/ai2_corpus.txt 
   (b) CK12 - I downloaded two types of resources from the http://www.ck12.org: 
       First, I used the HTML files in the EPUB file "Concepts_b_v8_vdt.epub".
       Second, I downloaded the science-related books in pdf format ("FlexBooks"), and converted them
       into text files using MS Word (with the "Save as" option). The converted files are 
       under corpus/CK12 with the ".text" suffix; the original pdf books are quite large, so they
       aren't included here (I kept the original name, only replaced the suffix).
       I prepared four corpus files from the CK12 books:
       corpus/CK12/OEBPS/ck12.txt - based on the HTML files in "Concepts_b_v8_vdt.epub";
       corpus/CK12/OEBPS/ck12_paragraphs.txt - as above, but using a different criterion
          for splitting the text into sections (paragraphs);
       corpus/CK12/ck12_text.txt - based on the pdf files;
       corpus/CK12/ck12_text_sentences.txt - as above,  but using a different criterion
          for splitting the text into sections (sentences).
   (c) Quizlet - study cards from quizlet.com. I parsed the cards (e.g., in order to remove
       wrong answers from multiple-choice questions), and manually editted some of them (e.g.,
       to fix format problems). The full corpus is in corpus/quizlet/quizlet_corpus.txt
   (d) Saylor and OpenStax - books in pdf format from Saylor Academy (http://www.saylor.org/books/) 
       and OpenStax College (https://www.openstaxcollege.org/books).
       I extracted the text from the pdf files using MS Word or (for large files) the pdfminer tool ("pdf2txt.py").
       The full corpus is in corpus/Saylor/saylor_text.txt
   (e) SimpleWiki - I downloaded the SimpleWiki dump file "simplewiki-20151102-pages-articles.xml" (496MB)
       from https://dumps.wikimedia.org/simplewiki/20151102/, and constructed three corpus files by extracting 
       some sections from subsets of the pages. The files are under corpus/simplewiki:
          simplewiki_1.0000_0.0500_0_5_True_True_True_corpus.txt (117MB)
          simplewiki_1.0000_0.1000_0_3_True_True_False_corpus.txt (114MB)
          simplewiki_1.0000_0.0100_0_3_True_True_False_pn59342_corpus.txt (4.4MB)
   (f) StudyStack - study cards from http://www.studystack.com. I parsed the cards (e.g., to
       remove sentences marked as False), and saved four versions under corpus/StudyStack, each 
       containing a different subset of the cards:
          studystack_corpus.txt  (72MB)
          studystack_corpus2.txt  (4MB)
          studystack_corpus3.txt  (9MB)
          studystack_corpus4.txt (17MB)
   (g) UtahOER - Utah Science Open Educational Resources textbooks downloaded from
       http://www.schools.utah.gov/CURR/science/OER.aspx. I extracted the text from the pdf
       files using the pdfminer tool. The entire corpus is in corpus/UtahOER/oer_text.txt
   (h) Wiki - I downloaded the Wiki dump file "enwiki-20150901-pages-articles.xml" (50.6GB)
       from https://dumps.wikimedia.org/enwiki/20150901/. I created two corpus files, containing
       different sections from subsets of the pages, under corpus/wiki:
          wiki_1.0000_0.0200_0_5_True_True_False_corpus.txt (161MB)
          wiki_0.5000_0.1000_0_5_True_True_False_pn59342_corpus.txt (33MB)
   (i) Wikibooks - I downloaded the WikiBooks dump file "enwikibooks-20151102-pages-articles.xml" (560MB)
       from https://dumps.wikimedia.org/enwikibooks/20151102/, and created a corpus file with
       some sections from a subset of the pages:
          wikibooks_1.0000_0.0200_0_10_True_True_False_corpus.txt (126MB)

4. Settings
   The SETTINGS.json file contains the following parameters:
   "BASE_DIR"   - the directory into which you extracted the archive
   "INPUT_DIR"  - directory with input files (training, validation, test sets)
   "CORPUS_DIR" - directory with corpus files, organized in sub-folders
   "SUBMISSION_DIR"  - directory for the submission (solution) files
   "TRAINING_FILE"   - training set file
   "VALIDATION_FILE" - validation set file
   "TESTING_FILE"    - test set file

=> IMPORTANT: Please change "BASE_DIR" and "TESTING_FILE" to the actual names of the directory in
              which you extracted the files and the test set file, respectively. The other
              settings should probably not be changed.
   
5. Execution
   The entire code is in the "ai2_Cardal.py" script. There are two phases for the execution.

   Phase A: Feature preparation
     In this phase, the script computes features for all questions and answers. There are 28 sets
     of features. Set 0 are "basic", in the sense that they are extracted from the questions
     and answers themselves (in the entire training set), without any additional corpus. 
     Each of the other 27 feature sets consists of one or two features that are computed 
     by searching for words from the questions and answers in one or more corpora.
     Features 1-20 use my search functions, and some of them are quite slow.
     Features 21-27 use PyLucene with the index files supplied in corpus/lucene_idx[1-7] (if you
     remove these files, the code will prepare the indexes from the corpus files).
     In order to speed-up the total runtime by utilizing multiple CPUs, each set of features can
     be computed separately (and in parallel), by running: "python ai2_Cardal.py prep [0-27]"
     Thus, you should run: 
        python ai2_Cardal.py prep 0
        python ai2_Cardal.py prep 1 
          ...and so on until:
        python ai2_Cardal.py prep 27
     Some features take minutes to calculate, others could take several hours or more.
     The model archive contains features computed for the training and validation sets (in the 
     folders "funcs_train" and "funcs_valid", respectively), so running "prep" as above will
     calculate the features only for the test set. In order to re-compute features, simply
     delete the relevant files from the "funcs_" directories.
     Note: In order to save computations, the code checks whether a question in the test set
           appears in the validation set; if so, it copies the scores each answer attained,
           instead of re-computing them. This assumes that the common questions share the same
           ID in both the validation and test files.

   Phase B: Model construction
     After all the features have been calculated, you should train the model and build the
     solution using:
        python ai2_Cardal.py run
     (Note: This will also compute any missing features that haven't been prepared in the
      previous phase)
     The "run" command first performs cross-validation on the training set and reports the
     scores (AUC and accuracy). It then trains the model on the complete training data and
     predicts the answers for both the validation and test sets. These solutions are 
     written to files in the "submission" folder.
     The training cross-validation scores should be as follows (by default, the code runs 
     only one CV iteration):
           CV scores   (mean = 0.83054): 0.83054
           CV accuracy (mean = 0.61600): 0.61600
     The model's predictions for the validation set should be:
         validation predictions (8132):
           102501: C (1.34e-01, 6.04e-02, 6.57e-01, 6.17e-02)
           102502: D (1.18e-01, 2.73e-01, 9.23e-02, 4.63e-01)
           ...
           102529: B (7.29e-02, 7.93e-01, 1.11e-01, 6.46e-02)
           102530: A (3.88e-01, 1.36e-01, 1.17e-01, 2.93e-01)
     The solution generated for the validation set in the "submission" directory should be 
     identical to the one supplied in the archive (submission/ai2_cardal_validation_20160204_1846.csv).

	
   