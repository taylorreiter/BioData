KEGG-Decoder
================================================================
### Description ###
Designed to parse through a KEGG-Koala outputs (including blastKOALA, ghostKOALA, KOFAMSCAN) to determine the completeness of various metabolic pathways.

* This module was constructed using manually curated "canonical" pathways described as part of KEGG Pathway Maps. For information regarding which KOs are used to predict a metabolic pathway see the KOALA_definitions.txt

* if you are interested in certain pathway and the genes are listed in KEGG it is possible to add it to file (with some Python scripting)

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/rotheconrad/KEGGDecoder-binder/master)

### Please Cite ###
If you find that using KEGG Decoder to process your data has been useful, please consider citing this manuscript. If you are using KEGG Decoder to make figures then definitely cite this manuscript!

* [Graham ED, Heidelberg JF, Tully BJ. (2018) Potential for primary productivity in a globally-distributed bacterial phototroph. ISME J 350, 1–6](https://www.nature.com/articles/s41396-018-0091-3)  


### Dependencies ###

* [Pandas](http://pandas.pydata.org/pandas-docs/stable/install.html)

* [Seaborn](http://seaborn.pydata.org/installing.html)

* [matplotlib](http://matplotlib.org/users/installing.html)

## Installation ##
```
python3 -m pip install KEGGDecoder
```

## Procedure ##
* Start with protein FASTA file (INPUT_PROTEIN.fasta). This file can be multiple genomes combined. Be sure your submitted FASTA file has headers that group genomes together, KEGG-decoder.py groups based on the name provided in FASTA header before the first underscore (_) 
```
For example
>NORP9_1
>NORP9_2
>NORP9_3
>NORP10_1
>NORP10_2
>NORP10_3
In the output this produces two rows of output, one for genome NORP9 and one for genome NORP10 in the list and heat map
```
* Process protein sequences through KEGG-KOALA ([GhostKoala](https://www.kegg.jp/ghostkoala/), [BlastKoala](https://www.kegg.jp/blastkoala/), or [KOFAMSCAN](https://www.genome.jp/tools/kofamkoala/)) and download the tab-delimited KO assignment text file (KOALA_OUTPUT.txt)
* The KOALA output text file should look like this:
```
NORP9_1	K00370
NORP9_2	K00371
```

* Run KEGG-decoder
```
KEGG-decoder --input (-i) <KOALA_OUTPUT.txt> --output (-o) <FUNCTION_OUT.list> --vizoption (-v) <static/interactive/tanglegram>
```

* The FUNCTION_OUT.list generates a TSV version of the heat map. The first row contains pathway/process names, subsequent rows contain submitted groups/genomes and fractional percentage of pathway/process

* 'static' figure output is an SVG file function_heatmap.svg. Each distinct identifier before the underscore in the FASTA file will have a row

* 'interactive' figure output is an HTML file function_heatmap.html. Each distinct identifier before the underscore in the FASTA file will have a row, but can be loaded into a browser and value will be displayed by hovering over a cell with the mouse. Draw a box to zoom in on specific regions.

* 'tanglegram' -- under construction

KEGG-Expander
================================================================
### UNDER CONSTRUCTION ###
While KEGG-decoder is now a module, KEGG-expander and Decoder_and_Expand will still require running the Python scripts. Using the FUNCTION_OUT.list file will allow you to still make the intended final figure.

### Description ###
Designed to expand on the output from KEGG-Decoder. Within KEGG there is a lack of information regarding several processes of interest. To overcome these shortcomings, a small targeted HMM database was created (and will be updated) to fill in gaps of information.

HMM models are predominantly from the PFam database, but when necessary are pulled from TIGRfam and SFam.

### Dependencies ###
* [HMMER3](http://www.hmmer.org/)

### Additional Information ###
* Details as to which HMM models and genes are in each described pathway or process can be found in the supporting document, Pfam_definitions.txt
* In version 0.3, KEGG-Expander targets: phototrophy via proteorhodopsin, peptidases, alternative nitrogenases, ammonia transport, DMSP lyase, and DMSP synthase
* Unfortunately, accuracy depends on the model used, using a bit score cutoff of 75 (approximately an E-value <10E-20) does not always capture the best matches. For example the rhodopsin model does not distinguish between proteorhodopsin and other light driven rhodopsins (we use a tree to determine the proteorhodopsins). Or several of the DMSP lyases at low bit scores will match metalloproteases; in this instance the script has been modified to look for a more stringent bit score (>500). Or the TIGRfam models for the Fe-only and Vanadium nitrogenases generally match the same protein. 

## Prodecure ##
* Using a protein FASTA file with the same gene name set-up as described above - GENOMEID_Number - run a search against the custom HMM database
```
hmmsearch --tblout <NAME>_expanderv0.3.tbl -T 75 /path/to/BioData/KEGGDecoder/HMM_Models/expander_dbv0.3.hmm <INPUT_PROTEIN.fasta>
```
* The HMM results table is used to construct the heatmap by running KEGG-expander.py
```
python KEGG-expander.py <NAME>_expanderv0.3.tbl <HMM_OUT.list>
```
* The OUTPUT LIST generates a text version of the heat map. The first row contains pathway/process names, subsequent rows contain submitted groups/genomes and fractional percentage of pathway/process

* Figure is output as hmm_heatmap.svg. Each distinct identifier before the underscore in the FASTA file will have a row

Decoder and Expand
================================================================
### Description ###
Combines the KEGG and HMM heatmaps in to a final heat map. 

### Procedure ###
* Run the script Decoder_and_Expand.py
```
python Decode_and_Expand.py <FUNCTION_OUT.list> <HMM_OUT.list>
```
* Figure is output as decode-expand_heatmap.py. Each distinct identifier before the underscore in the FASTA file will have a row

Change Log
================================================================
## V1.0 ##
KEGGDecoder can now be installed via pip install. KEGGDecoder now offers 2 visualization outputs - the classic 'static' version and
the new 'interactive' version which will open a heatmap where you zoom and interact with the heatmap output 
Contributions to V1.0 occured as part of the Moore Foundation funded 'Speeding Up Science' hackathon. With contributions provided by: Taylor Reiter (UCDavis), Roth Conrad (GeorgiaTech), Jay Osvatic (UniVienna), Luiz Irber (UCDavis)

## V0.8 ##
Add elements regarding arsenic reduction

## V0.7 ##
Clarifies elements of methane oxidation and adds additional methanol/alcohol dehydrogenase
to KEGG function search. Adds the serine pathway for formaldehyde assimilation

## V0.6 ##
V.0.6 Adds Bacterial Secretion Systems as descrived by KEGG covering Type I, II, III, IV, Vabc, VI, Sec-SRP and Twin Arginine Targeting systems

## V0.5 ##
Adds parameters to force labels to be printed on heatmap. Includes functions
for sulfolipid biosynthesis (key gene sqdB) and C-P lyase

## V0.4 ##
Adds sections that more accurately represents anoxygenic photosynthesis - type-II and type-I reaction centers, adds NiFe hydrogenase Hyd-1 hyaABC, corrected typo leading to missed assignment to hydrogen:quinone oxidoreductase

## V0.3 ##
Latest version adds checks for: retinal biosynthesis, sulfite dehydrogenase (quinone), hydrazine dehydrogenase, hydrazine synthase, DMSP/DMS/DMSO cycling, cobalamin biosynthesis, competence-related DNA transport, anaplerotic reactions
