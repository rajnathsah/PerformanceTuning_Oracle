# Bulk Data Processing

Why bulk data processing?  

For data processing in pl/sql, whenever pl/sql engine found sql statement, it passes control to sql engine to process the sql statement.
The more this happens, there is high chance of it causing performance issues. Switching of control betweel pl/sql engine and sql engine is called context switching.

Reducing context switching can help improve performance. 

In below example, for loop is used to update all the employee salary by 10%

```sql
DECLARE
BEGIN
   FOR employee_rec
      IN (SELECT employee_id
            FROM employees)
   LOOP
      UPDATE employees emp
         SET emp.salary = emp.salary + 
             ((emp.salary * 10)/100)
       WHERE emp.employee_id = employee_rec.employee_id;
   END LOOP;
END;
```

Same can be re-written with plan sql, if conditions are simple.

```sql
   UPDATE employees emp
      SET emp.salary =
               emp.salary
             + ((emp.salary * 10)/100);
```

In case, it is not simple, then collections can be used for processing large data in chunks to reduce context switching.

```sql
DECLARE
   TYPE employee_ids_t IS TABLE OF employees.employee_id%TYPE
           INDEX BY PLS_INTEGER; 
   l_employee_ids   employee_ids_t;   

   l_eligible       BOOLEAN;
BEGIN
   SELECT employee_id
     BULK COLLECT INTO l_employee_ids
     FROM employees;

   FORALL indx IN 1 .. l_employee_ids.COUNT
      UPDATE employees emp
         SET emp.salary =
                  emp.salary
                + ((emp.salary * 10)/100)
       WHERE emp.employee_id = l_employee_ids (indx);
END increase_salary;
```
# Bulk collect 

Bulk collect with record

```sql
DECLARE
   TYPE two_cols_rt IS RECORD
   (
      employee_id   employees.employee_id%TYPE,
      salary        employees.salary%TYPE
   );

   TYPE employee_info_t IS TABLE OF two_cols_rt;
   l_employees   employee_info_t;
BEGIN
   SELECT employee_id, salary
     BULK COLLECT INTO l_employees
     FROM employees
    WHERE department_id = 10;
END;
```

Bulk collect with limit  
In case when data to be fetched using bulk collect is huge, there is possibility of error because full data may not fit in session memory. To avoid this, it is always better to process data in chunks.

```sql
DECLARE
   c_limit PLS_INTEGER := 100;

   CURSOR employees_cur
   IS
      SELECT employee_id
        FROM employees
       WHERE department_id = department_id_in;

   TYPE employee_ids_t IS TABLE OF 
      employees.employee_id%TYPE;

   l_employee_ids   employee_ids_t;
BEGIN
   OPEN employees_cur;

   LOOP
      FETCH employees_cur
      BULK COLLECT INTO l_employee_ids
      LIMIT c_limit;

      EXIT WHEN l_employee_ids.COUNT = 0;
   END LOOP;
END;
```

# FORALL
It generates all the DML statements that would have been executed one row at a time, and send them all across to the SQL engine with one context switch. 

1. Each FORALL statement may contain just a single DML statement.  
2. PL/SQL declares the FORALL iterator (indx on line 30 in Listing 4) as an integer, just as it does with a FOR loop.
3. In at least one place in the DML statement, you need to reference a collection and use the FORALL iterator as the index value in that collection.  
4. When using the IN low_value . . . high_value syntax in the FORALL header, the collections referenced inside the FORALL statement must be densely filled. That is, every index value between the low_value and high_value must be defined.  
5. If your collection is not densely filled, you should use the INDICES OF or VALUES OF syntax in your FORALL header.

Update all employee salary by 10% using bulk collect and forall

```sql
DECLARE
   TYPE two_cols_rt IS RECORD
   (
      employee_id   employees.employee_id%TYPE,
      salary        employees.salary%TYPE
   );

   TYPE employee_info_t IS TABLE OF two_cols_rt;
   l_employees   employee_info_t;
BEGIN
   SELECT employee_id, salary
     BULK COLLECT INTO l_employees
     FROM employees
    WHERE department_id = 10;
    
    
    FORALL idx in 1..l_employees.count
        UPDATE employees emp
            SET emp.salary=emp.salary + ((emp.salary*10)/100)
        WHERE emp.employee_id = l_employees(idx).employee_id;
        
  COMMIT;
END;
```
