# EKF2 Log Replay
This page shows you how you can tune the parameters of the EKF2 estimator by using the replay feature on a real flight log.


## Introduction
A developer has the possibility to enable onboard logging of the EKF2 estimator input sensor data.
This allows him to tune the parameters of the estimator offline by running a replay on the logged data trying out
different sets of parameters. The remainder of this page will explain which parameters have to be set in order to
benefit from this feature and how to correctly deploy it.

## Prerequisites
* set the parameter **EKF2_REC_RPL** to 1. This will tell the estimator to publish special replay messages for logging.
* set the parameter **SDLOG_PRIO_BOOST** to a value contained in the set {0, 1, 2, 3}. A value of 0 means that the onboard logging app has a default (low) scheduling priority.
A low scheduling priority can lead to a loss of logging messages. If you find that your log file contains 'gaps' due to skipped messages then you can increase
this parameter to a maximum value of 3.
* set the parameter **SDLOG_RATE** to a value of 500. This will enable logging at 500Hz rate which is necessary in order to capture all replay messages.

## Deployment
Once you have a real flight log then you can run a replay on it by using the following command in the root directory of your PX4 Firmware
```
make posix_sitl_replay replay logfile=absolute_path_to_log_file/my_log_file.px4log
```
where 'absolute_path_to_log_file/my_log_file.px4log' is a placeholder for the absolute path of the log file you want to run the replay on.
Once the command has executed check the terminal for the location and name of the replayed log file.

## Changing tuning parameters for a replay
You can set the estimator parameter values for the replay in the file **rcS_replay_iris** located under
/posix-configs/SITL/init.
For example add the following line at the end of the script
```
param set EKF2_GB_NOISE 0.001
```
This will change the process noise for the gyro bias.

Another way of how to modify parameters is to use QGroundControl. QGroundControl will automatically connect to the replay process and you can use
the Parameters tab to do the required parmeter adjustments. You can also profit from viewing all of the estimator data in the Analyze widget.