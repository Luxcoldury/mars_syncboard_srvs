# ROS Services for SyncBoard

To send commands to the [mars_syncboard](https://github.com/Luxcoldury/mars_syncboard) either with `rosservice call` or using `roscpp` from a remote device, you will need this package of service definitions.

[mars_syncboard](https://github.com/Luxcoldury/mars_syncboard) itself also depends on this package for build and execution.

## How to setup

1. Clone this repo to `catkin_ws/src`

2. Simply goto `catkin_ws` then `catkin_make`


## Services advertised by [mars_syncboard](https://github.com/Luxcoldury/mars_syncboard)

### Service `toggle_trigger`

To start or stop triggering on Line 1,2,8~17.

Request and response of `toggle_trigger` in *Message Description Language* :

```
bool start_trigger
---
bool triggering
```

* `bool start_trigger` : `true` to start, `false` to stop

In normal use cases, lines are configured using `config_line` before calling `toggle_trigger` to start and finally stop triggering.

Line PPS and Line GPS is always on.

### Service `config_line`

To configure the signal output on Line 1,2,8~17.

Request and response of `config_line` in *Message Description Language* :

```
uint8 line_num
bool enabled
uint8 trigger_type
float32 freq
uint32 offset_us
uint8 duty_cycle_percent
---
bool succeeded
string msg
```

* `uint8 line_num` : The index of the line to be configured.
* `bool enabled` : If it is `true`, the line will start triggering after `toggle_trigger true`
* `uint8 trigger_type` : `0` for Rising edge, `1` for Falling edge, `2` for Either edge
* `float32 freq` : Frequency in Hz. Should be in (0,1000]. When the frequency is lesser than 1Hz, SyncBoard will try to trigger the line every $\lfloor 1/freq \rfloor$ seconds.
* `uint32 offset_us` : Offset/delay of the signal, in microsecond ($10^{-6}$ second)
* `uint8 duty_cycle_percent` : Duty cycle of the signal, in percentage. If `freq` is less than 1Hz, the width of the signal is `duty_cycle_percent`% of 1 second.

### Service `config_gps`

To configure the signal output on Line GPS.

Request and response of `config_gps` in *Message Description Language* :

```
uint32 baud
uint32 offset_us
bool inverted
---
bool succeeded
string msg
```

* `uint32 baud` : The baud rate of the GPS time signal. Should be (9600, 14400, 19200, 38400, 56000, 57600, 115200)
* `uint32 offset_us` :  Offset/delay of the signal, in microsecond ($10^{-6}$ second)
* `bool inverted` : If it is `true`, the level of the signal will be inverted.

### Service `toggle_button_led`

To control the LED on the external button.

Request and response of `toggle_button_led` in *Message Description Language* :

```
uint8 mode
---
uint8 mode
```

* `uint8 mode` : `0` for off, `1` for always on, `2` for blinking every 1 second

## Topics advertised by [mars_syncboard](https://github.com/Luxcoldury/mars_syncboard)

### Topic `line/x` (in which `x` is the line index)

The exact `time` which Line `x` triggered (or is going to trigger) at is published through this topic.

The messages are published every 1 second, not in real-time.

`x` resides in 1,2,8~17.

### Topic `button`

`String` Message `Pressed` / `Released` will be published when the button is pressed / released.