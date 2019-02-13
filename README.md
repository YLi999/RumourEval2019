# This repo is under reconstruction from horrible coding techniques applied before the deadline.
# Please come back soon for less ugly code.


#Stuff into paper:
* Preprocessing
* LSTM-baseline vs Non-LSTM baseline
* Structured-self attention results, introspection
* BERT - short explanation, description of input
* BERT - analysis attention at transformer layers
* BERT - effect of including source/previous post
* BERT - describe shortly unsuccessful experiments
    * NER, POS, DEP tags
    * 3rd segment
    * sentiment analysis
    * handcrafted features
* Comparison with BERT - small
* Describe system FUSION approaches
* What we did not tried?
* Data analysis, bias towards Debunk This: forum

##Notes:

Extremna senzitivita na hyperparametre:
s LR 5e-5 sa to nic nenauci     

Skusal som pridat aj pos-ner tagy
problem s maskovanim paddingu
zda sa ze trenovanie modelov z shuffle funguje horsie nez trenovanie modelov bez shufflovania

optimalizacia na F1:
SelfAtt bez weighting scheme - ~ 0.44
SelfAtt z weighting scheme ~ 0.46 - 0.47
Bert - ~0.46 bez vah
Bert - ~0.50 s vahami

TODO remove 6 posts without text from training data


BERT:
predspracovanie cez twitter preprocesing (nahradenie url,mentionov atp specialnymi tokenmi) - pomohlo

pridanie stopwordov - pomohlo

pridanie featuru o source commente - pomohlo 0.837037 acc

pred tym:
Epoch 10, Validation loss|acc: 0.572339|0.826263 - (Best 0.5669|0.830976)
2019-01-04 16:12:39,089 DEBUG root: 0 - 0.76
1 - 0.75
2 - 0.89
3 - 0.93
4 - 0.95
5 - 0.94
6 - 0.94
7 - 0.97
8 - 0.87
9 - 1.00
10 - 0.86
11 - 1.00
12 - 1.00
13 - 1.00

potom 
Epoch 10, Validation loss|acc: 0.570702|0.837037 - (Best 0.5707|0.837037)


2019-01-07 14:06:01,456 DEBUG root: 0 - 0.71
1 - 0.77
2 - 0.91
3 - 0.93
4 - 0.96
5 - 0.93
6 - 0.94
7 - 0.97
8 - 0.87
9 - 1.00
10 - 0.86
11 - 1.00
12 - 1.00
13 - 1.00

Vysledky spred  rokov (iba na twitteri)
ECNU 	0.778 	
IITP 	0.641 	
kimber 	0.701 	
Mama Edha 	0.749 	
NileTMRG 	0.71 	
Turing 	0.769 	
Uwaterloo 	0.768 	


v jednom rune dokonca aj (hoci s este s trochu vyssou VL -- asi sa jedna iba o nahodu)
2019-01-07 14:41:51,189 INFO root: Epoch 5, Validation loss|acc: 0.580561|0.841751 - (Best 0.5806|0.841751)

uprava vstupu - rozdelenie src a prev postu

skusil som custom token \[p\] ale nezda sa ze by pomohol (acc 83,7710,83.0976) 
skusil som \[SEP\] token - nezda sa zeby nieco sposobilo (83.3670)
\[SEP\] token a 3 segmenty

uprava na batch 32 a dlzka viet 512 - 

pridanie klasifikacie veracity ku source tweetu - 



* Penalizacia u self attentionu nepomohla


* L1 reg vahy pre features:
batch.hasnegation, -6.8358e-02
batch.hasswearwords,  6.3869e-04
batch.capitalratio,  1.1418e-02
batch.hasperiod, 1.0274e-01
batch.hasqmark,  1.4008e-01
batch.hasemark, 3.6752e-02
batch.hasurl, -2.3978e-02
batch.haspic,1.4836e-05
batch.charcount,1.5156e-01
batch.wordcount, 3.8923e-02
batch.issource,-9.8941e-02
batch.Word2VecSimilarityWrtOther,5.6432e-02
batch.Word2VecSimilarityWrtSource,1.2725e-01
batch.Word2VecSimilarityWrtPrev-8.4859e-05

*BERT + issource bez trenovania BERTa
2019-01-10 15:13:46,439 INFO root: Epoch 66, Validation loss|acc: 0.733636|0.810774 - (Best 0.7336|0.810774)

* BErt + issource je velmi zly v denyovani
DEV
Total unique examples: 1485
Class balance: [102, 1181, 82, 120]
Class balance [ normalized ]: [ 6.86868687 79.52861953  5.52188552  8.08080808]
Class balance: [29, 0, 3, 6]
Class balance [ normalized ]: [76.31578947  0.          7.89473684 15.78947368]
MODEL_PREDS
Total unique examples: 1485
Class balance: [47, 1354, 3, 81]
Class balance [ normalized ]: [ 3.16498316 91.17845118  0.2020202   5.45454545]


* baseline bez avg-w2v
78,8552 acc !

"""
torch.sum(self.final_layer.weight,0)[300:]
Out[2]: 
tensor([-6.8358e-02,  6.3869e-04,  1.1418e-02,  1.0274e-01,  1.4008e-01,
         3.6752e-02, -2.3978e-02,  1.4836e-05,  1.5156e-01,  3.8923e-02,
        -9.8941e-02,  5.6432e-02,  1.2725e-01, -8.4859e-05], device='cuda:0',
       grad_fn=<SliceBackward>)
"""

2x bert - nepomohol

pridanie sentiment analyzy -  stabilnejsi trening?, zda sa 

pridanie velkych pismien a hashtagov - nepomohlo

pridanie dalsich featur (src_num_false_synonyms	, src_num_false_antonyms,
 thread_num_false_synonyms	, thread_num_false_antonyms	,
  src_unconfirmed	, src_rumour	, thread_unconfirmed	, 
  thread_rumour	, src_num_wh	, thread_num_wh)
- nepomohlo


* labeling scheme
0 supp, 1 comm, 2 deny, 3 query


* Self - attention without recurrent neural network actually works equally good as BERT, but the training seems to be more stable!

* distribucia hlbky datasetu

Dev data  
 Hlbka, Pocet  
('0', 38),  
('1', 810),  
('2', 211),  
('3', 122),  
('4', 102),  
('5', 67),  
('6', 52),  
('7', 31),  
('8', 23),  
('9', 15),  
('10', 7),  
('11', 4),  
('12', 2),  
('13', 1)  

Najlepsi vysledok Berta
2019-01-04 14:22:07,840 DEBUG root: Epoch 3, Validation loss|acc: 0.578753|0.836364 - (Best 0.5788|0.836364)
2019-01-04 14:22:07,840 DEBUG root: 0 - 0.79
1 - 0.77
2 - 0.90
3 - 0.93
4 - 0.94
5 - 0.94
6 - 0.92
7 - 0.94
8 - 0.87
9 - 0.93
10 - 0.86
11 - 1.00
12 - 1.00
13 - 1.00
# Ak dobre maskujem
* Baseline LSTM - najlepsie 83.2072

# TODO: recheck maskingu
* Baseline bez LSTM - najlepsie 83.5513
* Textovy model - najlepsie 80.5230557467309


* BERT: 83.6889

TODO: pocitat accuracy v zavilosti od hlbky v branchi

Napad na feature - hlbka v branchi? alebo

 staci source/ non source embedding?