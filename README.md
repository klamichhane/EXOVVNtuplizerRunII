# EXOVVNtuplizerRunII

Ntuplizer for searches for heavy resonances decaying to dibosons

## installation instructions

For Spring15(74):

```
export SCRAM_ARCH=slc6_amd64_gcc491
cmsrel CMSSW_7_4_12_patch2
cd CMSSW_7_4_12_patch2/src
cmsenv
git cms-init
```

For Fall15(76):

```
export SCRAM_ARCH=slc6_amd64_gcc493
cmsrel CMSSW_7_6_3_patch2
cd CMSSW_7_6_3_patch2/src
cmsenv
git cms-init
git cms-merge-topic nhanvtran:Puppi76X-backportFrom80X
```

For Fall16(80):

```

cmsrel CMSSW_8_0_7
cd CMSSW_8_0_7
cmsenv
git cms-init

```



The flags for running on Spring15(74) or Fall15(76) or Spring16(80) samples have to be changed with config["FALL15"]=False/True and config["SPRING16"]=False/True in python/ntuplizerOptions_*_cfi.py

### optional packages

Necessary just before CMSSW_7_4_16: for the boosted Hbb tagger (will add a lot of packages that will take a long time to compile):
```
git remote add btv-cmssw https://github.com/cms-btv-pog/cmssw.git
git fetch btv-cmssw
git cms-merge-topic -u cms-btv-pog:BoostedDoubleSVTagger-WithWeightFiles-v2_from-CMSSW_7_4_1
```


### getting the code for Fall15 and Spring15

```
export GITUSER=`git config user.github`
echo "Your github username has been set to \"$GITUSER\""
git clone git@github.com:${GITUSER}/EXOVVNtuplizerRunII.git
cd EXOVVNtuplizerRunII
git remote add UZHCMS git@github.com:UZHCMS/EXOVVNtuplizerRunII.git
git fetch UZHCMS
git checkout -b DevelopmentBranch UZHCMS/master
cd $CMSSW_BASE/src
scram b distclean
scram b -j8
cd EXOVVNtuplizerRunII/Ntuplizer
```

### getting the code for Spring16

```
export GITUSER=`git config user.github`
echo "Your github username has been set to \"$GITUSER\""
git clone git@github.com:${GITUSER}/EXOVVNtuplizerRunII.git
cd EXOVVNtuplizerRunII
git remote add UZHCMS git@github.com:UZHCMS/EXOVVNtuplizerRunII.git
git fetch UZHCMS
git checkout -b DevelopmentBranch UZHCMS/80X_ntuplizer
cd $CMSSW_BASE/src
scram b distclean
scram b -j8
cd EXOVVNtuplizerRunII/Ntuplizer
```


### running

```
cmsRun config_data.py (for data)
cmsRun config_MC.py (for MC)
```

the flags for running on data can be changed in python/ntuplizerOptions_data_cfi.py
the flags for running on MC can be changed in python/ntuplizerOptions_MC_cfi.py

to recluster jets and MET, or to add the Higgs-tagger the following flags can be changed:
```
config["DOAK8RECLUSTERING"] = False
config["DOHBBTAG"] = False
config["DOAK8PRUNEDRECLUSTERING"] = False
config["DOMETRECLUSTERING"] = False
```
If you want to use Higgs tagger the first two flags must all be set to True.

### Batch submission

#### Config file creation

Config file creation can be done via the [createConfig.py](Ntuplizer/tools/createConfig.py) script. It requires a text file with a list of input data sets, see e.g. [samples/QCD_HT_RunIISpring15MiniAODv2.txt](Ntuplizer/samples/QCD_HT_RunIISpring15MiniAODv2.txt). To run:
```
python tools/createConfig.py samples/QCD_HT_RunIISpring15MiniAODv2.txt
```
When running over *data*, this requires the ```-d``` flag. The script will automatically determine if the data sets are available on the T3 storage element. Also, ```--help``` will provide more information (e.g. allows changing the default number of jobs per event). If you run the script from a different directory, you need to provide the location of the [template file](Ntuplizer/submitJobsOnT3batch.cfg).

#### Job submission

Submit your jobs using the [submitJobsOnT3batch.py](Ntuplizer/submitJobsOnT3batch.py) script with the generated config files like this:
```
python submitJobsOnT3batch.py -C myconfig.cfg
```
Once the jobs are done, they can be checked for completeness like this:
```
python submitJobsOnT3batch.py -C myconfig.cfg --check
```
Resubmit jobs like this:
```
python submitJobsOnT3batch.py -C myconfig.cfg --resubmit 1,4,7
```
And eventually copied to the SE (path given in the config file):
```
python submitJobsOnT3batch.py -C myconfig.cfg --copy
```
