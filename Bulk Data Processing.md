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

In case, it is not simple, 
