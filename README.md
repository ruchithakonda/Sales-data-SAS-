# Salesdata(SAS)
The dataset was extracted from Kaggle .first I identified and handled missing values,removed duplicates, standardized  text values like gender.. etc.convert date formats,  rename column names

/* Import the data*/
proc import datafile="/mnt/data/sales_data.csv"
out=salesdata dbms=csv  replace;
 guessingrows=max;
run;

2. Identify & Handle Missing Values:

/*checking  the missing values first*/

/* Count missing values per column */
proc means data=salesdata n nmiss;
run;
/* Optionally drop rows with missing critical columns */
data salesdata_clean;
    set salesdata;
    if cmiss(of _all_) then delete;   /* deletes rows with any missing */
run;

3. Remove Duplicate Rows:

proc sort data=salesdata_clean nodupkey;
    by _all_;
run;

4. Standardize Text Values:

Example: standardize gender and country names.

data salesdata_clean;
    set salesdata_clean;
    /* Gender standardization */
    if upcase(gender) in ("M", "MALE") then gender="Male";
    else if upcase(gender) in ("F", "FEMALE") then gender="Female";

    /* Country standardization */
    country = propcase(strip(country)); 
run;

5. Convert Date Formats:

Assume column = order_date.

data salesdata_clean;
    set salesdata_clean;
    /* Convert string to SAS date */
    order_date_new = input(order_date, ddmmyy10.);  
    format order_date_new ddmmyy10.;
    drop order_date;
    rename order_date_new=order_date;
run;

6. Rename Column Headers:
/*using datasets procedure*/
proc datasets lib=work nolist;
modify salesdata_clean;
    rename
        "Customer Name"n = customer_name
        "Order Date"n   = order_date
        "Sales Amount"n = sales_amount;
quit;

8. Check & Fix Data Types:
data salesdata_clean;
    set salesdata_clean;
    /* Convert age */
    age_num = input(age, best12.);
    drop age;
    rename age_num=age;

    /* Ensure sales_amount is numeric */
   /*using bestw.technique*/
    sales_amt_num = input(sales_amount, best12.);
    drop sales_amount;
    rename sales_amt_num=sales_amount;
run;
