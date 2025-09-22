# Sales-data-SAS-
The dataset was extracted from Kaggle .first I identified and handled missing values,removed duplicates, standardized  text values like gender.. etc.convert date formats,  rename column names
. Unzip and Import Data

If the file is zipped, first extract it manually (outside SAS), then import the .csv into SAS:

/* Import CSV into SAS */
proc import datafile="/mnt/data/sales_data.csv"
    out=salesdata
    dbms=csv
    replace;
    guessingrows=max;
run;


---

2. Identify & Handle Missing Values

You can check missing values first:

/* Count missing values per column */
proc means data=salesdata n nmiss;
run;

/* Optionally drop rows with missing critical columns */
data salesdata_clean;
    set salesdata;
    if cmiss(of _all_) then delete;   /* deletes rows with any missing */
run;

(You can adjust which columns are critical by specifying them instead of _all_).


---

3. Remove Duplicate Rows

proc sort data=salesdata_clean noduprecs;
    by _all_;
run;


---

4. Standardize Text Values

Example: standardize gender and country names.

data salesdata_clean;
    set salesdata_clean;
    /* Gender standardization */
    if upcase(gender) in ("M", "MALE") then gender="Male";
    else if upcase(gender) in ("F", "FEMALE") then gender="Female";

    /* Country standardization */
    country = propcase(strip(country));   /* e.g., "india" → "India" */
run;


---

5. Convert Date Formats

Assume column = order_date.

data salesdata_clean;
    set salesdata_clean;
    /* Convert string to SAS date */
    order_date_new = input(order_date, ddmmyy10.);  
    format order_date_new ddmmyy10.;
    drop order_date;
    rename order_date_new=order_date;
run;


---

6. Rename Column Headers

If your dataset has messy names, use:

proc datasets lib=work nolist;
    modify salesdata_clean;
    rename
        "Customer Name"n = customer_name
        "Order Date"n   = order_date
        "Sales Amount"n = sales_amount;
quit;


---

7. Check & Fix Data Types

Example: age as integer, sales_amount as numeric.

data salesdata_clean;
    set salesdata_clean;
    /* Convert age */
    age_num = input(age, best12.);
    drop age;
    rename age_num=age;

    /* Ensure sales_amount is numeric */
    sales_amt_num = input(sales_amount, best12.);
    drop sales_amount;
    rename sales_amt_num=sales_amount;
run;
