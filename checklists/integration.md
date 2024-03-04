### Create IB workspace
```bash
# Pick latest IB using scram list
cmsrel IB_NAME
cd IB_NAME/src
cmsenv
```
- Checkout a new integration branch
```bash
git checkout -b hgc-tpg-integration-XXXXXX
```
- Pick from the development branch the set of commits to be integrated

### :warning: If new data files have been added 
- Clone the `cms-data/L1Trigger-L1THGCal` repository outside of the CMSSW working area
```bash
git clone https://github.com/cms-data/L1Trigger-L1THGCal.git
cd L1Trigger-L1THGCal
git remote add hgctpg git@github.com:hgc-tpg/L1Trigger-L1THGCal.git
git checkout -b BRANCH_NAME
```
- Copy new data files into `L1Trigger-L1THGCal` and commit new data files there
- Push `L1Trigger-L1THGCal` and make PR to `cms-data`
- Remove data files (with `git rm`) from the CMSSW working area and commit
- Interactive rebase to squash the commit where the data file has been removed with the commit where the data file has been added
	- The goal is to completely remove this data file from the commit history as it shouldn't appear in the central CMSSW history
	- No data file should be included in `cmssw`
- Copy back data files from the `L1Trigger-L1THGCal` working area in order to be able to perform tests. These data files are now untracked in CMSSW.

### Run tests and make PR
- Run basic checks
```bash
scram b code-checks -j10
scram b code-format
scram b checker -j10
cd L1Trigger/L1THGCalUtilities/test
cmsRun testHGCalL1T_RelValV16_cfg.py
```

- Test workflows
```bash
# List workflows
runTheMatrix.py -n | grep 2026
# Run D86(C17=V16), D92(C18=V17), D94(C20=V17+HFNose) workflows
runTheMatrix.py -w upgrade -l 20034.0 --maxSteps=2 & # D86
runTheMatrix.py -w upgrade -l 22434.0 --maxSteps=2 & # D92
runTheMatrix.py -w upgrade -l 23234.0 --maxSteps=2 & # D94
```
- Push the integration branch and make a PR to `cms-sw`


