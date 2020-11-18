

# Enbling Supplemental Logging 

#
#

First let's assume we have a table named contact_details in one of our Production Source Systems as shown below:


```
ID FIRSTNAME LASTNAME MARITAL EDUCATION JOBTITLE POSTCODE ADDRESS                     UPDATED_TIME 
-- --------- -------- ------- --------- -------- -------- --------------------------- -------------
1  kate      smith    married accountant manager 3178     372 burwood hwy,glenwaverly 2018-08-02-15
2  joe       spark    single  student   clerk    3155     7 clarence st , boronia     2018-08-02-15
3  william   harme    single  electrici tradie   2477     8 karoo rd , huntindale     2018-08-02-16
4  nicholas  west     married engineer  tradie   3466     3 high st, scorby           2018-08-02-16
5  gerd      white    single  engineer  business 4884     9 highvale st, point cook   2018-08-02-16

```


Then imagine that **kate** got divorced so an update is executed against the  row where **'id = 1' ** to update her lastname and her marital_status.
If Supplemental logging is not on then the update statement will be logged into the database as below:
``` 
Actual SQL :
"update contact_details set lastname = 'hunt' , marital_status = 'single' where id = 1" 

How will be stored in DB:
"update contact_details set lastname = 'hunt' , marital_status = 'single' where id = 1 and ROWID = 'AAAQQ3AABAAAYN5AAB'" 
```

Then imagine that **kate** got divorced so an update is executed against the  row where **'id = 1' ** to update her lastname and her marital_status.
If Supplemental logging is on all the columns then the update statement will be logged into the database as below:
``` 
Actual SQL :
"update contact_details set lastname = 'hunt' , marital_status = 'single' where id = 1" 

How will be stored in DB:
"update contact_details set lastname = 'hunt' , marital_status = 'single' where id = 1 and education ='accountant' and jobtitle = 'manager ' and
 postcode = 3178 and  address= '372 burwood hwy,glenwaverly' and  updated_time = '2018-08-02-15' and ROWID = 'AAAQQ3AABAAAYN5AAA'" 

So we updated the lastname and marital_status fields of the row. Now the data in the table in our system looks like:

```
ID FIRSTNAME LASTNAME MARITAL EDUCATION JOBTITLE POSTCODE ADDRESS                     UPDATED_TIME 
-- --------- -------- ------- --------- -------- -------- --------------------------- -------------
1  kate      hunt     single  accountant manager  3178    372 burwood hwy,glenwaverly 2018-08-02-15
2  joe       spark    single  student   clerk    3155     7 clarence st , boronia     2018-08-02-15
3  william   harme    single  electrici tradie   2477     8 karoo rd , huntindale     2018-08-02-16
4  nicholas  west     married engineer  tradie   3466     3 high st, scorby           2018-08-02-16
5  gerd      white    single  engineer  business 4884     9 highvale st, point cook   2018-08-02-16

```

```
So this little example shows how enabling supplemental logging will help to record additional information in the database logs that can enable us to reconstruct records affected by UPDATES even though only a few columns are updated. 
``` 

```

The cleanser comes and strips all and reconstruct a row like below:
ID FIRSTNAME LASTNAME MARITAL EDUCATION  JOBTITLE POSTCODE          ADDRESS            UPDATED_TIME 
1  kate      hunt     single  accountant manager  3178     372 burwood hwy,glenwaverly 2018-08-02-15
```
