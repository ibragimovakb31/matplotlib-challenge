# matplotlib-challenge
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import scipy.stats as st

# Study data files
mouse_metadata_path = "data/Mouse_metadata.csv"
study_results_path = "data/Study_results.csv"

# Read the mouse data and the study results
mouse_metadata = pd.read_csv(mouse_metadata_path)
study_results = pd.read_csv(study_results_path)

# Combine the data into a single dataset
combined_trial_df = pd.merge(mouse_metadata, study_results,
                            how='left', on='Mouse ID')
# Display the data table for preview
combined_trial_df.head()
Out[30]:
Mouse ID	Drug Regimen	Sex	Age_months	Weight (g)	Timepoint	Tumor Volume (mm3)	Metastatic Sites
0	k403	Ramicane	Male	21	16	0	45.000000	0
1	k403	Ramicane	Male	21	16	5	38.825898	0
2	k403	Ramicane	Male	21	16	10	35.014271	1
3	k403	Ramicane	Male	21	16	15	34.223992	1
4	k403	Ramicane	Male	21	16	20	32.997729	1
In [32]:
# Checking the number of mice.
list_of_unique_mice = combined_trial_df["Mouse ID"].unique()

length_of_unique_mice_list = len(list_of_unique_mice)
length_of_unique_mice_list
Out[32]:
249
In [33]:
# Getting the duplicate mice by ID number that shows up for Mouse ID and Timepoint. 
duplicate_mice = combined_trial_df[combined_trial_df.duplicated(subset=['Mouse ID', 'Timepoint'])]
duplicate_mice
Out[33]:
Mouse ID	Drug Regimen	Sex	Age_months	Weight (g)	Timepoint	Tumor Volume (mm3)	Metastatic Sites
909	g989	Propriva	Female	21	26	0	45.000000	0
911	g989	Propriva	Female	21	26	5	47.570392	0
913	g989	Propriva	Female	21	26	10	49.880528	0
915	g989	Propriva	Female	21	26	15	53.442020	0
917	g989	Propriva	Female	21	26	20	54.657650	1
In [34]:
# Optional: Get all the data for the duplicate mouse ID. 

duplicate_mouse_data = combined_trial_df[combined_trial_df["Mouse ID"]=="g989"]
duplicate_mouse_data
Out[34]:
Mouse ID	Drug Regimen	Sex	Age_months	Weight (g)	Timepoint	Tumor Volume (mm3)	Metastatic Sites
908	g989	Propriva	Female	21	26	0	45.000000	0
909	g989	Propriva	Female	21	26	0	45.000000	0
910	g989	Propriva	Female	21	26	5	48.786801	0
911	g989	Propriva	Female	21	26	5	47.570392	0
912	g989	Propriva	Female	21	26	10	51.745156	0
913	g989	Propriva	Female	21	26	10	49.880528	0
914	g989	Propriva	Female	21	26	15	51.325852	1
915	g989	Propriva	Female	21	26	15	53.442020	0
916	g989	Propriva	Female	21	26	20	55.326122	1
917	g989	Propriva	Female	21	26	20	54.657650	1
918	g989	Propriva	Female	21	26	25	56.045564	1
919	g989	Propriva	Female	21	26	30	59.082294	1
920	g989	Propriva	Female	21	26	35	62.570880	2
In [35]:
# Create a clean DataFrame by dropping the duplicate mouse by its ID.
clean_mouse_data = combined_trial_df[combined_trial_df["Mouse ID"]!="g989"]
clean_mouse_data.head()
Out[35]:
Mouse ID	Drug Regimen	Sex	Age_months	Weight (g)	Timepoint	Tumor Volume (mm3)	Metastatic Sites
0	k403	Ramicane	Male	21	16	0	45.000000	0
1	k403	Ramicane	Male	21	16	5	38.825898	0
2	k403	Ramicane	Male	21	16	10	35.014271	1
3	k403	Ramicane	Male	21	16	15	34.223992	1
4	k403	Ramicane	Male	21	16	20	32.997729	1
In [40]:
# Checking the number of mice in the clean DataFrame.
#number_of_mice = len(clean_mouse_data)
#number_of_mice
clean_mouse_data["Mouse ID"].nunique()
Out[40]:
248
Summary Statistics
In [41]:
# Generate a summary statistics table of mean, median, variance, standard deviation, and SEM of the tumor volume for each regimen

# Use groupby and summary statistical methods to calculate the following properties of each drug regimen: 
# mean, median, variance, standard deviation, and SEM of the tumor volume. 
# Assemble the resulting series into a single summary dataframe.
mean = clean_mouse_data.groupby("Drug Regimen").mean()["Tumor Volume (mm3)"]
median = clean_mouse_data.groupby("Drug Regimen").median()["Tumor Volume (mm3)"]
variance = clean_mouse_data.groupby("Drug Regimen").var()["Tumor Volume (mm3)"]
standard_deviation = clean_mouse_data.groupby("Drug Regimen").std()["Tumor Volume (mm3)"]
standard_error = clean_mouse_data.groupby("Drug Regimen").sem()["Tumor Volume (mm3)"]
summary_table = pd.DataFrame({"Mean":mean,
                            "Median":median,
                            "Variance":variance,
                            "Standard Deviation":standard_deviation,
                             "Standard Error":standard_error,
                             })
summary_table
Out[41]:
Mean	Median	Variance	Standard Deviation	Standard Error
Drug Regimen					
Capomulin	40.675741	41.557809	24.947764	4.994774	0.329346
Ceftamin	52.591172	51.776157	39.290177	6.268188	0.469821
Infubinol	52.884795	51.820584	43.128684	6.567243	0.492236
Ketapril	55.235638	53.698743	68.553577	8.279709	0.603860
Naftisol	54.331565	52.509285	66.173479	8.134708	0.596466
Placebo	54.033581	52.288934	61.168083	7.821003	0.581331
Propriva	52.320930	50.446266	43.852013	6.622085	0.544332
Ramicane	40.216745	40.673236	23.486704	4.846308	0.320955
Stelasyn	54.233149	52.431737	59.450562	7.710419	0.573111
Zoniferol	53.236507	51.818479	48.533355	6.966589	0.516398
In [42]:
# Generate a summary statistics table of mean, median, variance, standard deviation, and SEM of the tumor volume for each regimen
summary_table_aggregate = clean_mouse_data.groupby("Drug Regimen").agg({"Tumor Volume (mm3)":["mean", "median", "var", "std", "sem"]})
summary_table_aggregate
# Using the aggregation method, produce the same summary statistics in a single line
Out[42]:
Tumor Volume (mm3)
mean	median	var	std	sem
Drug Regimen					
Capomulin	40.675741	41.557809	24.947764	4.994774	0.329346
Ceftamin	52.591172	51.776157	39.290177	6.268188	0.469821
Infubinol	52.884795	51.820584	43.128684	6.567243	0.492236
Ketapril	55.235638	53.698743	68.553577	8.279709	0.603860
Naftisol	54.331565	52.509285	66.173479	8.134708	0.596466
Placebo	54.033581	52.288934	61.168083	7.821003	0.581331
Propriva	52.320930	50.446266	43.852013	6.622085	0.544332
Ramicane	40.216745	40.673236	23.486704	4.846308	0.320955
Stelasyn	54.233149	52.431737	59.450562	7.710419	0.573111
Zoniferol	53.236507	51.818479	48.533355	6.966589	0.516398
Bar and Pie Charts
In [58]:
# Generate a bar plot showing the total number of timepoints for all mice tested for each drug regimen using Pandas.
mice_in_treatment = clean_mouse_data.groupby(["Drug Regimen"])["Mouse ID"].count()
mice_in_treatment.plot(kind = "bar", x = "Drug Regimen", y = mice_in_treatment) 
mice_in_treatment
Out[58]:
Drug Regimen
Capomulin    230
Ceftamin     178
Infubinol    178
Ketapril     188
Naftisol     186
Placebo      181
Propriva     148
Ramicane     228
Stelasyn     181
Zoniferol    182
Name: Mouse ID, dtype: int64

In [59]:
# Generate a bar plot showing the total number of timepoints for all mice tested for each drug regimen using pyplot.
plt.bar(mice_in_treatment.index.values, mice_in_treatment.values)
plt.xticks(rotation=90)
plt.show()

In [62]:
# Generate a pie plot showing the distribution of female versus male mice using Pandas
sex_count = clean_mouse_data.groupby(["Sex"])["Sex"].count()

sex_count.plot(kind = "pie",autopct="%1.1f%%")
sex_count
Out[62]:
Sex
Female    922
Male      958
Name: Sex, dtype: int64

In [63]:
# Generate a pie plot showing the distribution of female versus male mice using pyplot
plt.pie(sex_count.values, labels=sex_count.index.values,autopct="%1.1f%%")
plt.show()

Quartiles, Outliers and Boxplots
In [65]:
# Calculate the final tumor volume of each mouse across four of the treatment regimens:  
# Capomulin, Ramicane, Infubinol, and Ceftamin

last_timepoint = clean_mouse_data.groupby(["Mouse ID"])["Timepoint"].max()
last_timepoint = last_timepoint.reset_index()

# Merge this group df with the original dataframe to get the tumor volume at the last timepoint
merge_tumor_volume = pd.merge(last_timepoint,clean_mouse_data,on=["Mouse ID", "Timepoint"],how="left")
merge_tumor_volume
Out[65]:
Mouse ID	Timepoint	Drug Regimen	Sex	Age_months	Weight (g)	Tumor Volume (mm3)	Metastatic Sites
0	a203	45	Infubinol	Female	20	23	67.973419	2
1	a251	45	Infubinol	Female	21	25	65.525743	1
2	a262	45	Placebo	Female	17	29	70.717621	4
3	a275	45	Ceftamin	Female	20	28	62.999356	3
4	a366	30	Stelasyn	Female	16	29	63.440686	1
...	...	...	...	...	...	...	...	...
243	z435	10	Propriva	Female	12	26	48.710661	0
244	z578	45	Ramicane	Male	11	16	30.638696	0
245	z581	45	Infubinol	Female	24	25	62.754451	3
246	z795	45	Naftisol	Female	13	29	65.741070	3
247	z969	45	Naftisol	Male	9	30	73.867845	4
248 rows × 8 columns

In [66]:
# Put treatments into a list for for loop (and later for plot labels)


treatment = ["Capomulin", "Ramicane", "Infubinol", "Ceftamin"]
tumor_list = []
for drug in treatment:

    tumor_volume = merge_tumor_volume.loc[merge_tumor_volume ["Drug Regimen"]==drug, "Tumor Volume (mm3)"]
    tumor_list.append(tumor_volume) 
# calculate the IQR and quantitatively determine if there are any potential outliers. 
    quartiles = tumor_volume.quantile([.25,.5,.75])
    lowerq = quartiles[0.25]
    upperq = quartiles[0.75]
    iqr = upperq-lowerq

    print(f"The lower quartile of occupancy is: {lowerq}")
    print(f"The upper quartile of occupancy is: {upperq}")
    print(f"The interquartile range of occupancy is: {iqr}")
    print(f"The the median of occupancy is: {quartiles[0.5]} ")

    lower_bound = lowerq - (1.5*iqr)
    upper_bound = upperq + (1.5*iqr)
    print(f"Values below {lower_bound} could be outliers.")
    print(f"Values above {upper_bound} could be outliers.")


# Determine outliers using upper and lower bounds
    outlier = tumor_volume.loc[(tumor_volume < lower_bound) | (tumor_volume > upper_bound)]
    outlier 
    
The lower quartile of occupancy is: 32.37735684
The upper quartile of occupancy is: 40.1592203
The interquartile range of occupancy is: 7.781863460000004
The the median of occupancy is: 38.1251644 
Values below 20.70456164999999 could be outliers.
Values above 51.83201549 could be outliers.
The lower quartile of occupancy is: 31.56046955
The upper quartile of occupancy is: 40.65900627
The interquartile range of occupancy is: 9.098536719999998
The the median of occupancy is: 36.56165229 
Values below 17.912664470000003 could be outliers.
Values above 54.30681135 could be outliers.
The lower quartile of occupancy is: 54.04860769
The upper quartile of occupancy is: 65.52574285
The interquartile range of occupancy is: 11.477135160000003
The the median of occupancy is: 60.16518046 
Values below 36.83290494999999 could be outliers.
Values above 82.74144559000001 could be outliers.
The lower quartile of occupancy is: 48.72207785
The upper quartile of occupancy is: 64.29983003
The interquartile range of occupancy is: 15.577752179999997
The the median of occupancy is: 59.85195552 
Values below 25.355449580000002 could be outliers.
Values above 87.66645829999999 could be outliers.
In [67]:
# Generate a box plot of the final tumor volume of each mouse across four regimens of interest
plt.boxplot(tumor_list, labels= treatment )
Out[67]:
{'whiskers': [<matplotlib.lines.Line2D at 0x7fb4296909d0>,
  <matplotlib.lines.Line2D at 0x7fb429690ca0>,
  <matplotlib.lines.Line2D at 0x7fb4296a92b0>,
  <matplotlib.lines.Line2D at 0x7fb4296a9640>,
  <matplotlib.lines.Line2D at 0x7fb4296b7be0>,
  <matplotlib.lines.Line2D at 0x7fb4296b7f70>,
  <matplotlib.lines.Line2D at 0x7fb4296cb550>,
  <matplotlib.lines.Line2D at 0x7fb4296cb8e0>],
 'caps': [<matplotlib.lines.Line2D at 0x7fb42969e070>,
  <matplotlib.lines.Line2D at 0x7fb42969e400>,
  <matplotlib.lines.Line2D at 0x7fb4296a99d0>,
  <matplotlib.lines.Line2D at 0x7fb4296a9d60>,
  <matplotlib.lines.Line2D at 0x7fb4296c0340>,
  <matplotlib.lines.Line2D at 0x7fb4296c06d0>,
  <matplotlib.lines.Line2D at 0x7fb4296cbc70>,
  <matplotlib.lines.Line2D at 0x7fb4296d7040>],
 'boxes': [<matplotlib.lines.Line2D at 0x7fb429690580>,
  <matplotlib.lines.Line2D at 0x7fb42969eee0>,
  <matplotlib.lines.Line2D at 0x7fb4296b7850>,
  <matplotlib.lines.Line2D at 0x7fb4296cb1c0>],
 'medians': [<matplotlib.lines.Line2D at 0x7fb42969e790>,
  <matplotlib.lines.Line2D at 0x7fb4296b7130>,
  <matplotlib.lines.Line2D at 0x7fb4296c0a60>,
  <matplotlib.lines.Line2D at 0x7fb4296d73d0>],
 'fliers': [<matplotlib.lines.Line2D at 0x7fb42969eb20>,
  <matplotlib.lines.Line2D at 0x7fb4296b74c0>,
  <matplotlib.lines.Line2D at 0x7fb4296c0df0>,
  <matplotlib.lines.Line2D at 0x7fb4296d7760>],
 'means': []}

Line and Scatter Plots
In [69]:
# Generate a line plot of tumor volume vs. time point for a mouse treated with Capomulin
capomulin_mice = clean_mouse_data.loc[clean_mouse_data["Drug Regimen"] == "Capomulin"]

mouse_id = capomulin_mice.loc[clean_mouse_data["Mouse ID"] == "j246"] 
x = mouse_id["Timepoint"] 
y = mouse_id["Tumor Volume (mm3)"] 
plt.xlabel('Timepoint')
plt.ylabel('Tumor Volume (mm3)')
plt.plot(x,y)
Out[69]:
[<matplotlib.lines.Line2D at 0x7fb42973ba90>]

In [76]:
# Generate a scatter plot of average tumor volume vs. mouse weight for the Capomulin regimen
capomulin_average = capomulin_mice.groupby("Mouse ID").mean()
x = capomulin_average["Weight (g)"]
y = capomulin_average["Tumor Volume (mm3)"]

plt.scatter(x,y)
plt.xlabel('Weight')
plt.ylabel('Tumor Volume')
plt.show()

Correlation and Regression
In [75]:
# Calculate the correlation coefficient and linear regression model 
# for mouse weight and average tumor volume for the Capomulin regimen
x = capomulin_average["Weight (g)"]
y = capomulin_average["Tumor Volume (mm3)"]
corr=round(st.pearsonr(x,y)[0],2)
(slope, intercept, rvalue, pvalue, stderr) = st.linregress(x, y)

line_eq = "y = " + str(round(slope,2)) + "x + " + str(round(intercept,2))
plt.scatter(x,y)
plt.plot(x,regress_values,"r-")
plt.xlabel("Weight (g)")
plt.ylabel("Tumor Volume (mm3)")
plt.show()
