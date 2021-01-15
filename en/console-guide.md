## Compute > Auto Scale > Console Guide

## Instance Templates
### Creating Instance Templates

To create an instance through auto scale, an instance template must be created first.
Below are required to create an instance template:

<table class="it">
  <tr>
    <th>Classification</th>
    <th>Item</th>
    <th>Description</th>
  </tr>
  <tr>
    <td rowspan="2">Template Information</td>
    <td>Name</td>
    <td>Name of Instance Template</td>
  </tr>
  <tr>
    <td>Description</td>
    <td>Description of an instance template. Up to 255 characters for English</td>
  </tr>
  <tr>
    <td rowspan="8">Instance Information</td>
    <td>Image</td>
    <td>An instance image to be created with an instance template</td>
  </tr>
  <tr>
    <td>Name</td>
    <td>Name of an instance to be created<br>All instances created with a same instance template are named the same</td>
  </tr>
  <tr>
    <td>Availability Area </td>
    <td>Area where an instance is to be created </td>
  </tr>
  <tr>
    <td>Flavors </td>
    <td>Specifications of an instance to be created </td>
  </tr>
  <tr>
    <td>Default Disk Size </td>
    <td>Size of a default disk of an instance to be created <br> The unit is GB; the size is to be confined depending on the instance flavors </td>
  </tr>
  <tr>
    <td>Key Pair</td>
    <td>Key to access an instance to be created </td>
  </tr>
  <tr>
    <td>Security Group </td>
    <td>Security rules of an instance to be created </td>
  </tr>
  <tr>
    <td>Network </td>
    <td>Network to connect with an instance to be created. <br> Can connect up to 4, and the order is important.<br> The first network serves as the default gateway address </td>
  </tr>
  <tr>
    <td rowspan="4">Additional Information </td>
    <td>Floating IP</td>
    <td>Whether to assign a floating IP to an instance which is to be created </td>
  </tr>
  <tr>
    <td>Name of Additional Disk</td>
    <td>Name of a disk to be additionally assigned to an instance which is to be created. </td>
  </tr>
  <tr>
    <td>Size of Additional Disk </td>
    <td>Size of a volume to be additionally assigned to an instance to be created <br> The unit is GB; allows a value between 10 and 1000GB only </td>
  </tr>
  <tr>
    <td>User Script </td>
    <td>Script to execute immediately after booting on an instance to be created <br> Allows up to 65535 characters in English </td>
  </tr>
</table>

> [Note]
> Additional disks can be used after mounted by a user script. Regarding the mounting procedure using user scripts, refer to [Block Storage Guide](/Storage/Block%20Storage/en/overview/#_2).

<br/>

> [Caution]
> An instance template, once created, cannot be modified.

## Scaling Groups
### View List of Scaling Groups
Shows currently-active scaling groups. On the View List screen, status of each scaling group can be found.

- Minimum/Maximum Instances: Minimum/Maximum number of instances that a scaling group can create.
- Current Instances: The number of instances currently possessed by a scaling group.
- Instance Template: The instance template currently used by a scaling group.
- Load Balancer: The load balancer currently used by a scaling group.
- Status: Refers to the status of a scaling group, by which success/failure of operation according to its policy can be confirmed. Below are the list of status.

| Status | Description |
|--|--|
| CREATE_IN_PROGRESS | Creation of a scaling group is under progress |
| CREATE_COMPLETE | Scaling group has been successfully created <br> Instances are created as many as running instances |
| CREATE_FAILED | Creating a scaling group has failed  <br> Contact Administrator|
| UPDATE_IN_PROGRESS | Change of a scaling group is under way |
| UPDATE_COMPLETE | Change in resources owned by a scaling group has been made due to modification or scale-out/in policy |
| UPDATE_FAILED | Operation for a scaling group has failed <br> Contact Administrator |

### Create Scaling Groups
Following items can be defined in a scaling group.

<table class="sg">
  <tr>
    <th>Classification</th>
    <th>Item</th>
    <th>Description</th>
  </tr>
  <tr>
    <td rowspan="5">Setting</td>
    <td>Name</td>
    <td>Name of a scaling group, within 20 characters in combination of capital and lower cases, '-', '.', and numbers </td>
  </tr>
  <tr>
    <td>Instance Template</td>
    <td>The instance template to be used by a scaling group </td>
  </tr>
  <tr>
    <td>Minimum Instance</td>
    <td>The number of instances to be maintained at the minimum while a scaling group is activated </td>
  </tr>
  <tr>
    <td>Maximum Instance</td>
    <td>The number of maximum instances allowed while a scaling group is activated </td>
  </tr>
  <tr>
    <td>Running Instance </td>
    <td>The number of instances created when a scaling group is activated for the first time </td>
  </tr>
  <tr>
    <td rowspan="4">Policy</td>
    <td>Condition</td>
    <td>Initiating conditions for scale-out/in policy <br> Specify performance indicators, reference values, and continued time</tr>
  <tr>
    <td>Conditional Operator</td>
    <td>Operators to be applied between initiating conditions <br> With `and `, policy is initiated when all conditions are satisfied<br> With`or`, policy is initiated when only one of the conditions is met</td>
  </tr>
  <tr>
    <td>Instance</td>
    <td>Number of instances to be created or deleted when a policy is initiated. </td>
  </tr>
  <tr>
    <td>Cooldown Period </td>
    <td>Time to wait until a policy is initiated again after previous initiation <br>If cooldown period has not passed, policy cannot be initiated even if conditions are met.  </td>
  </tr>
  <tr>
    <td>Load Balancer </td>
    <td>Selected Load Balancer </td>
    <td>The load balancer that a created instance is to be connected with.  </td>
  </tr>
</table>

### View Details and Modify
Select a scaling group from the list of scaling groups and check its details.

Click `Edit`on details screen, to modify attributes of the scaling group. By modifying the scaling group, instance templates in use or minimum/maximum/running instances can be changed.

### View Policy and Execute
Select a scaling group from the list of scaling groups and check its scaling policy.

Click `Edit` on policy details, to modify scaling policy. Or, click `Execute`in scale up/down policy to initiate the policy by force.  

### View and Create Scheduled Tasks
Select a scaling group from the list of scaling groups, and check scheduled tasks.

With task scheduling, the number of minimum/maximum/running instances of a scaling group at a specific time can be adjusted.  
Scheduled tasks can be set for one-time or periodic execution.

Items as follows are required to create a scheduled task:

| Item | Description |
|--|--|
| Name | Name of a scheduled task |
| Change Items | Attributes of a scaling group to be changed by a scheduled task <br>Select one out of the minimum/maximum/running instances |
| Value | New value of an attribute specified in change items <br>Modify the attribute selected from  `Change Items`on specified timing to this value |
| Repeat | Whether to repeat a scheduled task<br>Select either once or Cron expression |
| Cron Expression | Activated when Cron expression is selected for `Repeat` |
| Start Time | Activation time for a scheduled task <br>When `Repeat`is set once, task shall be executed on start time <br>When Cron expression is selected for`Repeat`, scheduled tasks are executed on a regular basis from the start time. |
| End Time | Closing time for a scheduled task <br>Activated when Cron expression is selected for `Repeat` |

> [Note]
> The Cron Expression is applied to show execution time/cycle of a scheduled task.
>
> The Cron expression is comprised of five items, each of which is divided by space characters and it means as follows:   
>
> | Item | Range Allowed | Available Special Characters |
> |--|--|--|
> | Minute | 0-59 | `*` `,` `-` |
> | Hour| 0-23 | `*` `,` `-` |
> | Day | 1-31 | `*` `,` `-` `?` `L` `W` |
> | Month | 1-12<br>JAN-DEC | `*` `,` `-` |
> | Day | 0-6<br>SUN-SAT | `*` `,` `-` `?` `L` `#` |
>
> For each item, use numbers or special characters to specify execution time. For correct grammar, refer to the following:
>
> | Special Characters | Meaning |
> |--|--|
> | * | All Hours |
> | - | Range |
> | , | Specific Time |
> | / | Increase Volume |
> | L | Last Time |
> | W | Closest Weekday: available only on **Day** items |
> | # | The Nth Day: available only on  **Day** items |
>
> The Cron expression is used like in the following examples.
>
> `0 10 * * *`: Executes at every 10:00 <br>
> `0/20 15 * * *`: Executes in every 20 minutes from 15:00, like 15:00, 15:20, and 15:40 <br>
> `0 12-15 * * *`: Executes at every 12:00, 13:00, 14:00, and 15:00 <br>
> `0 0 15 6,7,8 *`: Executes at 00:00 on the 15th of June, July, and August <br>
> `0 0 L * *`: Executes at 00:00 on the last day of every month <br>
> `0 9 25W * *`: Executes at 09:00 on a closest weekday from the 25th day of every month <br>
> `0 9 * * 3#2`: Executes at 09:00 on the second Thursday of every month <br>

> [Caution]
> Start time of a scheduled task can be specified only after three minutes from the current time. If a scaling group is now changing, scheduled task may be delayed.

### View List of Created Instances
Select a scaling group from the list and check the list of created instances.

> [Caution]
> Instances that a scaling group created are also exposed on the list of instance products. However, user cannot control them.  

### View Statistical Graphs
Select a scaling group from the list and check its statistical graph.

> [Note]
> The statistical graph shows the average usage of instances that belong to a scaling group during the last 1 day. You can also identify the point of time and cause of increase or decrease.

Statistical graphs provide statistics on system resources, as follows:

| System Resources | Provided Statistical Data |
| --- | --- |
| CPU Usage | Average CPU usage of all instances that belong to a scaling grop|
| Memory Usage | Average memory usage of all instances that belong to a scaling group|
| Disk Transfer Rate | Average data volume of read/write disk data per minute for all instances that belong to a scaling group|
| Network Transfer Rate | Average data volume of send/receive network per minute for all instances that belong to a scaling group |

Each graph can be extended up to 5-minute intervals, providing 1-minute statistical data at the minimum.

> [Note]
> Statistics require data of every instance of a scaling group, but it is impossible to collect all instance data at once. And that may cause some missing data during the first few minutes. Statistics are calculated based only on those instances which include collected data; hence, with further data added at later stage, values may change. Now, after all data is collected for every instance, you can find the same values at all times.
