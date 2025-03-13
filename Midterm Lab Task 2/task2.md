# Midterms Lab Task 2 ! - Data Cleaning and Preparation using POWER QUERY
in this lab activity we were tasked to clean a set of data using EXCEL POWER QUERY. Additionally in this file will show the process of cleaning the Data using POEWR QUERY EXCEL
## Step-by-Step Process
### Step 1: Download and Load Data  
1. Download the dataset (Uncleaned_DS_jobs.csv)  
2. Open Excel  
3. Go to Data → New Query → Open File → Text/CSV  
4. Click Load and then Edit using Power Query Editor  

### Step 2: Duplicate Raw Data  
1. Right-click the dataset in the Queries pane  
2. Select Duplicate  

### Step 3: Clean Salary Data  
1. Select the Salary Estimate column  
2. Go to Transform Menu → Extract → Text Before Delimiter  
3. Type "(" and click OK  
4. Create two new columns: Min Salary and Max Salary  
   - Select Salary Estimate column → Add Column Menu → Column from Examples → From Selections  
   - Type the first min salary value and press Enter (all rows will auto-fill)  
   - Rename the column to "Min Sal"  
   - Repeat the process for Max Salary  

### Step 4: Add Role Type Column  
1. Go to Add Column Menu → Custom Column  
2. Rename the column to "Role Type"  
3. Use this logic:  
   - If Job Title contains "Data Scientist" → Assign "Data Scientist"  
   - If Job Title contains "Data Analyst" → Assign "Data Analyst"  
   - If Job Title contains "Data Engineer" → Assign "Data Engineer"  
   - If Job Title contains "Machine Learning" → Assign "Machine Learning Engineer"  
   - Otherwise, assign "Other"  
4. Change the column type to Text  

### Step 5: Split Location Column  
1. Select the Location column  
2. Add a Custom Column with corrections:  
   - If Location = "New Jersey" → Assign ", NJ"  
   - If Location = "Remote" or "United States" → Assign ", Other"  
   - If Location = "Texas" → Assign ", TX"  
   - If Location = "California" → Assign ", CA"  
3. Click OK, then select the new column  
4. Go to Transform → Split Column → By Delimiter (comma ",")  
5. Click OK  
6. Rename the second split column to "State Abbreviations"  
7. Check and replace incorrect values (e.g., "Anne Rundell" → "MA")  

### Step 6: Split Size Column  
1. Create two new columns: MinCompanySize and MaxCompanySize  
2. Use the same method as Salary Estimate to split values  

### Step 7: Handle Negative Values  
1. Filter out -1s from the Competitors column  
2. Filter out 0s from the Revenues column  
3. Remove -1s from the Industry column  

### Step 8: Clean Company Names  
1. Remove any extra characters or ratings after company names  

### Step 9: Copy Cleaning Steps as Proof  
1. Go to Home Menu → Click Advanced Editor  
2. Copy and save the code in your portfolio  

### Step 10: Reshape and Group Data  
#### Group by Role Type  
1. Duplicate the raw data → Rename it as "Sal By Role Type dup"  
2. Select only Role Type, Min Salary, and Max Salary columns  
3. Change Min and Max Salary type to currency  
4. Multiply values by 1000 (Numbers Column → Standard → Multiply → Type 1000)  
5. Group rows by Role Type and get the average for Min and Max Salary  

#### Group by Company Size  
1. Create a reference of raw data → Rename it as "Sal By Role Size ref"  
2. Select only Size, Min Salary, and Max Salary columns  
3. Change Min and Max Salary type to currency  
4. Multiply values by 1000  
5. Group rows by Size and get the average for Min and Max Salary  


### Step 11: Merge State Mapping  
1. Click Unclean DS Jobs  
2. Right-click in the Queries pane → New Query → Open Workbook State Mapping  
3. Select the columns and click OK  
4. Select Uncleaned DS Jobs query  
5. Choose the State Abbreviation column in both queries  
6. Click Merge → Click OK  
7. Rename the merged column as "State Full Name"  
8. Remove nulls and blanks  



### Step 12: Group by State  
1. Create a reference of raw data → Rename it as "Sal By State ref"  
2. Select only State Full Name, Min Salary, and Max Salary columns  
3. Change Min and Max Salary type to currency  
4. Multiply values by 1000  
5. Group rows by State Full Name and get the average for Min and Max Salary  



### Step 13: View Query Dependencies  
1. Go to View Menu → Click Dependencies  
2. Check if all queries are correctly linked

### This is the APPLIED STEPS I used for cleaning the data
let\
    Source = Excel.Workbook(File.Contents("C:\Users\COMLAB\Downloads\Uncleaned_DS_jobs.xlsx"), null, true),\
    Uncleaned_DS_jobs_Sheet = Source{[Item="Uncleaned_DS_jobs",Kind="Sheet"]}[Data],\
    #"Promoted Headers" = Table.PromoteHeaders(Uncleaned_DS_jobs_Sheet, [PromoteAllScalars=true]),\
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"index", Int64.Type}, {"Job Title", type text}, {"Salary Estimate", type text}, {"Job Description", type text}, {"Rating", type number}, {"Company Name", type text}, {"Location", type text}, {"Headquarters", type any}, {"Size", type any}, {"Founded", Int64.Type}, {"Type of ownership", type any}, {"Industry", type any}, {"Sector", type any}, {"Revenue", type any}, {"Competitors", type any}}),\
    #"Extracted Text Before Delimiter" = Table.TransformColumns(#"Changed Type", {{"Salary Estimate", each Text.BeforeDelimiter(_, "("), type text}}),\
    #"Inserted Text Between Delimiters" = Table.AddColumn(#"Extracted Text Before Delimiter", "MinSal", each Text.BetweenDelimiters([Salary Estimate], "$", "K"), type text),\
    #"Inserted Text Between Delimiters1" = Table.AddColumn(#"Inserted Text Between Delimiters", "MaxSal", each Text.BetweenDelimiters([Salary Estimate], "$", "K", 1, 0), type text),\
    #"Added Custom" = Table.AddColumn(#"Inserted Text Between Delimiters1", "Role type", each if Text.Contains([Job Title], "Data Scientist") then\
"Data Scientist"\
else if Text.Contains([Job Title], "Data Analyst") then\
"Data Analyst"\
else if Text.Contains([Job Title], "Data Engineer") then\
"Data Engineer"\

else if Text.Contains([Job Title], "Machine Learning") then\
"Machine Learning Engineer"\
else\
"other"),\
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Custom",{{"Role type", type text}}),\
    #"Added Custom1" = Table.AddColumn(#"Changed Type1", "Loc Corrected", each if [Location]= "New Jersey" then ", NJ"\
else if [Location] = "Remote" then ", other"\
else if [Location]= "United States" then ", other"\
else if [Location]= "Texas" then ", TX"\
else if [Location]= "Patuxent" then ", MA"\
else if [Location]= "California" then ", CA"\
else if [Location]= "Utah" then ", UT"\
else [Location]),\
    #"Split Column by Delimiter" = Table.SplitColumn(#"Added Custom1", "Loc Corrected", Splitter.SplitTextByDelimiter(",", QuoteStyle.Csv), {"Loc Corrected.1", "Loc Corrected.2"}),\
    #"Changed Type2" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Loc Corrected.1", type text}, {"Loc Corrected.2", type text}}),\
    #"Replaced Value" = Table.ReplaceValue(#"Changed Type2","Anne Rundell","MA",Replacer.ReplaceText,{"Loc Corrected.2"}),\
    #"Renamed Columns" = Table.RenameColumns(#"Replaced Value",{{"Loc Corrected.2", "State abbreveations"}}),\
    #"Inserted Text Before Delimiter" = Table.AddColumn(#"Renamed Columns", "MinCompanySize", each Text.BeforeDelimiter([Size], " "), type text),\
    #"Inserted Text Between Delimiters2" = Table.AddColumn(#"Inserted Text Before Delimiter", "MaxCompanySize", each Text.BetweenDelimiters([Size], " ", " ", 1, 0), type text),\
    #"Filtered Rows" = Table.SelectRows(#"Inserted Text Between Delimiters2", each ([Competitors] <> -1)),\
    #"Filtered Rows1" = Table.SelectRows(#"Filtered Rows", each ([Industry] <> -1)),\
    #"Split Column by Delimiter1" = Table.SplitColumn(#"Filtered Rows1", "Company Name", Splitter.SplitTextByDelimiter("#(lf)", QuoteStyle.Csv), {"Company Name.1", "Company Name.2"}),\
    #"Changed Type3" = Table.TransformColumnTypes(#"Split Column by Delimiter1",{{"Company Name.1", type text}, {"Company Name.2", type number}}),\
    #"Removed Columns" = Table.RemoveColumns(#"Changed Type3",{"Company Name.2"}),\
    #"Renamed Columns1" = Table.RenameColumns(#"Removed Columns",{{"Company Name.1", "Company Name"}}),\
    #"Removed Columns1" = Table.RemoveColumns(#"Renamed Columns1",{"Job Description"})\
in\
    #"Removed Columns1"\
\
2.0\
\
let\
    Source = Excel.Workbook(File.Contents("C:\Users\COMLAB\Downloads\Uncleaned_DS_jobs.xlsx"), null, true),\
    Uncleaned_DS_jobs_Sheet = Source{[Item="Uncleaned_DS_jobs",Kind="Sheet"]}[Data],\
    #"Promoted Headers" = Table.PromoteHeaders(Uncleaned_DS_jobs_Sheet, [PromoteAllScalars=true]),\
    #"Changed Type" = Table.TransformColumnTypes(#"Promoted Headers",{{"index", Int64.Type}, {"Job Title", type text}, {"Salary Estimate", type text}, {"Job Description", type text}, {"Rating", type number}, {"Company Name", type text}, {"Location", type text}, {"Headquarters", type any}, {"Size", type any}, {"Founded", Int64.Type}, {"Type of ownership", type any}, {"Industry", type any}, {"Sector", type any}, {"Revenue", type any}, {"Competitors", type any}}),\
    #"Extracted Text Before Delimiter" = Table.TransformColumns(#"Changed Type", {{"Salary Estimate", each Text.BeforeDelimiter(_, "("), type text}}),\
    #"Inserted Text Between Delimiters" = Table.AddColumn(#"Extracted Text Before Delimiter", "MinSal", each Text.BetweenDelimiters([Salary Estimate], "$", "K"), type text),\
    #"Inserted Text Between Delimiters1" = Table.AddColumn(#"Inserted Text Between Delimiters", "MaxSal", each Text.BetweenDelimiters([Salary Estimate], "$", "K", 1, 0), type text),\
    #"Added Custom" = Table.AddColumn(#"Inserted Text Between Delimiters1", "Role type", each if Text.Contains([Job Title], "Data Scientist") then\
"Data Scientist"\
else if Text.Contains([Job Title], "Data Analyst") then\
"Data Analyst"\
else if Text.Contains([Job Title], "Data Engineer") then\
"Data Engineer"\
\
else if Text.Contains([Job Title], "Machine Learning") then\
"Machine Learning Engineer"\
else\
"other"),\
    #"Changed Type1" = Table.TransformColumnTypes(#"Added Custom",{{"Role type", type text}}),\
    #"Added Custom1" = Table.AddColumn(#"Changed Type1", "Location Corrected", each if [Location]= "New Jersey" then ", NJ"\
else if [Location] = "Remote" then ", other"\
else if [Location]= "United States" then ", other"\
else if [Location]= "Texas" then ", TX"\
else if [Location]= "Patuxent" then ", MA"\
else if [Location]= "California" then ", CA"\
else if [Location]= "Utah" then ", UT"\
else [Location]),\
    #"Split Column by Delimiter" = Table.SplitColumn(#"Added Custom1", "Location Corrected", Splitter.SplitTextByDelimiter(",", QuoteStyle.Csv), {"Location Corrected.1", "Location Corrected.2"}),\
    #"Changed Type2" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Location Corrected.1", type text}, {"Location Corrected.2", type text}}),\
    #"Replaced Value" = Table.ReplaceValue(#"Changed Type2","Anne Rundell","MA",Replacer.ReplaceText,{"Location Corrected.2"}),\
    #"Renamed Columns" = Table.RenameColumns(#"Replaced Value",{{"Location Corrected.2", "State Abbreveation"}}),\
    #"Inserted Text Before Delimiter" = Table.AddColumn(#"Renamed Columns", "MinCompanySize", each Text.BeforeDelimiter([Size], " "), type text),\
    #"Inserted Text Between Delimiters2" = Table.AddColumn(#"Inserted Text Before Delimiter", "MaxCompanySIze", each Text.BetweenDelimiters([Size], " ", " ", 1, 0), type text),\
    #"Split Column by Delimiter1" = Table.SplitColumn(#"Inserted Text Between Delimiters2", "Company Name", Splitter.SplitTextByDelimiter("#(lf)", QuoteStyle.Csv), {"Company Name.1", "Company Name.2"}),\
    #"Changed Type3" = Table.TransformColumnTypes(#"Split Column by Delimiter1",{{"Company Name.1", type text}, {"Company Name.2", type number}}),\
    #"Removed Columns" = Table.RemoveColumns(#"Changed Type3",{"Company Name.2"}),\
    #"Renamed Columns1" = Table.RenameColumns(#"Removed Columns",{{"Company Name.1", "Company Name"}}),\
    #"Removed Columns1" = Table.RemoveColumns(#"Renamed Columns1",{"Job Description"}),\
    #"Changed Type4" = Table.TransformColumnTypes(#"Removed Columns1",{{"MinSal", Currency.Type}, {"MaxSal", Currency.Type}}),\
    #"Multiplied Column" = Table.TransformColumns(#"Changed Type4", {{"MaxSal", each _ * 1000, Currency.Type}}),\
    #"Multiplied Column1" = Table.TransformColumns(#"Multiplied Column", {{"MinSal", each _ * 1000, Currency.Type}}),\
    #"Filtered Rows" = Table.SelectRows(#"Multiplied Column1", each ([Competitors] <> -1)),\
    #"Filtered Rows1" = Table.SelectRows(#"Filtered Rows", each ([Industry] <> -1)),\
    #"Filtered Rows2" = Table.SelectRows(#"Filtered Rows1", each ([Founded] <> -1))\
in\
    #"Filtered Rows2"\
## Here is the screenshot of the transformation process of the data
### Here's the Data output before I started to clean it
![images](https://github.com/DavidLenard/EDM-David/blob/main/Images/Unclean1.png)
## Here are the screenshot of the Advanced Editor
![Image](https://github.com/DavidLenard/EDM-David/blob/main/Images/Editor.png)
### Here's the screenshot of my Data output after data cleaning (see screenshot)
![Image](https://github.com/DavidLenard/EDM-David/blob/main/Images/Cleaned.png)


### Here's the screenshot of Sal By Role type (see screenshot)
![Image](https://github.com/DavidLenard/EDM-David/blob/main/Images/Salbyroletype.png)
### Here's the screenshot of Sal By Role Size (see screenshot)
![Image](https://github.com/DavidLenard/EDM-David/blob/main/Images/Salbyref.png)
### Here's the screenshot of Sal By State (see screenshot)
![Image](https://github.com/DavidLenard/EDM-David/blob/main/Images/Salbystateref.png)
### Here's the screenshot of States (see screenshot)
![Image](https://github.com/DavidLenard/EDM-David/blob/main/Images/States.png)
### Here's the screenshot of the Query Dependencies
![Image](https://github.com/DavidLenard/EDM-David/blob/main/Images/queries.png)
