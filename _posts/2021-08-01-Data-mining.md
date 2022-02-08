---
layout: post
read_time: true
show_date: true
title: "Data Mining for Catalyst Materials"
date: 2021-08-01
img: posts/20210801/Datamining.png
tags: [Python, Data]
author: Tianyou Mou
description: "Data screening is crucial."
---

## Overview

Catalyst materials are playing important roles in the whole world. Catalysts are composed of different chemical species, leading to the different catalytic performances. While catalysts are often composed of a single kind of metal, they can also be a combination of several or even more than five kinds of different metals. So the whole possible catalyst space is huge, which means it's extremely hard to directly synthesize each of them and tell which one performs better for a specific case. Thus, machine learning methods step in to help screen the catalysts, but ML methods usually require large data sets for accuracy. Nowadays, many research scientists developed databases for ML usage, but these kind of databases are very large, and include probably all possibly combinations, which may not be the case for a specific catalytic reaction which only needs a small part of this database. So, there is a need to filter the large database to clean the data for a specific problem. 


## Open Catalyst Project data screening

The Open Catalyst Project is a collaborative research effort between Facebook AI Research (FAIR) and Carnegie Mellon University’s (CMU) Department of Chemical Engineering. The aim is to use AI to model and discover new catalysts for use in renewable energy storage to help in addressing climate change.

An open challenge is finding low-cost catalysts to drive these reactions at high rates. Through the use of quantum mechanical simulations (density functional theory), new catalyst structures can be tested and evaluated. Unfortunately, the high computational cost of these simulations limits the number of structures that may be tested. The use of AI or machine learning may provide a method to efficiently approximate these calculations, leading to new approaches in finding effective catalysts.

To enable the broader research community to participate in this important project, we are releasing the Open Catalyst Dataset for training ML models. The dataset contains 1.2 million molecular relaxations with results from over 250 million DFT calculations. In addition to the data, baseline models and code are provided on our Github page. View the leaderboard to see the latest results and to submit your own to the evaluation server! Join the discuss forum to join the discussion with the community and ask any questions.

However, it's not efficient to grab useful data from that huge database, so I set up a couple of codes for quick inquiries. 


### 1. Screen alloys with p-block metals and terminations of (111) and (100)

Import 
```python
import pandas as pd
import pickle
import os
```
Lists of all chemical elements in different blocks. Metals in l2 is the so-called p-block metals.
```python
l1 = ['Pt']
l2 = ['Al','Ga','Ge','In','Sn','Sb','Tl','Pb','Bi','Po']
l3 = ['H','Li','Na','K','Rb','Cs','Fr',
      'Be','Mg','Ca','Sr','Ba','Ra',
      'Sc','Y','Lu','Lr',
      'Ti','Zr','Hf','Rf',
      'V','Nb','Ta','Db',
      'Cr','Mo','W','Sg',
      'Mn','Tc','Re','Bh',
      'Fe','Ru','Os','Hs',
      'Co','Rh','Ir','Mt',
      'Ni','Pd','Ds',
      'Zn','Cd','Hg','Cn',
      'B','Nh',
      'C','Si','Fl',
      'N','As',
      'O','S','Se','Te',
      'F','Cl','Br','I','At','P']
l4 = ['Li','Na','K','Rb','Cs','Fr',
      'Be','Mg','Ca','Sr','Ba','Ra',
      'Nh',
      'Si','Fl',
      'As',
      'Se','Te',
      'Cl','Br','At']
l5 = ['H','P','B','C','N','S','O','F','I']
```
Read in data and filter it.
```python
f1 = pd.read_pickle('../OC.pkl')
dict1 = {}
dict2 = {}
f = {}

j = 0
k = 0

for i,n in enumerate(f1):
    if (f1[n]['miller_index'][0] == 1 and f1[n]['miller_index'][1] == 1 and f1[n]['miller_index'][2] == 1) or (f1[n]['m\
iller_index'][0] == 1 and f1[n]['miller_index'][1] == 0 and f1[n]['miller_index'][2] == 0):
        if any(word in f1[n]['bulk_symbols'] for word in l2):
            if any(word in f1[n]['bulk_symbols'] for word in l4):
                continue
            else:
                #f[n] = {f[n]['bulk_symbols'], f[n]['miller_index']}                                                    
                f[n] = f1[n]
                k += 1
```
Save data. 
```python
if os.path.exists('index.pickle'): os.remove('index.pickle')
if os.path.exists('index.txt'): os.remove('index.txt')

with open('index.pickle', 'wb') as handle:
    pickle.dump(f, handle, protocol=pickle.HIGHEST_PROTOCOL)
with open('index.txt', 'w') as f:
    print(f, file=f)

print ('your job is done, total {} systems'.format(j))
```

### 2. Screen all combinations of transition metals and p-block metals based on the previous alloys with specific terminations.

```python
f = pd.read_pickle('../l4/index.pickle')
dict1 = {}
dict2 = {}

j = 0
k = 0

for i,n in enumerate(f):
    m = 0; p = 0
    for word in l5:
        if word in f[n]['bulk_symbols']:
            string  = f[n]['bulk_symbols']
            results = [i for i in range(len(string)) if string.startswith(word, i)]
            print (results)
            for q in results:
                try:
                    small = f[n]['bulk_symbols'][q+1]
                    #                print (small)                                                                      
                    m += 1
                except:
                    break
		if (word == 'P' and (small in ['t','d','b','o'])) or \
                   (word == 'H' and (small in ['f','s','g'])) or \
                   (word == 'B' and (small in ['a','e','i','r','h'])) or \
                   (word == 'C' and (small in ['a','s','o','l','r','u','d','n'])) or \
                   (word == 'N' and (small in ['a','b','i','h'])) or \
                   (word == 'S' and (small in ['e','n','b','r','c','g','i'])) or \
                   (word == 'O' and small == 's') or \
                   (word == 'F' and (small in ['r','e','l'])) or \
                   (word == 'I' and (small in ['r','n'])):
                    p += 1

    print (f[n]['bulk_symbols'],m,p)
    if m == p:
        dict2[n] = {f[n]['bulk_symbols'], f[n]['miller_index']}
        dict1[n] = f[n]
        j += 1
```

### 3. Screen all bimetallic alloys since section 2 can have all possible combinations of alloys . 

```python
f1 = pd.read_pickle('../T+P/index.pickle')
dict1 = {}
dict2 = {}
f = {}

j = 0
k = 0

for i,n in enumerate(f1):
    r = 0
    for word in l2:
        if word in f1[n]['bulk_symbols']:
            r += 1
    if r == 1:
        f[n] = f1[n]

for i,n in enumerate(f):
    m = 0; p = 0; r = 0
    for word in l3:
        if word in f[n]['bulk_symbols']:
            r += 1
    if r == 1:
        dict2[n] = {f[n]['bulk_symbols'], f[n]['miller_index']}
        dict1[n] = f[n]
        j += 1
```

## Materials Project data screening

By computing properties of all known materials, the Materials Project aims to remove guesswork from materials design in a variety of applications. Experimental research can be targeted to the most promising compounds from computational data sets. Researchers will be able to data-mine scientific trends in materials properties. By providing materials researchers with the information they need to design better, the Materials Project aims to accelerate innovation in materials research.

### Utilize the 'query' function with different criteria for quick screening

```python
import pandas as pd
import pickle
import os
from pymatgen import MPRester

data = m.query(criteria={"elements": {"$nin":l6,"$in": l2},"nelements": 2,"spacegroup.symbol":"Pm-3m"}, properties=["material_id","pretty_formula","spacegroup.symbol"])

with open('index', 'w') as txt:
    print(data, file = txt)

data1 = {}
for i,n in enumerate(data):
    if ('3' in data[i]['pretty_formula']) and all(num not in data[i]['pretty_formula'] for num in l_):
#        print (data[i])                                                                                                
        ID = data[i]['material_id']
        data1[ID] = data[i]
        z += 1

with open('index.pickle', 'wb') as handle:
    pickle.dump(data1, handle, protocol=pickle.HIGHEST_PROTOCOL)
with open('index1', 'w') as txt:
    print(data1, file = txt)

print ('your job is done, total {} systems'.format(z))
```

Screen Pm3m type of crystal structure.
```python
m = MPRester('MlyixfIghRqd56ET')

f = pd.read_pickle('../Bimetallic/index.pickle')

dict1 = {}
dict2 = {}
dict3 = {}

for i,n in enumerate(f):
    ID = f[n]['bulk_mpid']
    if ID in f1:
        if f1[ID]['spacegroup.symbol'] == 'Pm-3m':
            dict3[n] = f[n]
            dict2[n] = {f[n]['bulk_symbols'],f[n]['bulk_mpid'], f[n]['miller_index'],f1[ID]['spacegroup.symbol'],f[n]['ads_symbols']}
            dict1[n] = f[n]
            z += 1

if os.path.exists('index.pickle'): os.remove('index.pickle')
if os.path.exists('index'): os.remove('index')

with open('index.pickle', 'wb') as handle:
    pickle.dump(dict1, handle, protocol=pickle.HIGHEST_PROTOCOL)
with open('index', 'w') as txt:
    print(dict2, file = txt)

print ('your job is done, total {} systems'.format(z))
```

### Try to match the two data sets for A3B type of alloys

```python
m = MPRester('MlyixfIghRqd56ET')

f = pd.read_pickle('../OC.pkl')
f1 = pd.read_pickle('../2e_1/index.pickle')
dict1 = {}
dict2 = {}
dict3 = {}

for i,n in enumerate(f):
    ID = f[n]['bulk_mpid']
    ADS = f[n]['ads_symbols']
    if ID in f1:
        if ADS == '*H':
            if (f[n]['miller_index'][0] == 1 and f[n]['miller_index'][1] == 1 and f[n]['miller_index'][2] == 1) \
            or (f[n]['miller_index'][0] == 1 and f[n]['miller_index'][1] == 0 and f[n]['miller_index'][2] == 0):
                dict2[n] = {f[n]['bulk_symbols'],f[n]['bulk_mpid'],f[n]['miller_index'],
                            f1[ID]['spacegroup.symbol'],f[n]['ads_symbols']}
                dict1[n] = f[n]
                z += 1

if os.path.exists('index.pickle'): os.remove('index.pickle')
if os.path.exists('index'): os.remove('index')

with open('index.pickle', 'wb') as handle:
    pickle.dump(dict1, handle, protocol=pickle.HIGHEST_PROTOCOL)
with open('index', 'w') as txt:
    print(dict2, file = txt)

print ('your job is done, total {} systems'.format(z))
```


## Brief introduction to crystal structures.




