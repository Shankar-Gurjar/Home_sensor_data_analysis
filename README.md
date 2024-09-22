# Home_sensor_data_analysis
# Outline

The home sensor system monitor and records data from a variety of sensors in a residential setting.
Physical Hardware
The system is composed of a number of sensors wired to and powered by a central ATMega 2560 microcontroller (sensor hub). The microcontroller is connected via 3 TTL serial lines to a host server (server).
 The sensor hub supplies 5 volts to all sensors. The server and sensor hub are on a battery backup power supply which can power the systems for 2-4 hours in the event of a power failure.
 
# Data format
Data is transmitted from the sensor hub to the server via TTL serial data. Each serial interface provides a connection for different services.

* Serial 0 - Interface to program the sensor hub with new firmware
* Serial 1 - System interface - all data sent to the server on this interface is logged. All data is sent via JSON.
* Serial 2 - User interface - this interface allows data to be sent / received manually over a console. Usually used for debugging. Data is sent as plain text.
* 
Data on serial 1 is sent via json strings. Incoming JSON strings are checked for validity and logged to a file. Each file grows to a maximum of 4 MB before a new file is created.
There are three message types possible.

name
notes
typical occurrence
print_reading
environment (temp, RH, etc.) Includes a summary of trigger_data.
once per minute. This time is referred to as 'sample period'
trigger_data
motion sensors and door open/closed
when a sensor changes states (0->1,1->0)
   system_start
  indicates sensor hub has started
 When the sensor hub is started or restarted (the server will sometimes force restart the hub if it does not receive data after a few minutes)
 print_reading fields
This document only covers the print_reading data msg type.
Format:
adc_{n}_{type}
● adc fields are direct reads of adc (analog to digital converter) pins. Sampled once a second
● {n} is the pin number
    64
water pressure (main line)
65
feb24 2022 became north sump line pressure. (previously was legacy co2 reading - not used)
66
feb24 2022 became south sump line pressure. (previously was legacy co2 reading - not used)
67
liquid moisture basement floor east
68
liquid moisture basement floor west
69
liquid moisture basement sump pump lid (will detect sump overflow condition)
      ● {type} is the type of reading
    avg
average (mean) in of that sample period
max
max value for that sample period
min
min value for that sample period
   baro1

 Barometric pressure measured outside. Measured in millibars. Note: missing decimal. Reading 100339 as 1003.39 millibars.
co2
co2 equivalent floor 1. Measured in ppm.
compdt
Date /time firmware for sensor hub last compiled.
elec_{n}_{type}
● elec fields are readings of electrical power usage at the circuit breaker. Readings are unitless but are relative to one another. Current is read via a split core transformer. Readings are taken 1 time a second but avg,max,sum are over the sample period (1 minute).
● {n} is the sensor number:
    0
Hot water heater
1
Upstairs
2
Main line phase 1
3
Main line phase 2 until 2022-01-16, then cabinet circuit power
4
Electric range / oven (240 volt)
5
Washer / dryer (240 volt)
6
Furnace blower motor
7
[not used]
        ● {type} is the type of reading:
    avg
average (mean) in of that sample period
max
max value for that sample period
sum
additive sum of all readings during the sample period
val
last instantaneous reading
    
 flow2
Flow of north output of sump pumps 1 and 2
flow3
Flow of south sump pump output for sump pump 3
k
Placeholder - always has value v
millis
The number of milliseconds the sensor hub has been running since last reboot.
pitime
Time and date msg was received by server.
read_duration
Time in milliseconds the sensor hub took to read temperature and RH sensors.
rh_{n}
Relative humidity in %. The location of these sensors:
    1
Center of basement
2
Basement wall
3
Outside
4
Floor 1
5
Garage
     temp_{n}_{ok}
● Temperature readings in degrees fahrenheit.
● Keys with 'ok' are boolean values to indicate whether the sensor reading had any errors.
0 = errors, 1 = no errors.
● {n} is the sensor number:
    1
Basement center
2
Floor 2
3
Floor 1 (on the floor)
   
     4
Garage
5
Attic crawl space
6
Furnace output air duct
7
Basement exterior wall
8
Outside air temperature
9
Floor 1 (ceiling) until 12-2019 then moved to first floor closet
      trpin_{n}_{type}
trpin data is from trigger pins. These are boolean values (1 = True, 0 = False) based on motion sensor and door open/close events. Since immediate action may be required for trigger data they are sent as soon as pin state changes. trigger_data data is sent immediately when the trigger event is received and stored as msg type = trigger_data. These fields serve to summarize the trigger data for the sample period. Trigger data is sampled at 10 hertz. (10 times a second)
Pin number information:
    38
motion back yard
40
motion front yard right
43
motion floor 2 - PIR sensor
44
motion front yard left
45
Garage door left - feb 2022 update to optical sensor, previously magnetic reed switch which had many outages / issues
47
motion floor 1 - PIR sensor
49
Garage door right - feb 2022 update to optical sensor - previously magnetic reed switch which had many outages/issues
           samples
number of samples in sample period
sampleson
number of samples where pin state is 1
state
state on last read
   
tvoc
Total volatile organic compounds in air. Downstairs. Same location as co2 sensor.
unixtime type
Identifies msg as print_reading, trigger data or system_start. Always present in all msg types.
uctime
millis field duplicate.
unixtime
unixtime msg received by server.
