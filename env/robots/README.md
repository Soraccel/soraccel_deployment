# Robot environments

One reviewed `.env` file is committed per robot or homogeneous robot group.
It contains only non-secret deployment data:

- image names and immutable digests;
- ROS domain ID;
- the absolute configuration path on the robot.

Copy `uav-dev-01.env.example`, replace every placeholder and commit the result
under the robot's real identifier. Keep credentials and calibration files out
of this directory.

