
# Post \#62895219 [Link](https://stackoverflow.com/questions/62895219/)

## Getting Error in airflow DAG unsupported operand type(s) for >>: 'list' and 'list'. Sequential and Parallel execution of tasks

**Vote**: 7 (449/702) **Views**: 8512 (446/702) 

**Internal ID** \#1-3-275

Created at 2020-07-14 12:28:22

Tags: `python` `airflow` `directed-acyclic-graphs` `airflow-scheduler`

----------

#### Metadata:

Area: `Data Science & Machine Learning`

Language: `python` (full parsed tag list: `python`)

----------

**Notepad**


----------

I am new to Apache airflow and DAG. There are total 6 tasks in the DAG (task1, task2, task3, task4, task5, task6). But at the time of running the DAG we are getting the error below.

Below is my code for the DAG. Please help. I am new to airflow.
```
from airflow import DAG
from datetime import datetime
from airflow.providers.databricks.operators.databricks import DatabricksSubmitRunOperator


default_args = {
    'owner': 'airflow',
    'depends_on_past': False
}

dag = DAG('DAG_FOR_TEST',default_args=default_args,schedule_interval=None,max_active_runs=3, start_date=datetime(2020, 7, 14)) 


#################### CREATE TASK #####################################   

task_1 = DatabricksSubmitRunOperator(
    task_id='task_1',
    databricks_conn_id='connection_id_details',
    existing_cluster_id='{{ dag_run.conf.clusterId }}',
    libraries= [
        {
        'jar': 'dbfs:/task_1/task_1.jar'
        }        
        ],
    spark_jar_task={
        'main_class_name': 'com.task_1.driver.TestClass1',
        'parameters' : [
            '{{ dag_run.conf.json }}'       
        ]
    }
)



    
task_2 = DatabricksSubmitRunOperator(
    task_id='task_2',
    databricks_conn_id='connection_id_details',
    existing_cluster_id='{{ dag_run.conf.clusterId }}',   
    libraries= [
        {
        'jar': 'dbfs:/task_2/task_2.jar'
        }        
        ],
    spark_jar_task={
        'main_class_name': 'com.task_2.driver.TestClass2',
        'parameters' : [
            '{{ dag_run.conf.json }}'                               
        ]
    }
)
    
task_3 = DatabricksSubmitRunOperator(
    task_id='task_3',
    databricks_conn_id='connection_id_details',
    existing_cluster_id='{{ dag_run.conf.clusterId }}',   
    libraries= [
        {
        'jar': 'dbfs:/task_3/task_3.jar'
        }        
        ],
    spark_jar_task={
        'main_class_name': 'com.task_3.driver.TestClass3',
        'parameters' : [
            '{{ dag_run.conf.json }}'   
        ]
    }
) 

task_4 = DatabricksSubmitRunOperator(
    task_id='task_4',
    databricks_conn_id='connection_id_details',
    existing_cluster_id='{{ dag_run.conf.clusterId }}',
    libraries= [
        {
        'jar': 'dbfs:/task_4/task_4.jar'
        }        
        ],
    spark_jar_task={
        'main_class_name': 'com.task_4.driver.TestClass4',
        'parameters' : [
            '{{ dag_run.conf.json }}'   
        ]
    }
) 

task_5 = DatabricksSubmitRunOperator(
    task_id='task_5',
    databricks_conn_id='connection_id_details',
    existing_cluster_id='{{ dag_run.conf.clusterId }}',
    libraries= [
        {
        'jar': 'dbfs:/task_5/task_5.jar'
        }        
        ],
    spark_jar_task={
        'main_class_name': 'com.task_5.driver.TestClass5',
        'parameters' : [
            'json ={{ dag_run.conf.json }}' 
        ]
    }
) 

task_6 = DatabricksSubmitRunOperator(
    task_id='task_6',
    databricks_conn_id='connection_id_details',
    existing_cluster_id='{{ dag_run.conf.clusterId }}',
    libraries= [
        {
        'jar': 'dbfs:/task_6/task_6.jar'
        }        
        ],
    spark_jar_task={
        'main_class_name': 'com.task_6.driver.TestClass6',
        'parameters' : ['{{ dag_run.conf.json }}'   
        ]
    }
) 

#################### ORDER OF OPERATORS ###########################  
 
    task_1.dag = dag
    task_2.dag = dag
    task_3.dag = dag
    task_4.dag = dag
    task_5.dag = dag
    task_6.dag = dag

task_1 >> [task_2 , task_3] >> [ task_4 , task_5 ] >> task_6
```



----------
        
## Answer \#0

**Accepted** Vote: 11

Created at 2020-07-23 12:05:15

------------

What is your desired Task Dependency? Do you want to run `task_4` after `task_2` only or after `task_2` and `task_3`
Based on that answer, use one of the following:
(use this if task_4 should run after both `task_2` and `task_3` are completed)
```
task_1 >> [task_2 , task_3]
task_2 >> [task_4, task_5] >> task_6
task_3 >> [task_4, task_5]
```

OR
(use this if task_4 should run after `task_2` is completed and `task_5` should run after `task_3` is completed)
```
task_1 >> [task_2 , task_3]
task_2 >> task_4
task_3 >> task_5
[task_4, task_5] >> task_6
```

A tip, you don't need to do the following:
```
task_1.dag = dag
    task_2.dag = dag
    task_3.dag = dag
    task_4.dag = dag
    task_5.dag = dag
    task_6.dag = dag
```

You can pass the `dag` parameter to your task itself, example:
```
task_6 = DatabricksSubmitRunOperator(
    task_id='task_6',
    dag=dag,
    databricks_conn_id='connection_id_details',
    existing_cluster_id='{{ dag_run.conf.clusterId }}',
    libraries= [
        {
        'jar': 'dbfs:/task_6/task_6.jar'
        }        
        ],
    spark_jar_task={
        'main_class_name': 'com.task_6.driver.TestClass6',
        'parameters' : ['{{ dag_run.conf.json }}'   
        ]
    }
)
```

or use DAG as your context manager as documented in [https://airflow.apache.org/docs/stable/concepts.html#context-manager](https://airflow.apache.org/docs/stable/concepts.html#context-manager) and Point (1) in [https://medium.com/datareply/airflow-lesser-known-tips-tricks-and-best-practises-cf4d4a90f8f](https://medium.com/datareply/airflow-lesser-known-tips-tricks-and-best-practises-cf4d4a90f8f)


------------
    
    
## Answer \#1

 Vote: 8

Created at 2020-07-14 12:40:45

------------

Airflow task dependencies can't handle [list]>>[list]. Easiest way around this is to specify your dependencies over multiple lines:
```
task_1 >> [task_2 , task_3]
task_2 >> [task_4, task_5]
task_3 >> [task_4, task_5]
[task_4 , task_5 ] >> task_6
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2021-09-21 13:23:52

------------

Same problem here, running Version : 1.10.14+composer GCP:
Code:
```
i = 0   # iterator
while i < len(LIST_OF_OPS):   # list of operators which I have aggregated beforehand
    LIST_OF_OPS[i] >> LIST_OF_OPS[i+1:i+8] # one task queues up seven more
    i += 7 # in order not to start all of them in parallel, I increase the iterator to one less than the number of tasks started just now; a chain is formed.
```

This solution allows me to dynamically generate the tasks, and iterate over them. Currently in use for a workflow with 280 tasks.
[](https://i.stack.imgur.com/C0auf.png)


------------
    
    
## Answer \#3

 Vote: 3

Created at 2022-09-14 01:38:25

------------

My solution was to first encapsulate all the leading tasks with a `DummyOperator` task, then use the same `DummyOperator` task to initiate the next list of tasks.
[](https://i.stack.imgur.com/8VGuq.png)
```
with dag:

    task_1 = DummyOperator(
        task_id = 'real_operation_1'
    )

    task_2 = DummyOperator(
        task_id = 'real_operation_2'
    )

    task_3 = DummyOperator(
        task_id = 'dummy_operation'
    )

    task_4 = DummyOperator(
        task_id = 'real_operation_3'
    )

    task_5 = DummyOperator(
        task_id = 'real_operation_4'
    )

    [task_1, task_2] >> task_3 >> [task_4, task_5]
```



------------
    
    
## Answer \#4

 Vote: 0

Created at 2022-09-01 03:12:03

------------

taskgroup may be useful to address the grouping of some task and adding dynamic dependencies between taskgroups


------------
    
    