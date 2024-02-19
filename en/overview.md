## Compute > Auto Scale > Overview

The Auto Scale service keeps monitoring instance loads to add or create instances as required. It also automatically creates a new instance when there is an error, such as disconnected network, so that the failed instance can be replaced.

## Scaling Group
Scaling group defines conditions to additionally create/delete instances, as well as activities to perform when the conditions are met. On principle, a scaling group is comprised of settings for minimum/maximum/running instances, scale out/in policy and auto healing policy.
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
- Read/Write disk volume per second (Bytes/s)
- Network transmission volume per second (Bytes/s)

The performance indicators in the above are collected at every minute, and collected indicators are managed by each group. Auto scale products eventually check the average value of all instances within a scaling group.

> [Caution] Policy cannot take effect if, only one specific instance satisfies conditions.

A scaling group observes whether specified performance indicators exceed the reference value throughout a duration and determines whether to execute a policy. For instance, if the CPU usage rate is more than 90% with 5 minutes of duration, the policy can take effect when the CPU usage rate does not fall below 90% for five minutes.

With policy set as specified conditions are met, the scaling group executes operations according to the conditions. More specifically, an operation refers to adjusting the value of a running instance in a scaling group: an increased value of a running instance is from **Scale-out Policy**, while a decrease is from **Scale-in Policy**.

> [Note] During a scale-in event, older instances are stopped first. Implement a signal handler for the signal that is raised when an instance is stopped ([SIGTERM/SIGKILL](https://www.freedesktop.org/software/systemd/man/systemd.service.html) on Linux, [WM_QUERYENDSESSION](https://msdn.microsoft.com/en-us/library/windows/desktop/aa376890.aspx)/[WM_ENDSESSION](https://msdn.microsoft.com/en-us/library/windows/desktop/aa376889.aspx) on Windows) in order to ensure that your services are stopped without interruption.

To prevent unlimited execution of operations following conditions, the cooldown period is configured. During a cooldown period after the last time of operation, policy cannot take effect even with satisfying conditions.

Here is an example to explain what it means to have a cooldown period.

Let's assume that a scaling policy defines_"When the CPU usage rate remains at over 90% for 5 minutes, the running instances should increase by two"_. If the CPU usage rate of actual instances remain for over 90% for 6 minutes, two instances will be created after the first 5 minutes. Then, another two instances will be created in the next 1 minute, as the condition is met once again.

As such, without a cooldown period, instances may be abruptly increased or decreased. An appropriate use of a cooldown period is recommended for an efficient resource use.

> [Note] Cooldown can be specified each for scale-out and scale-in policy.
> In general, a cooldown period for a scale-out policy is set as short as possible to readily react to sudden load hikes, while that for a scale-in policy is set to the longest period allowed, in order to gradually exclude an instance from the service. Continued monitoring of a service load is required to set an appropriate policy, and prevent instances from being wasted.

<br>

> [Caution] To prevent execution of both scale-out/in policies, scaling groups should also require a cooldown period. The cooldown period of a scaling group should follow the smaller value of a scale-out/in policy.

With auto healing policies, the failure of each individual instance is handled by deleting the instance and replacing it with a new one. If an instance's performance statistics are not collected during a continuous 3-minute period, it is determined as an error and auto healing will proceed. Auto healing occurs regardless of the cooldown period.

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
