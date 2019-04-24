# cleaningcensusdata
Cleaning U.S. Census data - Commuting Analysis Project (special thanks to Chris Ullery for sharing code on YouTube) 

# All project packages imported at the start

# Project packages
import pandas as pd
import numpy as np

# Visualizations
import matplotlib.pyplot as plt 
import seaborn as sns
import plotly as py

# Statistics
from scipy import stats
from scipy.stats import norm, skew
from statistics import mode


import sys
import warnings

if not sys.warnoptions:
    warnings.simplefilter("ignore")
    
# Read in Census ACS 2017 data
df1 = pd.read_csv('../ACS_17_1YR_CP.csv',skiprows=[1],dtype=object)
df1.head()

df1 = pd.DataFrame(data=df1)

# Selecting Census variables for analysis
df2 = df1[['GEO.id','GEO.id2',"GEO.display-label",'HC01_VC03','HC02_VC03','HC04_VC03','HC06_VC03','HC08_VC03','HC01_VC11','HC02_VC11','HC04_VC11','HC06_VC11','HC08_VC11','HC01_VC27','HC02_VC27','HC04_VC27','HC06_VC27','HC08_VC27','HC01_VC28','HC02_VC28','HC04_VC28','HC06_VC28','HC08_VC28','HC01_VC29','HC02_VC29','HC04_VC29','HC06_VC29','HC08_VC29','HC01_VC30','HC02_VC30','HC04_VC30','HC06_VC30','HC08_VC30','HC01_VC31','HC02_VC31','HC04_VC31','HC06_VC31','HC08_VC31','HC01_VC32','HC02_VC32','HC04_VC32','HC06_VC32','HC08_VC32','HC01_VC36','HC02_VC36','HC04_VC36','HC06_VC36','HC08_VC36','HC01_VC40','HC02_VC40','HC04_VC40','HC06_VC40','HC08_VC40']]

df2.head()

# Renaming Census variables used in analysis
df2 = df2.rename(columns={"GEO.display-label":'Metro_Area','HC01_VC03':'pop_16over_2017','HC02_VC03':'pop_16over_2016','HC04_VC03':'pop_16over_2015','HC06_VC03':'pop_16over_2014','HC08_VC03':'pop_16over_2013','HC01_VC11':'labforce_2017','HC02_VC11':'labforce_2016','HC04_VC11':'labforce_2015','HC06_VC11':'labforce_2014','HC08_VC11':'labforce_2013','HC01_VC27':'commuters_2017','HC02_VC27':'commuters_2016','HC04_VC27':'commuters_2015','HC06_VC27':'commuters_2014','HC08_VC27':'commuters_2013','HC01_VC28':'commuters_drove_alone_2017','HC02_VC28':'commuters_drove_alone_2016','HC04_VC28':'commuters_drove_alone_2015','HC06_VC28':'commuters_drove_alone_2014','HC08_VC28':'commuters_drove_alone_2013','HC01_VC29':'commuters_carpool_2017','HC02_VC29':'commuters_carpool_2016','HC04_VC29':'commuters_carpool_2015','HC06_VC29':'commuters_carpool_2014','HC08_VC29':'commuters_carpool_2013','HC01_VC30':'commuters_pubtrans_2017','HC02_VC30':'commuters_pubtrans_2016','HC04_VC30':'commuters_pubtrans_2015','HC06_VC30':'commuters_pubtrans_2014','HC08_VC30':'commuters_pubtrans_2013','HC01_VC31':'commuters_walk_2017','HC02_VC31':'commuters_walk_2016','HC04_VC31':'commuters_walk_2015','HC06_VC31':'commuters_walk_2014','HC08_VC31':'commuters_walk_2013','HC01_VC32':'commuters_other_2017','HC02_VC32':'commuters_other_2016','HC04_VC32':'commuters_other_2015','HC06_VC32':'commuters_other_2014','HC08_VC32':'commuters_other_2013','HC01_VC36':'mean_commute_time_2017','HC02_VC36':'mean_commute_time_2016','HC04_VC36':'mean_commute_time_2015','HC06_VC36':'mean_commute_time_2014','HC08_VC36':'mean_commute_time_2013','HC01_VC40':'civ_emp_pop_2017','HC02_VC40':'civ_emp_pop_2016','HC04_VC40':'civ_emp_pop_2015','HC06_VC40':'civ_emp_pop_2014','HC08_VC40':'civ_emp_pop_2013'})

df2 = pd.DataFrame(data=df2)
df2

# Observing the default data types of selected variables
df2.info()

# Converting variables into appropriate data types:
df2['mean_commute_time_2017'] = df2.mean_commute_time_2017.replace('',np.nan)
df2['mean_commute_time_2016'] = df2.mean_commute_time_2016.replace('',np.nan)
df2['mean_commute_time_2015'] = df2.mean_commute_time_2015.replace('',np.nan)
df2['mean_commute_time_2014'] = df2.mean_commute_time_2014.replace('',np.nan)
df2['mean_commute_time_2013'] = df2.mean_commute_time_2013.replace('',np.nan)
df2['commuters_drove_alone_2017'] = df2.commuters_drove_alone_2017.replace('',np.nan)
df2['commuters_drove_alone_2016'] = df2.commuters_drove_alone_2016.replace('',np.nan)
df2['commuters_drove_alone_2015'] = df2.commuters_drove_alone_2015.replace('',np.nan)
df2['commuters_drove_alone_2014'] = df2.commuters_drove_alone_2014.replace('',np.nan)
df2['commuters_drove_alone_2013'] = df2.commuters_drove_alone_2013.replace('',np.nan)
df2['commuters_carpool_2017'] = df2.commuters_carpool_2017.replace('',np.nan)
df2['commuters_carpool_2016'] = df2.commuters_carpool_2016.replace('',np.nan)
df2['commuters_carpool_2015'] = df2.commuters_carpool_2015.replace('',np.nan)
df2['commuters_carpool_2014'] = df2.commuters_carpool_2014.replace('',np.nan)
df2['commuters_carpool_2013'] = df2.commuters_carpool_2013.replace('',np.nan)
df2['commuters_pubtrans_2017'] = df2.commuters_pubtrans_2017.replace('',np.nan)
df2['commuters_pubtrans_2016'] = df2.commuters_pubtrans_2016.replace('',np.nan)
df2['commuters_pubtrans_2015'] = df2.commuters_pubtrans_2015.replace('',np.nan)
df2['commuters_pubtrans_2014'] = df2.commuters_pubtrans_2014.replace('',np.nan)
df2['commuters_pubtrans_2013'] = df2.commuters_pubtrans_2013.replace('',np.nan)
df2['commuters_walk_2017'] = df2.commuters_walk_2017.replace('',np.nan)
df2['commuters_walk_2016'] = df2.commuters_walk_2016.replace('',np.nan)
df2['commuters_walk_2015'] = df2.commuters_walk_2015.replace('',np.nan)
df2['commuters_walk_2014'] = df2.commuters_walk_2014.replace('',np.nan)
df2['commuters_walk_2013'] = df2.commuters_walk_2013.replace('',np.nan)
df2['commuters_other_2017'] = df2.commuters_other_2017.replace('',np.nan)
df2['commuters_other_2016'] = df2.commuters_other_2016.replace('',np.nan)
df2['commuters_other_2015'] = df2.commuters_other_2015.replace('',np.nan)
df2['commuters_other_2014'] = df2.commuters_other_2014.replace('',np.nan)
df2['commuters_other_2013'] = df2.commuters_other_2013.replace('',np.nan)
df2['pop_16over_2017'] = df2.pop_16over_2017.replace('',np.nan)
df2['pop_16over_2016'] = df2.pop_16over_2016.replace('',np.nan)
df2['pop_16over_2015'] = df2.pop_16over_2015.replace('',np.nan)
df2['pop_16over_2014'] = df2.pop_16over_2014.replace('',np.nan)
df2['pop_16over_2013'] = df2.pop_16over_2013.replace('',np.nan)
df2['labforce_2017'] = df2.labforce_2017.replace('',np.nan)
df2['labforce_2016'] = df2.labforce_2016.replace('',np.nan)
df2['labforce_2015'] = df2.labforce_2015.replace('',np.nan)
df2['labforce_2014'] = df2.labforce_2014.replace('',np.nan)
df2['labforce_2013'] = df2.labforce_2013.replace('',np.nan)
df2['commuters_2017'] = df2.commuters_2017.replace('',np.nan)
df2['commuters_2016'] = df2.commuters_2016.replace('',np.nan)
df2['commuters_2015'] = df2.commuters_2015.replace('',np.nan)
df2['commuters_2014'] = df2.commuters_2014.replace('',np.nan)
df2['commuters_2013'] = df2.commuters_2013.replace('',np.nan)
df2['civ_emp_pop_2017'] = df2.civ_emp_pop_2017.replace('',np.nan)
df2['civ_emp_pop_2016'] = df2.civ_emp_pop_2016.replace('',np.nan)
df2['civ_emp_pop_2015'] = df2.civ_emp_pop_2015.replace('',np.nan)
df2['civ_emp_pop_2014'] = df2.civ_emp_pop_2014.replace('',np.nan)
df2['civ_emp_pop_2013'] = df2.civ_emp_pop_2013.replace('',np.nan)

df2['mean_commute_time_2017'] = df2.mean_commute_time_2017.astype(float)
df2['mean_commute_time_2016'] = df2.mean_commute_time_2016.astype(float)
df2['mean_commute_time_2015'] = df2.mean_commute_time_2015.astype(float)
df2['mean_commute_time_2014'] = df2.mean_commute_time_2014.astype(float)
df2['mean_commute_time_2013'] = df2.mean_commute_time_2013.astype(float)
df2['commuters_drove_alone_2017'] = df2.commuters_drove_alone_2017.astype(float)
df2['commuters_drove_alone_2016'] = df2.commuters_drove_alone_2016.astype(float)
df2['commuters_drove_alone_2015'] = df2.commuters_drove_alone_2015.astype(float)
df2['commuters_drove_alone_2014'] = df2.commuters_drove_alone_2014.astype(float)
df2['commuters_drove_alone_2013'] = df2.commuters_drove_alone_2013.astype(float)
df2['commuters_carpool_2017'] = df2.commuters_carpool_2017.astype(float)
df2['commuters_carpool_2016'] = df2.commuters_carpool_2016.astype(float)
df2['commuters_carpool_2015'] = df2.commuters_carpool_2015.astype(float)
df2['commuters_carpool_2014'] = df2.commuters_carpool_2014.astype(float)
df2['commuters_carpool_2013'] = df2.commuters_carpool_2013.astype(float)
df2['commuters_pubtrans_2017'] = df2.commuters_pubtrans_2017.astype(float)
df2['commuters_pubtrans_2016'] = df2.commuters_pubtrans_2016.astype(float)
df2['commuters_pubtrans_2015'] = df2.commuters_pubtrans_2015.astype(float)
df2['commuters_pubtrans_2014'] = df2.commuters_pubtrans_2014.astype(float)
df2['commuters_pubtrans_2013'] = df2.commuters_pubtrans_2013.astype(float)
df2['commuters_walk_2017'] = df2.commuters_walk_2017.astype(float)
df2['commuters_walk_2016'] = df2.commuters_walk_2016.astype(float)
df2['commuters_walk_2015'] = df2.commuters_walk_2015.astype(float)
df2['commuters_walk_2014'] = df2.commuters_walk_2014.astype(float)
df2['commuters_walk_2013'] = df2.commuters_walk_2013.astype(float)
df2['commuters_other_2017'] = df2.commuters_other_2017.astype(float)
df2['commuters_other_2016'] = df2.commuters_other_2016.astype(float)
df2['commuters_other_2015'] = df2.commuters_other_2015.astype(float)
df2['commuters_other_2014'] = df2.commuters_other_2014.astype(float)
df2['commuters_other_2013'] = df2.commuters_other_2013.astype(float)

pass
df2['pop_16over_2017'] = df2.pop_16over_2017.astype(int)
df2['pop_16over_2016'] = df2.pop_16over_2016.astype(int)
df2['pop_16over_2015'] = df2.pop_16over_2015.astype(int)
df2['pop_16over_2014'] = df2.pop_16over_2014.astype(int)
df2['pop_16over_2013'] = df2.pop_16over_2013.astype(int)
df2['labforce_2017'] = df2.labforce_2017.astype(int)
df2['labforce_2016'] = df2.labforce_2016.astype(int)
df2['labforce_2015'] = df2.labforce_2015.astype(int)
df2['labforce_2014'] = df2.labforce_2014.astype(int)
df2['labforce_2013'] = df2.labforce_2013.astype(int)
df2['commuters_2017'] = df2.commuters_2017.astype(int)
df2['commuters_2016'] = df2.commuters_2016.astype(int)
df2['commuters_2015'] = df2.commuters_2015.astype(int)
df2['commuters_2014'] = df2.commuters_2014.astype(int)
df2['commuters_2013'] = df2.commuters_2013.astype(int)
df2['civ_emp_pop_2017'] = df2.civ_emp_pop_2017.astype(int)
df2['civ_emp_pop_2016'] = df2.civ_emp_pop_2016.astype(int)
df2['civ_emp_pop_2015'] = df2.civ_emp_pop_2015.astype(int)
df2['civ_emp_pop_2014'] = df2.civ_emp_pop_2014.astype(int)
df2['civ_emp_pop_2013'] = df2.civ_emp_pop_2013.astype(int)

# Variables that are integers
df2['pop_16over_2017'] = pd.to_numeric(df2['pop_16over_2017'], errors='coerce')
df2['pop_16over_2016'] = pd.to_numeric(df2['pop_16over_2016'], errors='coerce')
df2['pop_16over_2015'] = pd.to_numeric(df2['pop_16over_2015'], errors='coerce')
df2['pop_16over_2014'] = pd.to_numeric(df2['pop_16over_2014'], errors='coerce')
df2['pop_16over_2013'] = pd.to_numeric(df2['pop_16over_2013'], errors='coerce')
df2['labforce_2017'] = pd.to_numeric(df2['labforce_2017'], errors='coerce')
df2['labforce_2016'] = pd.to_numeric(df2['labforce_2016'], errors='coerce')
df2['labforce_2015'] = pd.to_numeric(df2['labforce_2015'], errors='coerce')
df2['labforce_2014'] = pd.to_numeric(df2['labforce_2014'], errors='coerce')
df2['labforce_2013'] = pd.to_numeric(df2['labforce_2013'], errors='coerce')
df2['commuters_2017'] = pd.to_numeric(df2['commuters_2017'], errors='coerce')
df2['commuters_2016'] = pd.to_numeric(df2['commuters_2016'], errors='coerce')
df2['commuters_2015'] = pd.to_numeric(df2['commuters_2015'], errors='coerce')
df2['commuters_2014'] = pd.to_numeric(df2['commuters_2014'], errors='coerce')
df2['commuters_2013'] = pd.to_numeric(df2['commuters_2013'], errors='coerce')
df2['civ_emp_pop_2017'] = pd.to_numeric(df2['civ_emp_pop_2017'], errors='coerce')
df2['civ_emp_pop_2016'] = pd.to_numeric(df2['civ_emp_pop_2016'], errors='coerce')
df2['civ_emp_pop_2015'] = pd.to_numeric(df2['civ_emp_pop_2015'], errors='coerce')
df2['civ_emp_pop_2014'] = pd.to_numeric(df2['civ_emp_pop_2014'], errors='coerce')
df2['civ_emp_pop_2013'] = pd.to_numeric(df2['civ_emp_pop_2013'], errors='coerce')

# Dropping NaN values
df2 = df2.dropna(subset=['pop_16over_2017'])
df2 = df2.dropna(subset=['pop_16over_2016'])
df2 = df2.dropna(subset=['pop_16over_2015'])
df2 = df2.dropna(subset=['pop_16over_2014'])
df2 = df2.dropna(subset=['pop_16over_2013'])
df2 = df2.dropna(subset=['labforce_2017'])
df2 = df2.dropna(subset=['labforce_2016'])
df2 = df2.dropna(subset=['labforce_2015'])
df2 = df2.dropna(subset=['labforce_2014'])
df2 = df2.dropna(subset=['labforce_2013'])
df2 = df2.dropna(subset=['commuters_2017'])
df2 = df2.dropna(subset=['commuters_2016'])
df2 = df2.dropna(subset=['commuters_2015'])
df2 = df2.dropna(subset=['commuters_2014'])
df2 = df2.dropna(subset=['commuters_2013'])
df2 = df2.dropna(subset=['civ_emp_pop_2017'])
df2 = df2.dropna(subset=['civ_emp_pop_2016'])
df2 = df2.dropna(subset=['civ_emp_pop_2015'])
df2 = df2.dropna(subset=['civ_emp_pop_2014'])
df2 = df2.dropna(subset=['civ_emp_pop_2013'])

# converting to integers
df2['pop_16over_2017'] = df2.pop_16over_2017.astype(int)
df2['pop_16over_2016'] = df2.pop_16over_2016.astype(int)
df2['pop_16over_2015'] = df2.pop_16over_2015.astype(int)
df2['pop_16over_2014'] = df2.pop_16over_2014.astype(int)
df2['pop_16over_2013'] = df2.pop_16over_2013.astype(int)
df2['labforce_2017'] = df2.labforce_2017.astype(int)
df2['labforce_2016'] = df2.labforce_2016.astype(int)
df2['labforce_2015'] = df2.labforce_2015.astype(int)
df2['labforce_2014'] = df2.labforce_2014.astype(int)
df2['labforce_2013'] = df2.labforce_2013.astype(int)
df2['commuters_2017'] = df2.commuters_2017.astype(int)
df2['commuters_2016'] = df2.commuters_2016.astype(int)
df2['commuters_2015'] = df2.commuters_2015.astype(int)
df2['commuters_2014'] = df2.commuters_2014.astype(int)
df2['commuters_2013'] = df2.commuters_2013.astype(int)
df2['civ_emp_pop_2017'] = df2.civ_emp_pop_2017.astype(int)
df2['civ_emp_pop_2016'] = df2.civ_emp_pop_2016.astype(int)
df2['civ_emp_pop_2015'] = df2.civ_emp_pop_2015.astype(int)
df2['civ_emp_pop_2014'] = df2.civ_emp_pop_2014.astype(int)
df2['civ_emp_pop_2013'] = df2.civ_emp_pop_2013.astype(int)

# Checking that the data types were applied to variables
df2.info()

# Dropping "Metro Area" from Metro_Area:
df2["Metro_Area"]= df2["Metro_Area"].str.split(" Metro", n = 1, expand = True) 

# Dropping non-major Metro areas (Metro Areas that have pop<500,000)
indexnonmajormetros = df2[df2['pop_16over_2017'] < 1000000].index
df2.drop(indexnonmajormetros, inplace = True)

# Create some initial data viz...
# Plotting mean commute times for top-20 Metros based on mean commute time:

# Step 1 - sort descending by mean commute time:
df2 = df2.sort_values('pop_16over_2017', ascending=False)
df2.head()

# Step 2: Plot the mean commute time for the top 20 metros:
x = df2['Metro_Area']
y = df2['mean_commute_time_2017']

df2.sort_values('mean_commute_time_2017', ascending=False)[:20].plot.barh(y = 'mean_commute_time_2017', x = 'Metro_Area')







