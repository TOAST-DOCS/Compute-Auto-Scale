## Compute > Auto Scale > Overview

Auto Scale helps to add or remove instances by monitoring loads of an instance. With auto scale, stable and flexible user service can be provided as real-time responses to loads are available. 
The auto scale is composed of an instance template and a scaling group.

## Instance Template
An instance template is an attribute of an instance that is to be automatically created. Items that are required when creating an instance must be set by default.
In addition, floating IPs and more volumes may be added, and a user script, which is to be executed for the first operation of an instance created by instance template, can be added by configuration.

> [Note] The role of an instance template is to simply save setting.
> An instance cannot be created only by creating an instance template.

## Scaling Group
Scaling group defines conditions to additionally create/delete instances, as well as activities to perform when the conditions are met. On principle, a scaling group is comprised of settings for minimum/maximum/running instances and scale out/in policy.
### Minimum, Maximum, and Running Instances
Minimum, maximum, and running instances are the parameters that must be defined in a scaling group, and each can be described as follows:

- Minimum/Maximum Instances: The minimum/maximum value of the number of instances that a scaling group can create.
- Running Instances: The number of instances that are created and currently run by a scaling group. Change of this value results in change of the number of instances.

If the number of minimum, maximum, and running instances refers to 1, 10, and 2, respectively, two instances are created at first, depending on the running instances. Later, with scale-out/in policy, the running instances may be increased or decreased. Running instances cannot surpass a specified range beyond minimum/maximum instances.

### Policy
A policy refers to a definition of criteria for creating or removing instances, and is composed of more than one condition, as well as operations when such conditions are met.
Conditions for a policy include instance performance indicators, reference value, and continued time. Following are the instance performance indicators applied for a scaling group.

- CPU usage rate (%)
- Memory usage rate (%)
- Read/Write disk volume per minute (KB/m)
- Network transmission volume per minute (KB/m)

The performance indicators in the above are collected at every minute, and collected indicators are managed by each group. Auto scale products eventually check the average value of all instances within a scaling group.

> [Caution] Policy cannot take effect if, only one specific instance satisfies conditions.

A scaling group observes whether specified performance indicators exceed the reference value throughout a duration and determines whether to execute a policy. For instance, if the CPU usage rate is more than 90% with 5 minutes of duration, the policy can take effect when the CPU usage rate does not fall below 90% for five minutes.

With policy set as specified conditions are met, the scaling group executes operations according to the conditions. More specifically, an operation refers to adjusting the value of a running instance in a scaling group: an increased value of a running instance is from **Scale-out Policy**, while a decrease is from **Scale-in Policy**.

> [Note] With a scale-in policy, closing starts from the oldest instance. Implement a handler of signals that occur when an instance is closed ([SIGTERM/SIGKILL](https://www.freedesktop.org/software/systemd/man/systemd.service.html) for Linux, and [WM_QUERYENDSESSION](https://msdn.microsoft.com/en-us/library/windows/desktop/aa376890.aspx)/[WM_ENDSESSION](https://msdn.microsoft.com/en-us/library/windows/desktop/aa376889.aspx) for Windows) so as to close service without suspension.

To prevent unlimited execution of operations following conditions, the cooldown period is configured. During a cooldown period after the last time of operation, policy cannot take effect even with satisfying conditions.

Here is an example to explain what it means to have a cooldown period.

Let's assume that a scaling policy defines_"When the CPU usage rate remains at over 90% for 5 minutes, the running instances should increase by two"_. If the CPU usage rate of actual instances remain for over 90% for 6 minutes, two instances will be created after the first 5 minutes. Then, another two instances will be created in the next 1 minute, as the condition is met once again.

As such, without a cooldown period, instances may be abruptly increased or decreased. An appropriate use of a cooldown period is recommended for an efficient resource use.

> [Note] Cooldown can be specified each for scale-out and scale-in policy.
> In general, a cooldown period for a scale-out policy is set as short as possible to readily react to sudden load hikes, while that for a scale-in policy is set to the longest period allowed, in order to gradually exclude an instance from the service. Continued monitoring of a service load is required to set an appropriate policy, and prevent instances from being wasted.

<br>

> [Caution] To prevent execution of both scale-out/in policies, scaling groups should also require a cooldown period. The cooldown period of a scaling group should follow the smaller value of a scale-out/in policy.

### Load Balancer
Specifies a load balancer to connect after an instance is created. With a scale-out policy, created instances are connected to a designated load balancer. As newly-added instances naturally share loads by load balancer, they can be immediately put into services.

> [Note] The actual implementation timing of an instance for service is after response to load balancer's status check is normally provided, when booting is completed and user service operates properly.

## Usage Scenario
The scaling group executes automatic scale-out/in depending on policy settings. However, if required, user can specifically adjust running instances. Autoscale supports the following types of adjustment:

- Adjustment by Policy <br>
  The number of instances can be adjusted in accordance with the policy specified in a scaling group. It is the default operation of scaling group.

- Adjustment by User <br>
  User executes scale-out/in policy on a console. It operates even when scale-out/in policy execution conditions are not met.

- Adjustment by Reservation <br>
  The number of running instances can be adjusted at every specific timing, with performance indicators of an instance serving as criteria. Execution can be set to occur just once, or repetitively.
