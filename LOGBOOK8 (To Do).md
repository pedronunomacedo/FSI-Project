# WEEK 8 and 9

## SEEDs LAB

Link: https://github.com/seed-labs/seed-labs/tree/master/category-web/Web_SQL_Injection

### Task 1
#### Get familiar with SQL Statements

- For this task, we want to display the information of the employee 'Alice'. For that, we need to use the mysql tool to exploe the platform database.

```bash
mysql> select * from credential;
```

![Terminal print of task 1 - credential table](img/category_web/SQL_Injection_Attack/Task1(credential_table).png)

### Task2
#### Task2.1 - SQL Injection Attack on SELECT Statement

- The objective of this task is to find a way to login into the administrator account, using the knowledge accquire so far, to gte information about all employees.
- To achieve this, we inject the code below on the username field of the login form.

![Terminal print of task 2 - login page](img/category_web/SQL_Injection_Attack/Task2_1_1(login_as_admin).JPG)

![Terminal print of task 2 - admin account](img/category_web/SQL_Injection_Attack/Task2_1_2(admin_account).JPG)

#### Task2.2 - SQL Injection Attack from command line

- The goal of this task it's simmilar to the previous one, but this time using the command line to enter on the administrator account.
- To do so, we used the bellow command:

```bash
curl "www.seed-server.com/unsafe_home.php?username=Admin%27or%27a%27=%27a&Password=123"
```

#### Task2.3 - Append a new SQL statement

- We cannot inject two sql queries in the system. **This restriction is due to the use of `mysql query function`.**
- In order for this to work, we would need to replace this function with the the **`mysql's multi-query`** function that allows for multiple query injection.


### Task3 - SQL Injection Attack on UPDATE Statement
#### Task3.1 - Modify your own salary

- The goal of this task is to modify your salary on the database system using the UPDATE statement.
- To do so, we inserted on the _Nickname_ field the following query to change the salary.

```sql
alice', salary='9999
```

![Terminal print of task 3_1 - modify Alice salary](img/category_web/SQL_Injection_Attack/Task3_1_1(modify_alice_salary).JPG)

- This injection resulted on the following change on the profile page:

![Terminal print of task 3_1 - modified Alice salary](img/category_web/SQL_Injection_Attack/Task3_1_2(modified_alice_salary).JPG)

#### Task3.2 - Modify other peoples' salary

- The main goal of this task is to change the salary of your boss Boby.
- To do so, we used a similar way of the previous task, but now updating the salary of Boby on Alice's account.

![Terminal print of task 3_2 - modify Boby salary](img/category_web/SQL_Injection_Attack/Task3_2_1(modify_boby_salary).JPG)

- This resulted on the following change:

![Terminal print of task 3_2 - modified Boby salary](img/category_web/SQL_Injection_Attack/Task3_2_2(modified_boby_salary).JPG)

#### Task3.3 - Modify other's people' password
- In this task, we want to change the password of Boby's account.
- In order to do this, we used SQL Injection on the Nickname field on the _Edit profile_ section.
- **Attention:** The password to put on the UPDATE query needs to be encrypted in order to work.
- In this example, we changed the password to "123456", that encrypted to sha1 is "7c4a8d09ca3762af61e59520943dc26494f8941b".

![Terminal print of task 3_3 - modify Boby's password](img/category_web/SQL_Injection_Attack/Task3_3_1(modify_boby_password).JPG)

![Terminal print of task 3_3 - trying to login as Boby](img/category_web/SQL_Injection_Attack/Task3_3_2(trying_to_login_as_boby).JPG)

![Terminal print of task 3_3 - on Boby's account](img/category_web/SQL_Injection_Attack/Task3_3_3(on_boby_account).JPG)

