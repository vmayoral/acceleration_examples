# acceleration_examples

`acceleration_examples` is a meta-package that contain various ROS packages examples demonstrating the use of hardware acceleration. Each one of these examples aims to support all hardware acceleration technology solutions complying with REP-2008 (see [pending PR](https://github.com/ros-infrastructure/rep/pull/324)). By doing so, `acceleration_examples` aims to a) illustrate ROS package maintainers and ROS users how to build their own acceleration kernels and b) guarantee interoperability across technologies complying with REP-2008.

In turn, a CI system will be set to build the meta-package against all suported hardware.



[![](https://img.shields.io/badge/hardware_acceleration-KV260-ec1c24.svg)](https://www.xilinx.com/products/som/kria/kv260-vision-starter-kit.html)
[![](https://img.shields.io/badge/hardware_acceleration-ZCU102-ec1c24.svg)](https://www.xilinx.com/products/boards-and-kits/ek-u1-zcu102-g.html)



| Package | Kernel     | Description |   Acceleration factor | Technology | CPU baseline | Accelerated  |
|---------|------------|-------------|-----------------------|------------|--------------|--------------|
|[`publisher_xilinx`](publisher_xilinx) | | This package contains a minimalistic publisher using a member function for evaluation purposes which subclasses `rclcpp::Node` and sets up an `rclcpp::timer` to periodically call functions which publish messages. |   |  |  |
|[`simple_adder`](simple_adder) | [`adder1`](https://github.com/ros-acceleration/acceleration_examples/blob/main/simple_adder/src/adder1.cpp) [`adder2`](https://github.com/ros-acceleration/acceleration_examples/blob/main/simple_adder/src/adder2.cpp) | A trivial adder example. No interactions with the ROS 2 computational graph. Meant to demonstrate how HLS is integrated into build ROS 2 flows. |  N/A | KV260 | N/A |  |
|[`vadd_publisher`](vadd_publisher) | | A a trivial vector-add ROS 2 publisher. Adds two inputs to a vector in a loop and tries publishing it on the go at 10 Hz. |  | KV260 | 10 Hz<sup>[1](#myfootnote1)</sup> | |
|[`doublevadd_publisher`](doublevadd_publisher) | | A trivial double vector-add ROS 2 publisher. Adds two inputs to a vector in a loop and publishes on the go at 10 Hz. Running in hardware shows that it's not able to meet the rate target and stays at around 2 Hz. The objective of this package is to generate a computationally expensive baseline when executed in a general purpose embedded CPU. See [accelerated_doublevadd_publisher](accelerated_doublevadd_publisher) package for an optimized and accelerated version of the same package which offloads the vector operations into an FPGA. See [faster_doublevadd_publisher](faster_doublevadd_publisher) for an even more optimized version. |  | KV260 | 2 Hz<sup>[1](#myfootnote1)</sup> | |
| [`accelerated_vadd_publisher`](accelerated_doublevadd_publisher) | [`vadd`](https://github.com/ros-acceleration/acceleration_examples/blob/main/accelerated_doublevadd_publisher/src/vadd.cpp)  |  An offloaded version of the trivial [vadd_publisher](vadd_publisher) ROS 2 publisher which adds two inputs to a vector in a loop and publishes them at 10 Hz. Vector add operations are offloaded into to the FPGA and some minor dataflow optimizations are applied using HLS. The offloading operation into the FPGA allows the publisher to go from 2 Hz to 6 Hz but, still misses its target (10 Hz)  |      3x    |  KV260 |  2 Hz<sup>[1](#myfootnote1)</sup>  | 6 Hz<sup>[1](#myfootnote1)</sup> |
| [`faster_doublevadd_publisher`](faster_doublevadd_publisher) | [`vadd`](https://github.com/ros-acceleration/acceleration_examples/blob/main/faster_doublevadd_publisher/src/vadd.cpp)  |  An accelerated version of the trivial [vadd_publisher](https://github.com/ros-acceleration/acceleration_examples/tree/main/vadd_publisher) ROS 2 publisher which adds two inputs to a vector in a loop and publishes them at 10 Hz. Vector add operations are accelerated by exploiting parallelism with the FPGA. Also, similarly to [accelerated_vadd_publisher](accelerated_doublevadd_publisher), some basic dataflow optimizations are performed. The code parallelism and dataflow optimizations of the dataflow allows the publisher to go from 2 Hz to 10 Hz, meeting its target |      5x    |  KV260 |  2 Hz<sup>[1](#myfootnote1)</sup> | 10 Hz<sup>[1](#myfootnote1)</sup> |

- <a name="myfootnote1">1</a>: measured with `ros2 topic hz <topic-name>`
- <a name="myfootnote2">2</a>: very low rate.

### Quality Declaration

No quality is claimed according to [REP-2004](https://www.ros.org/reps/rep-2004.html). This meta-package is designed as a learning and reference resource and it should not be used in production environments.