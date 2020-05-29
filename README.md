# hnyc_cd_processing

This repository contains three Python scripts that can help us generate final fields of interest from the [1850](input/1850) and [1880](input/1880) City Directory. You can use these scripts in the following order to obtain the final outputs.

There are 3 different inputs:
* 1880 Manhattan
* 1850 Manhattan
* 1850 Brooklyn

The relevant input and output files can be found on the [HNYC google drive](https://drive.google.com/drive/u/1/folders/1sEB2Tem9t7ZMNK24jTNZxtPQ2CN1ObPI)

An [EDA](http://htmlpreview.github.io/?https://raw.githubusercontent.com/CenterForSpatialResearch/hnyc_cd_processing/master/EDA.html) was created for the 1850 and 1880 outputs.

***

**Sample Input (1880 MN)**

```
Zimmermann Marie, strawgds. 165 Mercer, h 53 S. Oxford. B’klyn
```

**Sample Output**

```
{'Address 2': '165 Mercer',
 'Address 2 City': '',
 'Address 2 House Number': '165',
 'Address 2 Street Name': 'Mercer',
 'First Name': 'Marie',
 'Full Address': '165 Mercer,h 53 S. Oxford. B’klyn',
 'Full Entry': 'Zimmermann Marie, strawgds. 165 Mercer, h 53 S. Oxford. B’klyn',
 'Full Name': 'Zimmermann Marie',
 'H Address': 'h 53 S. Oxford. B’klyn',
 'H City': 'B’klyn',
 'H House Number': '53',
 'H Status-flag': 'True',
 'H Street Name': 'S. Oxford.',
 'Index': '462',
 'Last Name': 'Zimmermann',
 'Middle Name': '',
 'Occupation': 'strawgds',
 'Title': '',
 'Widow Of': '',
 'Widow-flag': 'False'}
```

## Script 1: Data Preprocessing (01_City_Directory_Formatting.ipynb)

In the OCR output, longer records are often split into multiple lines. 

<img src="https://github.com/CenterForSpatialResearch/hnyc_cd_processing/blob/master/images/image_1.PNG" width="512">

Therefore the first step is to dewrap lines when the first letter of next line is not equal to the initial letter in this section. There are a few special cases in the start of the next line that requires attention (B’klyn, B'way, E., W.), as they are part of addresses rather than the start of a new record. In this script, footnotes and extra delimiters are also removed.

The format of the 1880_MN, 1850_MN and 1850_BK input data are different. Hence, there are slight differences in the way the data was pre-processed.
- The 1850_MN and 1850_BK data are not split by '***' 
 - 1850_MN is split by 'B., C., D.', ...
 - 1850_BK is split by 'B, C, D', ...
- The first 8 lines in the 1850_BK dataset are headers, which were not required so they were removed
- Several extra steps were taken to clean the 1850_BK data
 - Remove page numbers
 - Remove 'Brooklyn Directory' headers
 - Remove whitespace and line breaks
 - Move text that were wrapped by [ and ( to the correct row
 - Clean up the Occupation tagging format

## Script 2: Build CRF Model (02_CRF.ipynb)

Conditional Random Field model is used in this project to identify name, occupation and address. In this script, a CRF is trained, and then evaluated on a test data set. Evaluation metrics include precision, recall and F1-score. Label notations and an example of labeled record are shown below.

<p float="left">
  <img src="https://github.com/CenterForSpatialResearch/hnyc_cd_processing/blob/master/images/label_notation.PNG" width="512">
  <img src="https://github.com/CenterForSpatialResearch/hnyc_cd_processing/blob/master/images/record_label.PNG" width="128">
<p>
 
Because the 1850 BK data use different abbreviations that are used to identify the name, occupation and address, it uses a separate training and testing dataset. This is the 1850_train_bk and 1850_validation_bk datasets found in the google drive.
  
## Script 3: Generate Final Fields (03_Final_Output.ipynb)
After identifying major components from each record, more detailed fields need to be generated, such as first name, last name, house number and street name etc.

- Due to the extra abbreviations in the 1850_BK dataset, additional fields like '\*' denoting a person of color can be extracted 

## Prerequisites:
To successfully run through these scripts, the following packages are needed:

```
-sklearn-crfsuite
-sklearn
-pandas
-pickle
-json
```
