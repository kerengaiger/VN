Load Types Classifying

Database Querying: 
The query I ran to fetch the rae data I created the training set from was:

"select raw.id as raw_id,event_timestamp, weight,weight_raw,acc_ax,acc_ay, steps.id as cycle_id, steps.crane_id, steps.start_time as cycle_start, steps.end_time as cycle_end, steps.step_num,steps.step_start_time, steps.step_end_time, steps.load_type_category_name, steps.load_type_name
from vn.view_cycles_loadtype_ex as raw
join vn.view_cycles_loadtype_ex as steps
on step_start_time <= event_timestamp and step_end_time >= event_timestamp
and raw.crane_id = steps.crane_id
where load_type_name in ('Formworks','Formwork accessories','Concrete','Mesh','Wood')
order by crane_id, event_timestamp"

This query returned 532,227 of raw data.

As part of my features analysis process, I tried to resemble the 'acc_ax' and 'acc_ay' as indicative features. I used the KMeans algorithm to cluster acc_ax and acc_ay to different areas, then used the labels of KMeans as a new column indicating the area of the movement in each row's event.

Features Assignment:

Each cycle has the following features which I trained my model on:
1-4. step i'th time - the time took for the i'th step to complete (Seconds)
5. cycle time - the time took for the cycle to complete (Seconds)
6. cycle start hour - start hour of the cycle (1-24)
7. cycle end hour - end hour of the cycle(1-24)
8-11. step i'th median weight - the median weight over all weights of events recorded in the i'th step
12-15.step i'th avg weigh - the weighted average over all weights of events recorded in the i'th step. The 'weight' of each weight was calculated by the number of events recorded with that specific weight.
16.area = the area of the load rigging step-  the area recorded at the latest event of step 2

Oversampling:
The final training set contained the following labels counts:
Concrete                 36
Formwork accessories      6
Formworks               725
Mesh                    305
Wood                     30
This training set is imbalanced, thus I oversampled it using SMOTE algorithm, and removed the 'Formwork accessories' class since it's too small.After the oversampling I has 514 samples from each class.

The Model:
The model I choose was Logistic Regression model.

Results(precision):
Concrete: 0.87
Formworks: 0.68
Mesh:0.68
Wood:0.77

TODO:
Implement a CNN model to help predicting the loadtype
