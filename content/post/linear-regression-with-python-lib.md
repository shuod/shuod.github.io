+++
date = "2016-04-29"
title = "Linear Regression with different Python libs: numpy, scipy"

Tags = ["Python", "Modeling", "Data"]

Categories = ["Development", "Data_science"]

draft = false

+++



Linearing regression is one of the fundamental techqiques to use when analyze the data. If you were using Python, you would have several options to do this, including numpy, scipy and sklearn.

<!--more-->

A free eBook to recommend is An Introduction to Statistical Learning, available at http://www-bcf.usc.edu/~gareth/ISL/ 



### numpy.linalg.lstsq

* numpy.linalg.lstsq(a, b, rcond=-1)
  return the least-squares solution to a linear matrix equation in the form of ax=b

    Input: a,b are array ------ we could use np.array to transform numbers into array
    Input: rcond is an optional paramenter that gives the cut-off ratio

    Return: in the form of array

    example:


```
import numpy as np
from matplotlib import pyplot as plt
import read_a_cell_in_excel2013

# load data file
workbook_name="D:\\cloud\\Dropbox\\9_data\\20150706_Fbench\\log_Fbench_values_used.xlsx"
    
sheet_name='HOMO'

exp_value=[]

B3LYP_value=[]

# read data and make them into an array

col_list = ['B','C','D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z','AA','AB','AC','AD']

row_list = ['7']

for row_name in row_list:
    for col_name in col_list:
        exp_value.append(read_a_cell_in_excel2013.read_a_cell_in_excel(workbook_name,sheet_name,col_name,row_name))
print (exp_value)
    
col_list = ['B','C','D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z','AA','AB','AC','AD']

row_list = ['9']

for row_name in row_list:
    for col_name in col_list:
        B3LYP_value.append(read_a_cell_in_excel2013.read_a_cell_in_excel(workbook_name,sheet_name,col_name,row_name))
print (B3LYP_value)

# prepare the input to np.linalg.lstsq

n=len(B3LYP_value)
print (n)

A=np.array(([[B3LYP_value[j], 1] for j in range(n)]))
B=np.array(exp_value)

# solve the equations
X=np.linalg.lstsq(A,B)[0]
k=X[0]; b=X[1]
T=np.arctan2(A,B)


#plot the points figure
plt.figure(figsize=(9,6))
plt.scatter(B3LYP_value,exp_value,c=T,s=25,alpha=0.4,marker='o')

#plot the fitting curve
start=[min(B3LYP_value),max(B3LYP_value)]
end=[min(B3LYP_value)*k+b,max(B3LYP_value)*k+b]

plt.plot(start,end,'b',linewidth=2.0)

plt.xlim(-6.5,-4.5)
plt.ylim(-6.5,-4.5)
plt.show()
```
### scipy.stats.linregress

* scipy.stats.linregress(x,y=none)
  return the least-squares solution to a linear matrix equation in the form of ax=b

    Input: x,y are array ------ we could use np.array to transform numbers into array

    Return: 
    slope: slope of the fitting curve in float 
    intercept: intercept of the fitting curve in float
    r-value: correlation coefficient
    p-value: two-sided p-value for a hypothesis test whose null hypothesis is that the slope is zero, in float
    stderr: standard error of the estimate in float

    example:

```
import numpy as np
from matplotlib import pyplot as plt
import read_a_cell_in_excel2013
from scipy import stats

# load data file
workbook_name="D:\\cloud\\Dropbox\\9_data\\20150706_Fbench\\log_Fbench_values_used.xlsx"
    
sheet_name='HOMO'

exp_value=[]

B3LYP_value=[]

# read data and make them into an array

col_list = ['B','C','D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z','AA','AB','AC','AD']

row_list = ['7']

for row_name in row_list:
    for col_name in col_list:
        exp_value.append(read_a_cell_in_excel2013.read_a_cell_in_excel(workbook_name,sheet_name,col_name,row_name))
print (exp_value)
    
col_list = ['B','C','D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z','AA','AB','AC','AD']

row_list = ['9']

for row_name in row_list:
    for col_name in col_list:
        B3LYP_value.append(read_a_cell_in_excel2013.read_a_cell_in_excel(workbook_name,sheet_name,col_name,row_name))
print (B3LYP_value)

# prepare the input to np.linalg.lstsq

n=len(B3LYP_value)
print (n)

B=np.array(exp_value)
A=np.array(B3LYP_value)


# solve the equations
slope, intercept, r_value, p_value, std_err=stats.linregress(A,B)

k=slope
b=intercept

#plot the points figure
plt.figure(figsize=(9,6))
plt.scatter(B3LYP_value,exp_value,s=25,alpha=0.4,marker='o')

#plot the fitting curve
start=[min(B3LYP_value),max(B3LYP_value)]
end=[min(B3LYP_value)*k+b,max(B3LYP_value)*k+b]

plt.plot(start,end,'b',linewidth=2.0)

plt.xlim(-6.5,-4.5)
plt.ylim(-6.5,-4.5)
plt.show()
```


### sklearn.linear_model.LinearRegression


* sklearn.linear_model.LinearRegression(fit_intercept=True, normalize=False, copy_X=True, n_jobs=1)[source]


	This is a wrap of scipy.But more focused on prediction and predictor.