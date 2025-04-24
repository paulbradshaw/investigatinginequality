# How to: Fetch Deprivation/Ethnicity Data Using XLOOKUP

We want to identify if there are inequalities in vaccination rates between areas with high deprivation and low deprivation. The data on vaccination rates doesn’t include information on the deprivation in each area — but we can add it.

To do this, we need to join the vaccination data with data on deprivation. Here’s how…

## 1. Download the Data

We need to download two sets of data: vaccination rates, and deprivation data. Combining these two datasets should provide new insights.

- The data on vaccination rates comes from the **Cover of vaccination evaluated rapidly (COVER)** programme 2024 to 2025: quarterly data.
  - Specifically, we download the ODS file called **Quarterly vaccination coverage statistics for children aged up to 5 years in the UK (data tables): October to December 2024**.
- The deprivation data comes from the **English indices of multiple deprivation (IMD)**.
  - Specifically, we download the Excel file **File 11: upper-tier local authority summaries**.
- We can also download **ONS ethnicity data** for a backup story: whether there are inequalities in vaccination rates between areas with different proportions of ethnic minorities.
  - Specifically, we download the CSV file **Population by ethnicity and Local Authority 2021**.

## 2. Get the Data into the Same Sheet

To use the XLOOKUP formula, we need both sets of data in the same spreadsheet/workbook.

Start with your focus spreadsheet: the vaccination rates. We are going to look at **Table 5: Completed primary immunisations at aged 12 months in England by upper tier local authority (UTLA) October to December 2024**.

For the deprivation data, we want the sheet called **IMD**. This has the overall deprivation rankings and scores for each authority (other sheets show particular measures of deprivation).

- [Vaccination Data Link](https://www.gov.uk/government/statistics/cover-of-vaccination-evaluated-rapidly-cover-programme-2024-to-2025-quarterly-data)
- [Deprivation Data Link](https://www.gov.uk/government/statistics/english-indices-of-deprivation-2019)
- [Ethnicity Data Link](https://www.ethnicity-facts-figures.service.gov.uk/uk-population-by-ethnicity/national-and-regional-populations/regional-ethnic-diversity/latest/downloads/population-by-ethnicity-and-local-authority-2021.csv)

To get that **IMD** sheet into the vaccinations spreadsheet, right-click on the sheet name and select **Move or copy…**. On the window that appears, select the name of the spreadsheet you want to move or copy this to (tick **Create a copy** if that’s all you want to do), and click OK.

## 3. Write the XLOOKUP Formula

*Note: [I have prepared a spreadsheet where the first two steps have been completed](https://docs.google.com/spreadsheets/d/1baHYv6EzQ4_DscuMp6BtJKEbpenjceoY/edit?usp=sharing&ouid=111945641693315989458&rtpof=true&sd=true) if you want to start from this point.*

Now we need to return to the sheet containing the data we want to start with: the vaccination data.

To match the two datasets, we need to find a column of data that both have in common (it doesn’t have to have the same name). In fact, there are two: a column of local authority names, and a column of codes.

It is best to match on the codes, as there is less opportunity for mismatches (e.g. “Bristol” in the vaccination data is “Bristol, City of” in the IMD data but the code **E06000023** is the same in each).

Start in the sheet we want to pull data into: that will be the vaccination data (**Table5**).

Right-click on column B and select **Insert** (or in Google Sheets: Insert 1 column to the left). This should create a new column B where we can put the new data. The data we are going to fetch is the “proportion of LSOAs [in that authority that are] in most deprived 10% nationally.”

Give the column this title: **LOOKUP: IMD - Proportion of LSOAs in most deprived 10% nationally**.

Underneath that title, in the first row of data (row 7 in **Table5**), type this formula:

```
=XLOOKUP(A7,IMD!A:A,IMD!G:G)
```

When you press Enter, the formula should do the following:
1. Look at the value in cell A7 (e.g. **E09000012**).
2. Locate that same value in sheet **IMD**, column A.
3. Bring back the value in the same row in **IMD**, column G.

For that code, the corresponding value in column G is **0.1111** (11%). If it doesn’t find a match, then it will return a `#N/A` error (see step 5 below).

## 4. Copy the XLOOKUP Formula Down

Once you’re happy the formula is working, copy it down the entire column. You can do this by selecting the cell containing the formula, and then hovering over the bottom right corner so that you see the cursor turn to a black cross.

Double-click on the black cross and it should copy down the whole column for as many cells as there are values to the left.

## 5. Check for Any #N/A Errors, and Manually Clean

If a formula doesn’t find a match, then it will return a `#N/A` error.

To bring any `#N/A` values to the top, sort the column with your lookup results in descending order by selecting a cell in that column and, in the Data menu, clicking the **Z-A** button (in Google Sheets go to **Data > Sort sheet** and select the **Z-A** option).

With all the `#N/A` results together, you can now search for those missing matches manually.

There are two possible causes of an `#N/A` error:
1. The matching value doesn’t exist in the other dataset, or:
2. The matching value exists, but there is a small difference which results in a non-match. This is common with place names where something as simple as “and” being used in one dataset and “&” in the other can result in a mismatch.

Small differences are most likely where you are matching on names, such as the example of “Bristol” not matching “Bristol, City of.”

Common causes include one name using “and” where the other uses “&”, or uses of commas. For example, “Bournemouth Christchurch and Poole” in one dataset is called “Bournemouth, Christchurch and Poole” in the other (note the comma).

With codes, a mismatch is likely to be due to an extra space in the cell — either breaking up the code, or at the start or end where it’s not easy to see. Replacing spaces with **Find & Replace**, or with functions like **TRIM** and **SUBSTITUTE**, can fix this.

If the matching value doesn’t exist, it may be because the other dataset doesn’t cover all the areas that your main dataset does. That may be down to:
- **Geographical coverage** (e.g. the UK vs England) or
- **Categorical coverage** (e.g. all authorities vs one type of authority) or
- **Different timescales** (e.g. some authorities have merged or subdivided in the time between the two datasets). 

For example, the vaccination data has data for “North Northamptonshire” but when the IMD was compiled, this was part of “Northamptonshire,” so we would have to exclude that from any analysis.

To fill the gaps, you may need to fetch extra data (e.g. for other nations or categories or timescales) and add it to the lookup data you started with.

---

Note: One mismatch on codes in our data is Buckinghamshire, which has the code **E10000002** in one dataset and **E06000060** in the other. A search for those two codes brings up an explanation that **E06000060** refers to the Buckinghamshire unitary authority, while **E10000002** refers to the abolished Buckinghamshire county. In April 2020, the Buckinghamshire county was replaced by the unitary authority, **E06000060**. This is confirmed on the Local Authority District to County (April 2020) Lookup in EN which says **“E10000002 - Buckinghamshire county abolished. E06000060 - Buckinghamshire unitary authority created.”**

- [Search Explanation](https://www.google.com/search?q=E10000002+or+E06000060)
- [Local Authority District to County Lookup](https://www.data.gov.uk/dataset/24c7058d-4c04-4a69-9ac5-824f9029b02b/local-authority-district-to-county-april-2020-lookup-in-en)

---

### Summary
1. Download the data
2. Get the data into the same sheet
3. Write the XLOOKUP formula
4. Copy the XLOOKUP formula down
5. Check for any #N/A errors, and manually clean
