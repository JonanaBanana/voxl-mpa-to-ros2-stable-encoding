# VOXL MPA to ROS2 with stable encoding.
This fork changes some behavior in the Compressed Image publisher for h264 and h265 encoding, providing more stable streaming over wifi and between onboard ros2 nodes. Mainly, this is achieved by transmitting SPS+PPS frames with every image which only adds about 3kbps of extra overhead per compressed video stream. Also, QoS parameters have been tweaked to provide better reliability and stability of the video stream to reduce amount of corrupted/garbled frames. With a tuned decoder, corruption pretty much never happens. 
The advantage that we get, is that any h264 or h265 decoder in ROS2 can latch on to the stream at any time and still be guaranteed to receive the decoding parameters, which was not possible before. This allows for realtime (30 fps at least) video streaming and bagfile recording of all cameras at only 1% of the previous bandwidth and with only a slight loss in quality. VOXL boards are very resource limited, and pretty much any raw video cannot be piped into ros2 without losing 20-90% fps, but encoding the streams, piping them into ros2 and then decoding them in a composable node allows for full resolution stream subscription in real time, which is much better for downstream custom ros2 algorithms.



# ORIGINAL README

ROS2 Foxy Nodes that takes in mpa data and published it to ROS2

## Build Instructions if running directly on target (VOXL2)
1. Ensure you have ros2 foxy installed on target - if not please execute the `./install_foxy.sh` command which will take about 20 minutes to run
2. Once installed, proceed to `cd` into the `colcon_ws` directory and run a `colcon build`
3. Once done, proceed to source the install/setup.script: `source install/setup.bash`
4. Once this is done the user can now run the voxl-mpa-to-ros2 code base by running `ros2 run voxl_mpa_to_ros2 voxl_mpa_to_ros2`

## Build Instructions if running in qrb5165-emulator and pushing to voxl2 post build

1. Requires the qrb5165-emulator (found [here](https://gitlab.com/voxl-public/support/voxl-docker)) to run docker ARM image
    * (PC) ```cd [Path To]/voxl-mpa-to-ros2```
    * (PC) ```git submodule update --init --recursive```
    * (PC) ```voxl-docker -i qrb5165-emulator```
2. Build project binary:
    * (qrb5165-emulator) ```./install_build_deps.sh qrb5165 stable```
    * (qrb5165-emulator) ```./clean.sh```
    * (qrb5165-emulator) ```./build.sh qrb5165```
    * (qrb5165-emulator) ```./make_package.sh```


### Installation
Install mpa-to-ros2 by running (VOXL):

(VOXL2/RB5):

```apt install voxl-mpa-to-ros2```

### Start Installed MPA ROS2 Node

Run the following commands(on voxl2):

Source the ros2 foxy setup script:

```source /opt/ros/foxy/setup.bash```

You can then run the nodes with: 

```ros2 launch voxl_mpa_to_ros2 voxl_mpa_to_ros2.launch```

##### Supported Interfaces
The current supported mpa->ros2 translations are:  

-cameras from voxl-camera-server or any services that publish an overlay (other than encoded images - only raw)

-IMUs

-6DOF data from voxl-qvio-server

-Point Clouds from the TOF sensor or services providing point clouds such as voxl-dfs-server

### Expected Behavior
```
voxl:/$ ros2 launch voxl_mpa_to_ros2 voxl_mpa_to_ros2.launch

MPA to ROS app is now running

Found new interface: stereo
Found new interface: tof_conf
Found new interface: tof_depth
Found new interface: tof_ir
Found new interface: tof_noise
Found new interface: tracking
Found new interface: tof_pc
```
