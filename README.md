Commented Code Detector

The tool detects commented code using heuristics based on usage of Halstead 
code metrics. In general, it can be used for all programming languages after
updating the lists of keywords, operators and comment marks. Currently the
script is fine-tuned for C++ and successfully tested (with C++ settings!) with
C and Java as well.

Please note that the tool uses heuristic algorithm - it is neither guaranteed
that each snipped of the commented code will be found nor that there will not
be some false-positives. However, about 75% of the hits really contain 
commented code.

The tool can be used for calculating Halstead metrics for the given file as
well.


=============================================================================
CONTENT:

src\:
    commentedCodeDetector.py - Script for detecting commented code. Please
        try commentedCodeDetector.py -h to see arguments lists. This script 
        accepts a singular file as an input.
    ccd_cpp.sh - Script for running commentedCodeDetector on each C++ file
        found in the given directory.
results\:
    Contain some example results of running ccd_cpp.sh on sources of a few
    well-known programs:
        C++: Doom3, LLVM and Firefox
        Java (with C++ settings for the script): Vuze
        C (with C++ setting for the script): Quake 3 Arena.

        
=============================================================================
ALGORITHM DETAILS
        
Halstead metrics are: Operators count, Distinct operators, Operands count,
Distinct operand, Program length, Program vocabulary, Volume, Difficulty,
Effort (see http://en.wikipedia.org/wiki/Halstead_complexity_measures).

0) Find comments. If two comments are adjacent, merge them into one comment.
1) For each comment:
a) Calculate Halstead metrics (as though the comment was a plain code)
b) Calculate Halstead metrics for joined comment and its context. Context of 
the comment are N lines of code directly before and directly after the
comment. N depends on the length (in lines) of the comment (see 
--context-multiplier and --min-context arguments).
c) Compare each metric calculated for a) and b) (Volume with Volume, Effort
with Effort etc.). Comparison uses relative values of metrics - value
of each metric is divided by the number of lines of code on which it was
computed. If the percentage difference between the relative value of a metric
for a) and b) is lower than COMMENTED_CODE_MAX_TRESHOLDS, give one Max Point,
if the value is higher than COMMENTED_CODE_MIN_TRESHOLDS, give one Min Point.
If the number of Min Points is higher than (or equals)
COMMENTED_CODE_MIN_TRESHOLDS_EXPECTED_PASSES and the number of Max Points
is higher than (or equals) COMMENTED_CODE_MAX_TRESHOLDS_EXPECTED_PASSES,
comment contains (with high probability) commented code. Report it.

Description of the step c) may be a little bit incomprehensible - please
look into the code (function analyzeComment) for more concise version :)


=============================================================================
CONFIGURING FOR OTHER LANGUAGES
    
1) Update lists RESERVED_WORDS, PURE_OPERATORS and variables 
ONELINE_COMMENT_TOKEN, MULTILINE_COMMENT_TOKEN_BEGIN, 
MULTILINE_COMMENT_TOKEN_END.

2) Some tunning of the COMMENTED_CODE_MAX_TRESHOLDS and 
COMMENTED_CODE_MIN_TRESHOLDS may be need (although current settings should
be good enough for all imperative and object-oriented languages). Using
higher verbosity levels may be useful in case of tuning. Please note that
initial tunning for C++ took about 10 minutes, so it is rather easy and
quick process.

If you configure the script for other languages, please let me know about
this fact and about the results of your code analysis.


=============================================================================
VARIOUS REMARKS
        
The script has been prepared as a project for Advanced program analysis course
during my Computer Science studies at University of Warsaw. Lack of tests
and poor comments are caused by the haste during preparation of it - student's
life :)

Script was prepared for Python 2.7.3.



Dominik Borowiec
domborow@gmail.com
January 2013
