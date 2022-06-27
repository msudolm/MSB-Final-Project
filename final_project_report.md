<h1 align="center" > Analyzing breast cancer microarray data to detect poor-prognosis gene expression signatures </h1>

<br>
<h3 align="center"> Małgorzata Sudoł </h3>

 <br>

<h3 align="center"> Modeling Complex Biological Systems </h3>
<h2 align="center"> FINAL PROJECT REPORT </h2>

 <br>

#### About the project:
The goal of the project was to build a classifier predicting breast cancer prognosis.
The first part of the project was to replicate the original van't Veer [1] study. The second part was to experiment with the data, propose other classifiers based on data exploration and compare classifiers performance.

<br>

## Abstract
Breast cancer is one of the most prevalent world's cancers. There are more lost disability-adjusted life years (DALYs) by women to breast cancer globally than any other type of cancer [3].
Breast cancer patients with the same stage of disease can have different treatment responses and overall outcome [1].
Chemotherapy or hormonal therapy reduces the risk of bad cancer outcome (distant metastases) by approximately one-third, however it was shown [4][5] that most patients (70-80%) receiving that kind of treatment would survive without it.
Developing tools for detecting tumors with high risk of developing distant metastases, would help with selecting patients who will benefit from the adjuvant therapies. That will also reduce the negative impact of chemotherapy on patients who will survive without it and reduce the general costs of breast cancer treatment.
In this research, five models predicting cancer outcome were built, and their performance was investigated on the same validation set.
The comparison showed that none of the models outperformed the others on all considered levels, but it also showed that analyzed models can be divided in the two groups depending on their strong points. Analysis carried out in this project create a great starting point for further inspecting the potential of microarray-data-based classifiers.
 <br>

## Introduction

### Breast cancer

According to World Health Organization, breast cancer is the most prevalent type of cancer in the world (WHO statistics, 2020) [3]
Breast cancer patients with the same stage of disease can have different treatment responses and different overall outcomes [1].
According to Veer and cited articles, therapies like chemotherapy or hormonal therapy reduce the risk of bad disease outcome (developing distant metastases) by circa one-third, but the majority of patients (70-80%) receiving this treatment would have survived without it [4][5]. Those therapies can also have side effects and are cost-consuming. The aim of the studies similar to the one presented in Veer et al 2002 paper [1] is to develop the tools that will point out tumors that are most likely to develop distant metastases in a few-year period.
The goal of this project was - first - to replicate Veer study - that means investigating the Veer dataset, going through all 
of the preprocessing steps, perform unsupervised clustering and build the prognosis classifier - and then to validate the constructed classifier using another breast cancer dataset (from Vijver et al 2002). The second part of the project was dedicated to take a closer look at the data, experiment with the analysis techniques using some modern research approaches, and finally propose another classifier based on this analysis, and then compare performance of the models built in Veer- and experimental-parts. 
 <br>
 
### First part - Veer et al. replication
This research consists of two parts. The first part is devoted to replicate the original van't Veer study [1] and then test the built model on van't Vijver [2] data. The aim was to replicate steps from Veer as precisely as possible. Due to description of some steps of analysis in original study and its
supplementary materials, some arbitrary decisions had to be made. Each of them is marked and commented in the analysis description in the Materials and Methods section.
Some of these decisions resulted in the need for changing parameters and threshold values used in further part of analysis.
Those changes are also marked when setting thresholds and parameters.
This part results with proposing two classifiers (differing by threshold value) built according to Veer data, one with the same threshold as in Vijver study, one proposed by myself, and their performance on previously cleaned and prepared Vijver data.
 <br>
 
### Second part - experimenting with Veer data
In the second part, more attention was given to Veer data. The mean to variance relationship and gene expression levels in samples were closer inspected.
Unknown values (NaN's / NaN values) were carefully removed from the data (including removing one sample containing over 10 000 unknown values from the dataset).
Then t-tests between "DM" (patients with distant metastases observed within 5 years after diagnosis) and "NODM" (patients without distant metastases observed within 5 years after diagnosis) groups were performed to select genes that may be significant for poor prognosis predictions.
Next, Principal Component Analysis based on Singular Vectors Decomposition was performed on selected genes in two variants -- with and without scaling
row expression values (in each variant, row values were centered before applying PCA). Then results were inspected and for each variant subset PC's with the highest differences between DM and NODM groups were selected, These were: PC1, PC2 and PC3 in centered PCA data and PC1, PC4, PC8 in scaled PCA data. For each of two PC subsets, exact PC values were visualized and carefully inspected for each of the training samples before moving further in analyses.
Based on PC's selection, three binary classifiers -- one for both, one for centered and one for scaled versions of PCA data were constructed.


After defining classifiers, test data (Vijver) were loaded, inspected (checking if they have the same gene names and scale as Veer data, removing unknown values) and preprocessed, including generating one copy of centered and one copy of centered-and-scaled test data to match two aforementioned training set versions.
Next, test data were rotated according to the rotation matrices from 'centered-' and 'scaled' Veer PCA, in order to obtain two variants of principal-components coordinates of the test set.
<br>

### Comparing constructed classifiers performance
The last step was to classify rotated test data using three PC-based binary classifiers, inspect results and compare PC-classifiers performance with classifiers derived from replicating van't Veer study.

The general conclusion is that pc-based models have higher mean accuracy but also higher DM misclassification rate (in veer-like models, generally more samples are assigned to DM group - that results in lower accuracy but also in lower false negative rate).
The best-performing (the highest accuracy and minimal fn-rates) pc-based model for analyzed data is c_0 classifier applied to scaled data.
<br>

## Materials and methods


### Data
Both datasets used in this study are available from April 2022 in R "cancerdata" package, as VEER and VIJVER Expression Sets.
VEER expression data consist of 78 samples ( named X1-X73 and X75-X79), represented by columns and 24 481 features (genes) represented by rows. VEER phenotype data consist of two columns "info" -- containing sample number, patient age and survival time in months, and "class" -- containing one of two possible factor variables "DM" - for patients who developed distant metastases in 5-year interval after diagnosis, and "NODM" - for patient who remained metastases-free in 5-year interval. There is also fData set available, in which symbols are assigned to some of the features.
VIJVER data consist of 295 cancer samples. Number and set of features are the same as in VEER. Phenotype data contain 43 columns with additional information; one of them is "class" column with the same factors as in VEER ("DM" and "NODM"), but part of the samples have unknown values in this column (107 out of 295). The remaining 42 columns were not used in this study. VIJVER data also contains a feature data set.
In both expression sets, expression values range from -2 to 2; according to [1], [2], [6] those are corrected for background level, log-transformed and normalized values.
 <br>
 
### Methods
All steps of analysis are available on github [GitHub Repository](https://github.com/msudolm/MSB-Final-Project/blob/main) as RMarkdown files.
<br>

#### Veer study replication

###### Preprocessing 
VEER Expression Set was loaded from "cancerdata" package and inspected. Unknown expression values (36 458) were counted; first their column-wise distribution was analyzed and one of the columns was deleted from the data because of high NaN content (over 10 from ~25 feature values were missing, X54 sample).
The second highest-NaN-content column (over 2 000 missing values < 10% of features) was kept in order not to cut another DM sample from the dataset (both high-NaN-content samples were from DM class). The number of NaN values in the remaining 76 samples were less than 80 per sample.
<br>

###### Selecting most predictive genes
Information that can be found in van't Veer study says that: 'Some 5,000 genes were significantly regulated across the group of samples that is, at least a twofold difference and a P-value of less than 0.01 in more than five tumors)', but there is no information provided, neither in original paper nor in supplementary materials, what does this p-value refers to (and how many times should the twofold difference appear in selected rows). Due to the lack of appropriate description and according to the information about selecting procedure described in [6] - first, rows with at least one value which absolute value is no less than 2 (so equal to 2 in this case). Then a multivariate t-test was performed to further subset significant rows.
As the t-test p-values were different from those reported in van't Veer (setting p-value threshold as 0.01 resulted in selecting only 31 genes), a new p-value threshold = 0.08 was chosen based on visual inspection of t-test statistics.
<br>

###### Unsupervised clustering
Next, according to veer study, unsupervised 2-dimensional clustering was performed (**Figure 1**). Replicated clustering results and those presented in Veer are much the same. The one difference observed is the presence of additional clustered area in the half of heatmap width, but it may be also the result of splitting the cluster in the right-bottom corner of Veer heatmap in two smaller clusters.


<br>


![Unsupervised clustering heatmap](https://github.com/msudolm/msb_breast_cancer_project/blob/main/unsupervised_clustering_heatmap.png)


**Figure 1** <br>
Heatmap with results of unsupervised two-dimensional clustering of van't Veer data.
<br>


###### Supervised classification 
According to the Veer description (paper and supplementary data), the supervised classification was conducted as follows:
first, values of (Pearson) correlation with outcome ("DM"/"NODM") were calculated for each of genes selected in the first step (twofold and p-value thresholding) and only genes with absolute correlation value greater than 0.2 were kept. Here, again, the original threshold value was changed (from 0.3) to obtain a sensible number of features (284 with 0.2 threshold vs 25 with 0.3 threshold). The number of genes hadn't changed compared to the first part of selection.
Next, genes were ordered by magnitude of correlation coefficient (descending). After that, the leave-one-out cross validation was performed in order to establish the optimal number of genes in a classifier (classifier assigns sample to the group ("DM"/"NODM") for which the Pearson correlation between sample expression and mean group expression is higher; each time correlation is calculated only for features vectors of length n, where n starts from 5, ends on number of selected important genes (cor 0.2 threshold), increases by 5 -- and describes number of top-correlation-magnitude genes from list defined in previous step).
<br>

###### Final number of genes included in classifier
The final number of genes to include in a classifier was set to 175 top genes, according to cross-validation results [**Figure 2a, 2b**].
This number is significantly greater than n=70 genes that maximizes accuracy in Veer study (here we can observe acc=0.82, fn=2 fp=12 for n=70 and acc=0.86, fn=2 fp=9 for n=175)

**Figure 2a**
![Cross-validation accuracy](https://github.com/msudolm/msb_breast_cancer_project/blob/main/cross_validation_accuracy.png)
**Figure 2b**
![Cross-validation fp and fn](https://github.com/msudolm/msb_breast_cancer_project/blob/main/cross_validation_fn_and_fp.png)

<br>
**Figure 2** <br>
**(a)** Accuracy values for leave-one-out cross-validation for correlation-magnitude-ordered genes from Veer.<br>
**(b)** False positives and false negatives numbers for leave-one-out cross-validation for correlation-magnitude-ordered genes from Veer. <br>
<br>
<br>

#### Experimenting with VEER data
In this part, data were preprocessed similar to the previous scenario, then closer inspected. This time, the only selection criterion was row t-test p-value below 0.1 threshold. Next, two variants of input for the principal component analysis algorithm were prepared - one with centered and one with centered-and-scaled row expression values (the reason behind keeping also not-scaled data was that original dependencies between expression values may also turn out to be informative). Then three classifiers were built based on obtained PCA results.
<br>

###### Loading and visualizing VEER data
Data were loaded and preprocessed as in the previous part (expression data and pData were extracted, unknown values removed). Additionally, feature set was displayed, and gene expression mean vs variance relationship was inspected. Then, levels of mean and individual gene expression were visualized.

###### Subsetting genes
In this part selection of genes was based only on multivariate row t-test. Based on t-test output statistics, genes with t-test p-value below 0.01 threshold were selected. As a result 4 605 features were chosen. The mean vs variance relationship was again visualized to gain the information about potential change of the ratio distribution in selected subset.

###### Centering and centering-and-scaling the data
Two versions of this ~4 000 gene subset were created. In the first one, row expressions were centered to have zero mean, in the second one, expressions were centered and scaled to unit variance. Those two variants were passed to the next step as PCA inputs.

###### PCA based on SVD
Principal Component Analysis (PCA) based Singular Vector Decomposition (SVD) was conducted using prcomp() R function on two passed variants of data.
Results of PC-decomposition were printed, plotted and closely inspected. For each of the two data variants, the subset of PC's whose mean values between "DM" and "NODM" samples differed the most, was selected. Those were PC1, PC2 and PC3 for centered-data variant and PC1, PC4 and PC8 for scaled-data variant.

###### Building classifiers
Based on selected PC's and the thorough visual inspection of their values across 77 samples, three binary classifiers were built. In each of these classifiers, assigning a sample to one of the classes was based on comparing its distance to the "DM" and "NODM" group centers, measured in the space spanned by a selected subset of principal components. First classifier (c_0) used only 1st PC and is suitable for both centered and centered-and-scaled data. Second and third classifiers (marked as c_1, s_1) are based on 3 PC's each and designed for centered and centered-and-scaled versions of data respectively (exact definitions of the models are available in supplementary RMarkdown files). 
<br>


#### Testing classifiers' performance

All the five classifiers - two from the first and three from the second part of the research (which will be further referred to as veer-like and pc-based classifiers accordingly) were tested on VIJVER dataset.
<br>

###### Preparing test data
VIJVER data were loaded and inspected (checking if the set of features and scale of expression are the same). Then the NaN-values in phenotype- and expression-data were analyzed. Due to the unknown class value ("DM"/"NODM") in the phenotype data, 107 samples were removed from the testing set.
Then three versions of the testing set - one for veer-like classifiers and two for pca-based classifiers, were prepared.
In the first version, genes were filtered and ordered according to the ordered list of 175 genes chosen in cross-validation. In the two last versions, the same genes as input genes for PCA were chosen. In each case, column- and row-unknown-values-distributions were investigated, columns with more than 10 NaN values were deleted and the rest of NaN values were replaced by row means. Test set versions for pc-based were additionally centered (both of them) and scaled (third one) to match aforementioned classifiers versions. Next, these two sets were rotated according to the rotation matrices from 'centered' and 'centered-and-scaled' Veer PCA data, in order to obtain principal-components-coordinates representations of the test sets.

Above operations finally resulted in producing the 186-samples-size test set for veer-like classifiers and two 185-samples-size test sets for pc-based classifiers.

###### Testing classifiers
All five classifiers were tested on appropriate input data versions of the Vijver set. Due to presence of the PC1 component in both considered ('only-centered-pca' and 'scaled-pca') PC subsets, classifier c_0 was tested on both - only-centered and centered-and-scaled test data versions, what finally resulted in five classification outputs for analyzed Vijver set.




## Results

![Cross-validation fp and fn](https://github.com/msudolm/msb_breast_cancer_project/blob/main/performance_table.jpeg)

**Table 3** <br>
Performance of DM-classifiers.
<br>
<br>

Results of five classification runs on Vijver data are shown in the above table (**Table 3**).
It can be concluded, that in general, the accuracy of pc-based classifiers outperformed veer-like classifiers (acc = 0.62 - 0.71 vs acc = 0.42 - 0.61) on these data, but also misclassification and FNR rates were higher in pc-based classification (0.17 - 0.19 vs 0.1 - 0.17 DM samples omitted (fn/(tp+tn+fp+fn)) in PC-based and veer-like classifiers accordingly), false negative rate:  FNR = 0.4-0.68 vs FNR = 0.1-0.42.
Thus, none of the models outperformed others in its performance. But in the veer-like group, the modified (not vijver-like) model achieves significantly better results than the one with NODM correlation threshold set to 0.4. This is also the model that dramatically outperformed all other ones in not-misclassifying DM samples as negative samples.

The misclassification rate reported in van't Veer study for validation set of 19 samples (12 "DM" and 7 "NODM") was 0.11 (2/19 samples), but there is no information provided about the class of misclassified samples, so the false negative rate may range from 0 to 0.17 and percent of omitted DM samples, according to the whole data, range from 0 to 0.11 - what is worse than performance of simple veer-like classifier proposed in this project, but outperforms investigated pc-based classifiers (which, as mentioned, tend to have higher positive misclassification rates). For more reliable comparisons, bigger reference validation set should be provided.
<br>

## Discussion

Conducted research showed that defined models based on particular genes -- and using correlation to measure similarity between samples, have generally lower DM-misclassification rates than models based on principal component's decomposition -- and using euclidean distance in principal-components space as similarity measurement. On the other side, pc-based models have on average higher accuracy scores (what raises the question if using accuracy as main model selection criterion, as done in Veer, is an appropriate choice in this context).

The 'Veer-like simple comparison' model achieved the best performance in context of selecting patients with the bad future disease outcome, who should be treated with adjuvant therapy, but despite promising, DM-misclassification rates should be further lowered to use the model in practice (for now 10% poor-prognosis patients would not be recognized by this tool).
On the other hand, proposed pc-based models which had higher DM-misclassification rates for analyzed data are very first attempts of pc-based approach and there is a huge space of possibilities for further experiments -- from establishing other PC selecting criteria to changing and combining similarity measures used in classifiers and changing 'voting power' of different components.

Another research direction may be to train and validate models on other datasets (e.g. containing more samples and less NaN values) and investigate their robustness. Furthermore, tumor samples may be divided into separate types before classification and more type-specific models could be used for prediction (as described in Veer and that can be also concluded from analyzing the unsupervised-clustering heatmap, there are more than one histopathological breast cancer types). This may also result in reducing outliers (which can be observed in principal components' values in the training set) and thus improve the performance of pc-based methods.



## References

1. Van 't Veer, L., Dai, H., van de Vijver, M. et al. Gene expression profiling predicts clinical outcome of breast cancer. Nature 415, 530–536 (2002). https://doi.org/10.1038/415530a
2. van de Vijver MJ, He YD, van't Veer LJ, Dai H, Hart AA, Voskuil DW, Schreiber GJ, Peterse JL, Roberts C, Marton MJ, Parrish M, Atsma D, Witteveen A, Glas A, Delahaye L, van der Velde T, Bartelink H, Rodenhuis S, Rutgers ET, Friend SH, Bernards R. A gene-expression signature as a predictor of survival in breast cancer. N Engl J Med. 2002 Dec 19;347(25):1999-2009. doi: 10.1056/NEJMoa021967. PMID: 12490681.
3. https://www.who.int/news-room/fact-sheets/detail/breast-cancer
4. Polychemotherapy for early breast cancer: an overview of the randomised trials. Early Breast Cancer Trialists' Collaborative Group. Lancet. 1998 Sep 19;352(9132):930-42. PMID: 9752815.
5. Tamoxifen for early breast cancer: an overview of the randomised trials. Early Breast Cancer Trialists' Collaborative Group. Lancet. 1998 May 16;351(9114):1451-67. PMID: 9605801.
6. Zhao B, Erwin A, Xue B. How many differentially expressed genes: A perspective from the comparison of genotypic and phenotypic distances. Genomics. 2018 Jan;110(1):67-73. doi: 10.1016/j.ygeno.2017.08.007. Epub 2017 Aug 24. PMID: 28843784.
