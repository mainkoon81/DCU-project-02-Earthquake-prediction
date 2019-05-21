# DCU-project-02-Earthquake-prediction

<img src="https://user-images.githubusercontent.com/31917400/56144176-cfa76580-5f99-11e9-9b33-dda2031c0b78.jpg" />



```
from __future__ import division
import pandas as pd
import matplotlib.pyplot as plt
import math
import swifter ## Parallel Pandas Libraries for faster processing (uses vectorization)
import numpy as np
import os
import glob ## Import files as a group of single glob

## Import all files as glob of files
data = {}
for filename in glob.glob('test/*.csv'):
    data[filename[:-4]] = pd.read_csv(filename
    
## Test if everything works fine
data['test/seg_00030f']['acoustic_data'].mean()

## Some preprossing of files
all_keys=[]
for x in data.keys():
    all_keys.append(x)
## list of file names

for x in all_keys:
    print(x)
######## this used later to traverse the data dictonary
    
    
## Create a new empty dictionary
pair = {}


## Normalize the acoustic data by using the formula log10(a/a0), where a0 is mean, a is each item

def normalize(df):
    ## Here apply Abs(x) cause we cannot compute log of negative value
    df['acoustic_data'] = df['acoustic_data'].swifter.apply(lambda x: abs(x)/df['acoustic_data'].mean())
    ## Drop 0s also as we cannot take log of 0s
    df.drop(df.loc[df['acoustic_data']==0].index, inplace=True)
    ## Apply log base 10 on all the rows
    df['acoustic_data'] = df['acoustic_data'].swifter.apply(lambda x: math.log10(x))

## Get Max value from acoustic data, corresponding to largest magnitude    
def check_quake(df):
    ## Return Max magnitude of data from each segment
    return df['acoustic_data'].max()

## Main Operation on the files
for x in all_keys:
    normalize(data[x])
    a = check_quake(data[x])
    pair[x]=a

## See if everthing worked out well
## Returns Max Magnitude from each segment
pair

import json

## Save Pair file
#with open('pair.json', 'w') as fp:
#    json.dump(pair, fp)

## Load Pair file
##with open('pair.json', 'r') as fp:
##    pair = json.loads(fp.read())


## Get all the segments where max magnitude is above the threshold
## Here is threshold is 2.5
new_pair = {}
for key, value in pair.items():
    if value > 2.5:
        new_pair[key] = value

# N = 10^a-bM 
# Ntot = 10^a
# therefore we have Ntot = 18 ## comes from new_pair
# Hence a = log(Ntot) = log(18) = 1.25

## Count No. of Occurences 
## N = 10^a-bM, where M is each max magnitude and b is 1 (by convention)
occurences = {}
for key, value in pair.items():
    occurences[key]= 10**(1.25-(1.0*value))
    
## Probability of the occurences
P_occurences = {}
for key, value in occurences.items():
    P_occurences[key]=value/18

# Lambda
P_occurences

# Inverse Lambda or Time_to_failure
inverse = {}
for key, value in P_occurences.items():
    inverse[key]=1/value


## Time_to_failure (not sure what is the unit of time though)
inverse


#with open('inverse.json', 'w') as fp:
#    json.dump(inverse, fp)
```
<img src="https://user-images.githubusercontent.com/31917400/56144176-cfa76580-5f99-11e9-9b33-dda2031c0b78.jpg" />


