# Exploratory correction after the first regression failed

Recorded before revision-2 generation. Keep revision/ unchanged as failed evidence.

The first revised pass scored 83/84 source-grounded answers and preserved both exact voice controls, but did not pass its regression gate. One output added an unsupported instruction; a Chinese limitation lost its explicit feature subject and one occurrence lost an exclusive scope restriction. The judge also identified possible ambiguities that are not definite factual errors.

Add two narrow checks to the existing conditional pass: make the feature the grammatical subject of a feature limitation, and ground final instructions as well as factual claims in the task/source. Keep the same seven regression cases and two generation contexts. This is a targeted retest after failure, not an independent performance estimate. Use the same source-grounded grading and manually inspect all source/limitation relationships; retain the first revision's failure.
