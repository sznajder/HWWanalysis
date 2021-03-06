
# DNN analysis

Using NN and Deep Learning to optimize the VBF selection

## 1. Creating a discriminant: DNN variable

Divided in three steps: data txt format, building DNN model and creating DNN variable.

### 1.1. Creating our data in TXT format:

* A binary classification problem (signal as 1 or backgorund as 0) for all of the input variables that describe each process.

* See template in [create_txt.C](https://github.com/lusanche/HWWanalysis/blob/master/DNNstudies/data_txt/create_txt.C).

### 1.2. Building a NN model/weight:

* The steps: load data, define, compile, fit and evaluate model.

* See template in [create_DNNmodel](https://github.com/lusanche/HWWanalysis/blob/master/DNNstudies/run_dnn/create_dnn.py).

* Can look at the training and test performance in the same template as well.

### 1.3. Creating the DNN variable:

* Using DNN model/weight for building a DNN discriminant inside a tree.

* See template in [adding_DNN_in_a_tree](https://github.com/lusanche/HWWanalysis/blob/master/DNNstudies/using_dnn/adding_dnn.py)

* Can do this using the gardener module.

## 2. Gardener module

Common tools to modify tree variables, add new variables, add weights, ...

### 2.1. Build a compatible area and setup github repository:
```
export SCRAM_ARCH=slc6_amd64_gcc630
cmsrel CMSSW_9_4_6_patch1
cd CMSSW_9_4_6_patch1/src/
cmsenv
git clone git@github.com:latinos/LatinoAnalysis.git
scram b
```
### 2.2. First copy an existing module :
```
cd LatinoAnalysis/Gardener/python/variables
cp anyVariable.py newVariable.py
```
### 2.3. Import the new module in [gardener.py](https://github.com/latinos/LatinoAnalysis/blob/master/Gardener/scripts/gardener.py) :
```ruby
from LatinoAnalysis.Gardener.variables.VBF_DNNvar      import DNNvarFiller
```
### 2.4. Add it to the list of nuisnaces in gardener.py :
```ruby
modules['vbfdnnvarFiller'] = DNNvarFiller()
```
### 2.5. Document how-to use it :
```
cd ../../scripts/
./gardener.py vbfdnnvarFiller \
/eos/cms/store/group/phys_higgs/cmshww/amassiro/Full2016_Apr17/Apr2017_summer16/lepSel__MCWeights__bSFLpTEffMulti__cleanTauMC__l2loose__hadd__l2tightOR__LepTrgFix__formulasMC__wwSel/latino_VBFHToWWTo2L2Nu_M125.root \
/eos/user/l/lusanche/Full2016/VBF_DNNvar/latino_VBFHToWWTo2L2Nu_M125_DNN.root
```

## 3. [Shape analysis](https://cms-hcomb.gitbooks.io/combine/content/part2/settinguptheanalysis.html#shape-analysis) with DNN (batch queue mode for mkShape)
after obtaining a data card  in [VBF analysis](https://github.com/lusanche/HWWanalysis/tree/master/VBFstudies#2-vbf-analysis-plots-configuration-for-mkshapes-mkplot-mkdatacards), prepare to use [combine](https://twiki.cern.ch/twiki/bin/viewauth/CMS/HATSatLPC2014StatisticsTools)

### 3.1. Setting up the environment (once)
```
export SCRAM_ARCH=slc6_amd64_gcc530
cmsrel CMSSW_8_1_0
cd CMSSW_8_1_0/src 
cmsenv
```
### 3.2. Standalone compilation in lxplus
```
git clone https://github.com/cms-analysis/HiggsAnalysis-CombinedLimit.git HiggsAnalysis/CombinedLimit
cd HiggsAnalysis/CombinedLimit
source env_standalone.sh
make -j 8; make # second make fixes compilation error of first
```
### 3.3. Update to a reccomended tag - currently the reccomended tag is v7.0.8
```
cd $CMSSW_BASE/src/HiggsAnalysis/CombinedLimit
git fetch origin
git checkout v7.0.8
scramv1 b clean; scramv1 b # always make a clean build
```
### 3.4. Expected/Observed significance
```
combine -M Significance -t -1 --expectSignal=1 datacard.txt
```

### 3.5. Lumiscale
```
combine -M Significance -t -1 --expectSignal=1 --setParameters lumiscale=2.78551 --freezeParameters lumiscale datacard.txt
```
