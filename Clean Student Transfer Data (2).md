```python
import pandas as pd
import matplotlib.pyplot as plt
import scipy.stats as stats
import seaborn as sb 
import numpy as np
from sklearn.linear_model import LinearRegression
from sklearn import preprocessing
import os
plt.rcParams.update({'font.size': 20})
fig = plt.figure()

pd.options.display.max_rows = 40
pd.set_option('display.max_columns', None)
pd.options.mode.chained_assignment = None #default='warn'
```


```python
df["ENTRY_YEAR"].value_counts()
```


```python
#Load In GSU 2010-2020 Cohort
df = pd.read_excel('GSU_Data_Cohorts_2010-2020_Pulled_2021May20.xlsx')  
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("Pre-", "")
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("SW", "Social Work")
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("Geology", "GeoSciences")
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("Pre-Computer Science", "Computer Science")

```


```python
#Preview Data
df.head()
```


```python
#CLEARINGHOUSEDATA
CLDF=pd.read_csv('Clearinghouse_DETLRPT.csv', dtype={"College State": str, "Degree CIP 1": str})
CLDF2= pd.read_excel('Clearinghouse_DETLRPT - Copy.xlsx', dtype={"College State": str, "Degree CIP 1": str})
```


```python
#Making a Loop for making multiple Bar Graphs of top 15 majors a year

Start = 2010
for Start in range(2010,2021):
    print(Start)
    plt.figure()
    year = (df['ENTRY_YEAR'] == Start)
    df2= df[(year)]  
    print(len(df2.index))
    df2["LATEST_MAJOR_NAME"].value_counts().nlargest(5).sort_values(ascending=True).plot(kind="barh")
    
```


```python
#Making the per captia transfer rates for the majors with the largest volume of students transferring.
major= ['Comp Sci', 'Psych','Undeclared', 'Biology', 'Nursing' ]
percent = [25.3,31.3,31.6,35.4,51.2]

plt.barh(major, percent)
plt.title("Transfer rates per Capita")
plt.xlabel("Percent Transfer")
plt.show()
```


```python
#Loop to find GSU's largest majors
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("Pre-", "")
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("SW", "Social Work")
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("Geology", "GeoSciences")
Start = 2010
for Start in range(2010,2021,4):
    print(Start)
    plt.figure()
    year = (df['ENTRY_YEAR'] == Start)
    df2 = df[(year)]
    print(len(df2.index))
    plt.xlabel("Number of Students Enrolled ")
    plt.title("Top Ten Most Populated Majors in the year" + str((Start))
    df2['LATEST_MAJOR_NAME'].value_counts().nlargest(10).plot(kind="barh")
              
```


```python
#Largest declared majors
plt.title("The Five Most Popular Majors at GSU from 2010-2021")
plt.xlabel("Number Of Students")
plt.ylabel("Major Name")
df['YRB_MAJOR_NAME'].value_counts().nlargest(5).sort_values(ascending=True).plot(kind="barh")
```


```python
#Figuring out total transfers

GSU = CLDF.loc[(CLDF["College Sequence"] == 1.0) & (CLDF["College Name"] == "GEORGIA STATE UNIVERSITY")].drop_duplicates(subset="ID") #This identifies every traditional freshman that began at GSU.


NGSU = CLDF[~CLDF["College Name"].str.contains("GEORGIA STATE UNIVERSITY", na=False)] #This finds every entry for a student not attending GSU

NNGSU = NGSU.loc[(NGSU["College Sequence"] >= 2.0)] #This is supposed to find students that have transferred at least once from the NGSU students. 


TotalTransfer= NNGSU.merge(GSU, on="ID", how='inner', indicator= True).drop_duplicates(subset="ID", keep='first') #This merge is supposed to combine it with the GSU dataframe and return with the IDs that match on both sides, to show all the Transfers from GSU and where they went. 

Blues= TotalTransfer.merge(df, on="ID", how='inner', indicator= 'both') 


T_Majorcount= Blues["YRB_MAJOR_NAME"].value_counts().nlargest()

TotalTransfer

len(TotalTransfer.index)

TotalTransfer["College Sequence_x"].value_counts()

```


```python
#Find number of transfers and total student population each year
fused = df.merge()

Start = 2010
for Start in range(2010, 2021):
    year= (Blues["ENTRY_YEAR"] == Start)
    years = (df["ENTRY_YEAR"] == Start)
    print (len(Blues[year]))
    print (len(df[years]))

```


```python
#top 5 transfer majors each year
Start = 2010
for Start in range(2010,2021):
    Blues["YRB_MAJOR_NAME"]=  Blues["YRB_MAJOR_NAME"].str.replace("Pre-", "")
    Blues["YRB_MAJOR_NAME"]=  Blues["YRB_MAJOR_NAME"].str.replace("SW", "Social Work")
    Blues["YRB_MAJOR_NAME"]=  Blues["YRB_MAJOR_NAME"].str.replace("Geology", "GeoSciences")
    print(Start)
    plt.figure()
    year = ( Blues['ENTRY_YEAR'] == Start)
    df2=  Blues[(year)]
    print(len(df2.index))
    print(df2["YRB_MAJOR_NAME"].value_counts().nlargest(5))
    df2["YRB_MAJOR_NAME"].value_counts().nlargest(5).sort_values(ascending=True).plot(kind="barh")
```


```python
#Top 5 Transfer Major,period

Blues["YRB_MAJOR_NAME"]=  Blues["YRB_MAJOR_NAME"].str.replace("Pre-", "")
Blues["YRB_MAJOR_NAME"]=  Blues["YRB_MAJOR_NAME"].str.replace("SW", "Social Work")
Blues["YRB_MAJOR_NAME"]=  Blues["YRB_MAJOR_NAME"].str.replace("Geology", "GeoSciences")
print(Start)
fig = plt.figure()
fig.suptitle("The Five Most Popular Majors to Transfer Out Of from 2010-2021.", fontsize=16)
plt.xlabel("Number Of Students Transferred")
plt.ylabel("Major Names")
print(Blues["YRB_MAJOR_NAME"].value_counts().nlargest(5))
Blues["YRB_MAJOR_NAME"].value_counts().nlargest(5).sort_values(ascending=True).plot(kind="barh")
```


```python
#This shows the IDS, entry major, starting college(College Name_y ) and transfer college (College Name_x)
green = TotalGSU.loc[((TotalGSU["ENTRY_YEAR"] == 2014))] #Isolates the students that entered in the Year 2010
green["YRB_MAJOR_NAME"].value_counts().nsmallest(20)#Used this to view the smallest majors
yellow = green.loc[(green["YRB_MAJOR_NAME"] == "Psychology")|(green['YRB_MAJOR_NAME']=='Nursing')].drop_duplicates(subset="ID") #Look at the major of choice

yellow[["YRB_MAJOR_NAME", "ID", "College Name_x", "College Name_y"]]
```


```python
#This shows the total number of X majors at GSU 
GSU= CLDF.loc[(CLDF["College Sequence"] == 1.0) & (CLDF["College Name"] == "GEORGIA STATE UNIVERSITY")].drop_duplicates(subset="ID") #This identifies every freshman that began at GSU.
GGSU= GSU.merge(df,on="ID",indicator='Yes') #Merges the Clearinghouse information with the IRB GSU dataframe
blue = GGSU.loc[((GGSU["ENTRY_YEAR"] == 2014))]
purple= blue.loc[(blue["YRB_MAJOR_NAME"] == "Psychology")].drop_duplicates(subset="ID")
purple[["YRB_MAJOR_NAME", "ID", "College Name" ]]

```


```python
#What Majors Have the highest rate of transfer per capita, no adjustment for low majors
TotalGSU=Blues
GGSU= CLDF.loc[(CLDF["College Sequence"] == 1.0) & (CLDF["College Name"] == "GEORGIA STATE UNIVERSITY")].drop_duplicates(subset="ID") 
GGGSU= GGSU.merge(df,on="ID",indicator='fruit')
left= TotalGSU.loc[((TotalGSU["ENTRY_YEAR"] == Start))] #Majors that transfer 
right= GGGSU.loc[((GGGSU["ENTRY_YEAR"] ))] #All students at GSU
plt.xlabel("Percent Transferred")
plt.title("What Majors Have The Highest Rate of Transfer")
plt.ylabel("Major Names")


bar=(GGGSU["YRB_MAJOR_NAME"].value_counts()).nlargest(10).sort_values(ascending=True).plot(kind="barh")

```


```python
#What Majors Have the lowest rate of transfer, no adjustment for low majors
TotalGSU=Blues.merge(df, on='ID',indicator='fruit')
GGSU= CLDF.loc[(CLDF["College Sequence"] == 1.0) & (CLDF["College Name"] == "GEORGIA STATE UNIVERSITY")].drop_duplicates(subset="ID") 
GGGSU= GGSU.merge(df,on="ID",indicator='fruit')
left= TotalGSU.loc[((TotalGSU["ENTRY_YEAR"]))] #Majors that transfer 
right= GGGSU.loc[((GGGSU["ENTRY_YEAR"] ))] #All students at GSU
plt.xlabel("Percent Transferred")
plt.title("What Majors Have The Lowest Rate of Transfer per capita")
plt.ylabel("Major Names")
bar=(TotalGSU["YRB_MAJOR_NAME"].value_counts()/GGGSU["YRB_MAJOR_NAME"].value_counts()).nsmallest(10).plot(kind="barh")
```


```python

#Transfers By Age
#What Majors Have the highest rate of transfer, no adjustment for low majors

TotalGSU=Blues
GGSU= CLDF.loc[(CLDF["College Sequence"] == 1.0) & (CLDF["College Name"] == "GEORGIA STATE UNIVERSITY")].drop_duplicates(subset="ID") 
GGGSU= GGSU.merge(df,on="ID",indicator='fruit')
left= TotalGSU.loc[((TotalGSU["ENTRY_YEAR"]))] #Majors that transfer 
right= GGGSU.loc[((GGGSU["ENTRY_YEAR"] ))] #All students at GSU
plt.xlabel("Percent Transferred")
plt.title("What Ages Have The Highest Rate of Transfer")
plt.ylabel("AGE")

bar=(TotalGSU["AGE"].value_counts()/GGGSU["AGE"].value_counts()).nlargest(10).sort_values(ascending=True).plot(kind="barh")

```


```python
left= TotalGSU #Majors that transfer in the highest rate for Males
right= GGGSU
lleft=left.loc[(left["SEX"] == "M")]
rright=right.loc[(right["SEX"] == "M")]
plt.xlabel("Percent Transferred")
plt.title("Majors With The Highest Rate of Transfer for Males")
plt.ylabel("Major Names")
bar=(lleft['YRB_MAJOR_NAME'].value_counts()/rright["YRB_MAJOR_NAME"].value_counts()).nlargest(5).sort_values(ascending=True).plot(kind="barh")
print (lleft['YRB_MAJOR_NAME'].value_counts())
print(rright["YRB_MAJOR_NAME"].value_counts())
#Possible to DO with latest major name, but it returns strange results... Maybe the Dataset is incomplete? 

```


```python
left= TotalGSU #Majors that transfer in the highest rate for Females
right= GGGSU
lleft=left.loc[(left["SEX"] == "F")]
rright=right.loc[(right["SEX"] == "F")]
plt.xlabel("Percent Transferred")
plt.title("Majors With The Highest Rate of Transfer for Females")
plt.ylabel("Major Names")
bar=(lleft['YRB_MAJOR_NAME'].value_counts()/right["YRB_MAJOR_NAME"].value_counts()).nlargest(5).sort_values(ascending=True).plot(kind="barh")
print (lleft['YRB_MAJOR_NAME'].value_counts())
print(right["YRB_MAJOR_NAME"].value_counts())
```


```python
TotalGSU["SEX"].value_counts() #Count of transfer Sexes
```


```python
GGGSU["SEX"].value_counts() #Count of sexes of traditional first year students at GSU
```


```python
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("Pre-", "") #Should be 2200+ if done correctly #769 psych no space #1386 pre psych
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("SW", "Social Work")
#df["YRB_MAJOR_NAME"].value_counts()
df.loc[(df["YRB_MAJOR_NAME"] == "Social Work")]
```


```python
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("Pre-", "")
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("SW", "Social Work")

left= TotalGSU #Majors that transfer in the highest rate 
right= GGGSU
lleft=left.loc[(left["SEX"] == "F")]
rright=right.loc[(right["SEX"] == "F")]
plt.xlabel("Percent Transferred")
plt.title("Majors With The Highest Rate of Transfer for Females at GSU")
plt.ylabel("Major Names")
bar=(lleft['YRB_MAJOR_NAME'].value_counts()/right["YRB_MAJOR_NAME"].value_counts()).nlargest(10).plot(kind="barh", color='g')
```


```python
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("Pre-", "")
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("SW", "Social Work")

left= TotalGSU #Majors that transfer in the highest rate 
right= GGGSU
lleft=left.loc[(left["SEX"] == "F")]
rright=right.loc[(right["SEX"] == "F")]
lleft['YRB_MAJOR_NAME'].value_counts()
```


```python
left= TotalGSU #Majors that transfer in the highest rate 
right= GGGSU
lleft=left.loc[(left["SEX"] == "M")]
rright=right.loc[(right["SEX"] == "M")]
plt.xlabel("Percent Transferred")
plt.title("Majors With The Highest Rate of Transfer for Males at GSU")
plt.ylabel("Major Names")
bar=(lleft['YRB_MAJOR_NAME'].value_counts()/rright["YRB_MAJOR_NAME"].value_counts()).nlargest(5).plot(kind="barh")
```


```python
#To make comprable graphs to the clearinghouse major graph
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("Pre-", "")
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("SW", "Social Work")
HealthProfessions= GGGSU.loc[(GGGSU["YRB_MAJOR_NAME"] == "Psychology")|(GGGSU['YRB_MAJOR_NAME']=='Nursing')|(GGGSU['YRB_MAJOR_NAME']=='Public Health')|(GGGSU['YRB_MAJOR_NAME']=='Respiratory Therapy')].drop_duplicates(subset="ID").shape[0]

BusinessManagement= GGGSU.loc[(GGGSU["YRB_MAJOR_NAME"] == "Finance")|(GGGSU['YRB_MAJOR_NAME']=='Actuarial Science')|(GGGSU['YRB_MAJOR_NAME']=='BBA Undeclared')|(GGGSU['YRB_MAJOR_NAME']=='Marketing')|(GGGSU['YRB_MAJOR_NAME']=='Business Economics')|(GGGSU['YRB_MAJOR_NAME']=='Accounting')].drop_duplicates(subset="ID").shape[0]

LibArts=  GGGSU.loc[(GGGSU["YRB_MAJOR_NAME"] == "SW")|(GGGSU['YRB_MAJOR_NAME']=='Social Work')|(GGGSU['YRB_MAJOR_NAME']=='Sociology')|(GGGSU['YRB_MAJOR_NAME']=='Religious Studies')|(GGGSU['YRB_MAJOR_NAME']=='Film and Media')|(GGGSU['YRB_MAJOR_NAME']=='English')|(GGGSU['YRB_MAJOR_NAME']=='Philosophy')].drop_duplicates(subset="ID").shape[0]

Biology= GGGSU.loc[(GGGSU["YRB_MAJOR_NAME"]== "Biological Science")].shape[0]
THealthProfessions= TotalGSU.loc[(TotalGSU["YRB_MAJOR_NAME"] == "Psychology")|(TotalGSU['YRB_MAJOR_NAME']=='Nursing')|(TotalGSU['YRB_MAJOR_NAME']=='Public Health')|(TotalGSU['YRB_MAJOR_NAME']=='Respiratory Therapy')].drop_duplicates(subset="ID").shape[0]

TBusinessManagement= TotalGSU.loc[(GGGSU["YRB_MAJOR_NAME"] == "Finance")|(TotalGSU['YRB_MAJOR_NAME']=='Actuarial Science')|(TotalGSU['YRB_MAJOR_NAME']=='BBA Undeclared')|(TotalGSU['YRB_MAJOR_NAME']=='Marketing')|(TotalGSU['YRB_MAJOR_NAME']=='Business Economics')|(TotalGSU['YRB_MAJOR_NAME']=='Accounting')].drop_duplicates(subset="ID").shape[0]

TLibArts=  TotalGSU.loc[(TotalGSU["YRB_MAJOR_NAME"] == "SW")|(TotalGSU['YRB_MAJOR_NAME']=='Social Work')|(TotalGSU['YRB_MAJOR_NAME']=='Sociology')|(TotalGSU['YRB_MAJOR_NAME']=='Religious Studies')|(TotalGSU['YRB_MAJOR_NAME']=='Film and Media')|(TotalGSU['YRB_MAJOR_NAME']=='English')|(TotalGSU['YRB_MAJOR_NAME']=='Philosophy')].drop_duplicates(subset="ID").shape[0]

TBiology= TotalGSU.loc[(TotalGSU["YRB_MAJOR_NAME"]== "Biological Science")].shape[0]
x = ['Health Prof.', 'Business', 'Humanities','Bio', "Comp Sci"]
y1 =[0.41, 0.25,0.21, 0.37,0.27]
y2=[0.59,0.75,0.79,0.63,0.73]
plt.xlabel("Major Names")
plt.ylabel("Percentage")
plt.title("GSU Transfer Rates between Common Majors")
plt.bar(x, y1, color='g')
plt.bar(x,y2, bottom=y1, color='r')
plt.legend(["Transfers", "Total Student Pop"])
plt.show()

DataSci=  GGGSU.loc[(GGGSU["YRB_MAJOR_NAME"] == "Computer Science")|(GGGSU['YRB_MAJOR_NAME']=='Data Science')].shape[0]
TDataSci=TotalGSU.loc[(TotalGSU["YRB_MAJOR_NAME"] == "Computer Science")|(TotalGSU['YRB_MAJOR_NAME']=='Data Science')].shape[0]

TDataSci
```


```python
#Transfer Ratio per Sex Per Major Per Year
df["YRB_MAJOR_NAME"]= df["YRB_MAJOR_NAME"].str.replace("Pre-", "")
Start=2010
TotalGSU=Blues.merge(df, on='ID',indicator='Yes', how="inner") #Merge the Transfer Data with the GSU data
GSU= CLDF.loc[(CLDF["College Sequence"] == 1.0) & (CLDF["College Name"] == "GEORGIA STATE UNIVERSITY")].drop_duplicates(subset="ID") #This identifies every freshman that began at GSU.
GGSU= GSU.merge(df,on="ID",indicator='Yes') #Merges the Clearinghouse information with the IRB GSU dataframe
for Start in range (2010,2021):
    print(Start)
    plt.figure()
    plt.xlabel("Percent Transferred")
    plt.title("What Majors Have The Highest Rate of Transfer for Males in year " + str(Start))
    plt.ylabel("Major Names")
    left= Blues.loc[((Blues["ENTRY_YEAR"] == Start))] #This selects the transfer students only from the year in question in the loop.
    right= GGSU.loc[((GGSU["ENTRY_YEAR"] == Start))] #This selects the current GSU attending students only from the year in question in the loop.
    lleft=left.loc[(left["SEX"] == "M")] #seperates only males
    rright=right.loc[(right["SEX"] == "M")]
    bar=(lleft["YRB_MAJOR_NAME"].value_counts()/rright["YRB_MAJOR_NAME"].value_counts()).nlargest(15).plot(kind="barh") #The idea here is that if I divide the number of students that transferred by the total GSU population, I get the percent that transferred out
    #However it does return those extremely high values. 
    
    
    



```
