

# Using Supplemental Logging 

#
#

First let's assume we have a table named contact_details in one of our Production Source Systems as shown below:


```
ID FIRSTNAME LASTNAME MARITAL EDUCATION JOBTITLE POSTCODE ADDRESS                     UPDATED_TIME 
-- --------- -------- ------- --------- -------- -------- --------------------------- -------------
1  kate      smith    married accountan manager  3178     372 burwood hwy,glenwaverly 2018-08-02-15
2  joe       spark    single  student   clerk    3155     7 clarence st , boronia     2018-08-02-15
3  william   harme    single  electrici tradie   2477     8 karoo rd , huntindale     2018-08-02-16
4  nicholas  west     married engineer  tradie   3466     3 high st, scorby           2018-08-02-16
5  gerd      white    single  engineer  business 4884     9 highvale st, point cook   2018-08-02-16

```


Then imagine that **kate** got divorced so an update is executed against the  row where **'id = 1' ** to update her lastname and her marital_status.
If Supplemental logging is not on then the update statement will be logged into the database as below:
``` 
"update contact_details set lastname = 'hunt' , marital_status = 'single' where id = 1" 

```

Then imagine that **kate** got divorced so an update is executed against the  row where **'id = 1' ** to update her lastname and her marital_status.
If Supplemental logging is on all the columns then the update statement will be logged into the database as below:
``` 
"update contact_details set lastname = 'hunt' , marital_status = 'single' , education ='accountan' , jobtitle = = 'manager ' , 
 postcode = 3178, address= '372 burwood hwy,glenwaverly' , updated_time = '2018-08-02-15'  where id = 1" 

```

So we updated the lastname and marital_status fields of the row. Now the data in the table in our system looks like:

```
ID FIRSTNAME LASTNAME MARITAL EDUCATION JOBTITLE POSTCODE ADDRESS                     UPDATED_TIME 
-- --------- -------- ------- --------- -------- -------- --------------------------- -------------
1  kate      hunt     single  accountan manager  3178     372 burwood hwy,glenwaverly 2018-08-02-15
2  joe       spark    single  student   clerk    3155     7 clarence st , boronia     2018-08-02-15
3  william   harme    single  electrici tradie   2477     8 karoo rd , huntindale     2018-08-02-16
4  nicholas  west     married engineer  tradie   3466     3 high st, scorby           2018-08-02-16
5  gerd      white    single  engineer  business 4884     9 highvale st, point cook   2018-08-02-16

```

And the data in the same table on the EDH database looks identical as the prod system:

```
ID FIRSTNAME LASTNAME MARITAL EDUCATION JOBTITLE POSTCODE ADDRESS                     UPDATED_TIME 
-- --------- -------- ------- --------- -------- -------- --------------------------- -------------
1  kate      hunt     single  accountan manager  3178     372 burwood hwy,glenwaverly 2018-08-02-15
2  joe       spark    single  student   clerk    3155     7 clarence st , boronia     2018-08-02-15
3  william   harme    single  electrici tradie   2477     8 karoo rd , huntindale     2018-08-02-16
4  nicholas  west     married engineer  tradie   3466     3 high st, scorby           2018-08-02-16
5  gerd      white    single  engineer  business 4884     9 highvale st, point cook   2018-08-02-16

```

But imagine if instead of appying the update we applied an insert into the table so the data would look like:


```
ID FIRSTNAME LASTNAME MARITAL EDUCATION JOBTITLE POSTCODE ADDRESS                     UPDATED_TIME 
-- --------- -------- ------- --------- -------- -------- --------------------------- -------------
1  kate      smith    married accountan manager  3178     372 burwood hwy,glenwaverly 2018-08-02-15
1  kate      hunt     single  accountan branch m 3178     372 burwood hwy,glenwaverly 2018-08-02-17
2  joe       spark    single  student   clerk    3155     7 clarence st , boronia     2018-08-02-15
3  william   harme    single  electrici tradie   2477     8 karoo rd , huntindale     2018-08-02-16
4  nicholas  west     married engineer  tradie   3466     3 high st, scorby           2018-08-02-16
5  gerd      white    single  engineer  business 4884     9 highvale st, point cook   2018-08-02-16

```

Now we have two rows with **'id = 1'** which is basically history about changes that occured to kate.  Imagine if you are runing a marketing campaign where the target are single customers. As kate situation has changed also the opportunity to offer products according to her current situation also changed so she is now suitable for this campaign.


Let's take a look another example. Assume that customer with **'id = 5' *** move to a wealthy suburb so let's update the address:

```
"update contact_details set address = '12 henderson rd,toorak' , set postcode = 3750 where id = 5"
```

Now the data in our prod system will look like:

```
ID FIRSTNAME LASTNAME MARITAL EDUCATION JOBTITLE POSTCODE ADDRESS                     UPDATED_TIME 
-- --------- -------- ------- --------- -------- -------- --------------------------- -------------
1  kate      smith    married accountan manager  3178     372 burwood hwy,glenwaverly 2018-08-02-15
2  joe       spark    single  student   clerk    3155     7 clarence st , boronia     2018-08-02-15
3  william   harme    single  electrici tradie   2477     8 karoo rd , huntindale     2018-08-02-16
4  nicholas  west     married engineer  tradie   3466     3 high st, scorby           2018-08-02-16
5  gerd      white    single  engineer  business 4884     12 henderson rd,toorak      2018-08-02-16

```

 And in our EDH copy :


```
ID FIRSTNAME LASTNAME MARITAL EDUCATION JOBTITLE POSTCODE ADDRESS                     UPDATED_TIME 
-- --------- -------- ------- --------- -------- -------- --------------------------- -------------
1  kate      smith    married accountan manager  3178     372 burwood hwy,glenwaverly 2018-08-02-15
2  joe       spark    single  student   clerk    3155     7 clarence st , boronia     2018-08-02-15
3  william   harme    single  electrici tradie   2477     8 karoo rd , huntindale     2018-08-02-16
4  nicholas  west     married engineer  tradie   3466     3 high st, scorby           2018-08-02-16
5  gerd      white    single  engineer  business 4884     12 henderson rd,toorak      2018-08-02-16

```

so basically we lost the previous address of our customer.


 But if instead the keep the change into the history table by inserting a new row:

```
ID FIRSTNAME LASTNAME MARITAL EDUCATION JOBTITLE POSTCODE ADDRESS                     UPDATED_TIME 
-- --------- -------- ------- --------- -------- -------- --------------------------- -------------
1  kate      smith    married accountan manager  3178     372 burwood hwy,glenwaverly 2018-08-02-15
1  kate      hunt     single  accountan branch m 3178     372 burwood hwy,glenwaverly 2018-08-02-17
1  joe       spark    single  student   clerk    3155     12 henderson rd,toorak      2018-08-02-22
2  joe       spark    single  student   clerk    3155     7 clarence st , boronia     2018-08-02-15
3  william   harme    single  electrici tradie   2477     8 karoo rd , huntindale     2018-08-02-16
4  nicholas  west     married engineer  tradie   3466     3 high st, scorby           2018-08-02-16
5  gerd      white    single  engineer  business 4884     9 highvale st, point cook   2018-08-02-16
5  gerd      white    single  engineer  business 3750     12 henderson rd,toorak      2018-08-02-16

```

Now we have more information/details about another customer which has moved to a wealthy suburd so he might be a candidate for a upselling campaign where new product might be available to offer due to changes that has occured. I added the **UPDATED_TIME** column in the table and this sort of column are very popular to track down when was the last time the a field in the row was updated.

So with this little example I wanted to show how valuable is keeping the history. There is a gap in our current approach to collect data from our ODS and as we are in the process of testing how we can introduce kafka into our pipelines then I think there is a good opportunity to try this option as well. Data is gold and the data that we overwrite is basically gone. 

