# TCRcost
##TCRcost: A deep learning model reveals TCR 3D structure achieves better prediction on TCR-peptide binding
In this study, we developed among one of the first deep learning methods, named TCRcost, to predict TCR-peptide binding by incorporating corrected 3D structures.
## Content
[Project Organization](##Project Organization)

[Usage](##Usage)

[Contacts](##Contacts)
##Project Organization
```
├── LICENSE                         <- Non-commercial license.
│     
├── README.md                       <- The README for users using TCRcost.
│ 
├── Correction_Module               <- The correction module of TCRcost. See README for their usages.
│   ├── data
│   │   ├── ref_data                <- The real structures as reference structures for evaluating the performance.
│   │   │   ├── true_all 
│   │   │   ├── true_main
│   │   │   └── true_side
│   │   ├── train_af                <- The training set of predicted structures generated by AlphaFold2.
│   │   ├── train_true              <- The training set of real structures.
│   │   ├── test_af                 <- The test set of real structures.
│   │   └── test_true               <- The test set of predicted structures generated by AlphaFold2.
│   ├── corrected_test.py           <- Correcting structures using pre-trained models.
│   ├── corrected_train.py          <- Training correction models of TCRcost.
│   ├── corrected_model.py          <- The network of correction module of TCRcost.
│   ├── alphafold_loss.py           <- The loss function of main chains.
│   ├── LDDT_loss.py                <- The loss function of side chains.
│   ├── structure_loss.py           <- The loss function of the angles and lengths of bonds.
│   ├── residue_constants.py        
│   ├── r3.py                  
│   ├── RMSD.py                     <- the performance evaluation of correction module.
│   ├── model.pth                   <- Pre-trained correction model.
│   └── output                      <- Structures after correction.
│
├── Binding_Prediction_Module       <- The binding prediction module of TCRcost. See README for their usages.
│   ├── data
│   │   ├── real_structures         <- The raw data files.
│   │   ├── Test.hdf                <- The test set.
│   │   └── Train.hdf               <- The training set. 
│   ├── binding_test.py             <- Making predictions using pre-trained models.
│   ├── binding_train.py            <- Training binding prediction models of TCRcost.
│   ├── model.py                    <- The network of binding prediction module of TCRcost.
│   ├── binding_sample_process.py   <- Processing raw data files.
│   ├── str_feat.py                 
│   ├── file_util.py                 
│   ├── img_util.py                  
│   ├── trpca.py                  
│   └── model.pth                   <- Pre-trained binding prediction model.
├── Figure                          <- Figure used in README.
    └── TCRcost.png
```
##Usage
Python package versions
TCRcost works perfectly in the following versions of the Python packages:
```
Python          3.9.7
torch           1.11.0+cpu
pandas          1.3.4
numpy           1.20.3
scikit-learn    0.24.2
```
### Correction Module
####Correcting structures using the pre-trained model
Users can use the pre-trained models we provided in `./Correction_Module/model.pth` to correct CDR3A:CDR3B:peptide structures directly by this command:

`python corrected_test.py --test_file_path ./data/test_af --test_save_path ./output --test_model model.pth --test_ref_path ./data/ref_data
`

The output files will saved in `./Correction_Module/output` including the complete structures and structures of main chains and side chains.
And the rmsd scores of the complete structures and structures of main chains and side chains will be output as:

```
 all_RMSD:  8.78435510453421 
 main_RMSD:  9.924537522992084 
 side_RMSD:  11.049083564709838
 ```
 
####Training correction models
Users can use the Python script `./Correction_Module/corrected_train.py` to train their own correction models on their TCR-peptide structure data samples for a better performance. For example, users can use the data files in `./Correction_Module/data/train_af` to train the model and save the model in `./Correction_Module/model_file` by this command:

`python corrected_train.py --epoch 1000 --lr 0.05 --train_file_path ./data/train_af --train_real_file_path ./data/train_true --model_path ./model_file`

### Binding Prediction Module
####Making predictions using the pre-trained model
Users can use the pre-trained models we provided in `./Binding_Prediction_Module/` to make predictions directly.
First, we need to process the raw data files, such as `./Binding_Prediction_Module/data/real_structures`by this command:

`python binding_sample_process.py --pdb_files ./data/real_structures`

In this process, we generated the same number of negative samples, randomly divided the training and test sets, obtained atomic features, and finally saved in the `Train.hdf` and `Test.hdf` files.
Then, we use the Python script `./Binding_Prediction_Module/binding_test.py` to make predictions and evaluate the performance of binding prediction module by this command:

`python binding_test.py --data-dir ./data --mlhdf-fn Test.hdf --model-path model.pth`

The results were output as:

```
Evaluation Summary:
ACC: 0.947,AUC: 0.974,Recall: 1.000,Precision: 0.905,F1: 0.950
```

####Training binding prediction models
Users can use the Python script `./Binding_Prediction_Module/binding_train.py` to train their own binding prediction models on their TCR-peptide structure data samples for a better performance. For example, users can use the processed data file in `./Binding_Prediction_Module/data/Train.hdf` to train the model and save the model in `./Binding_Prediction_Module/model_file` by this command:

`python binding_train.py --data-dir ./data --mlhdf-fn Train.hdf --model-path ./model_file --epoch-count 20 --learning-rate 0.005`
##Contacts
TCRcost is actively maintained by Fan Li, currently students at Xi'an Jiaotong University in the research group of Prof. Jiayin Wang. If you have any questions, please contact us by e-mail: lifan0513@stu.xjtu.edu.cn.