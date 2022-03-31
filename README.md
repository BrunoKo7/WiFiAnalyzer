# WiFiAnalyzer
### implemented in Kali Linux
----
Brouno Kokthi

Evangelos Pipilikas

Myrto Chatzivoukoudi
## About the project
This is the project for the cource Wireless Network and Mobile Communication at AUEB for the year 2021-2022. The main concept of this project is to find the best channel according to the load of every channel.
## Tools and libraries
+ Airmon-ng
+ Iwconfig
+ Airodump-ng
+ Scapy python3
+ Crontab
## File compilation
   ~~~~
   >!< Please make sure you run the script in Kali Linux environment and your wifi adapter supports MONITOR MODE. >!<
   ~~~~
1. Open the command line and set the directory to the one that contains the project
2. Use the airmon-ng tool to change the wifi interface from Managed mode to Monitor mode. This can be accomplished by running the command `airmon-ng start wlan0`
3. Add the rules in the crontab file according to the time you want the script to be triggered. For example the rule `0 20 * * * cd /project && ./script.sh` triggers the script at 20:00.
4. Alternate option: instead of adding rules in crontab file, you can change the directory and run the command `./script.sh`.
## Algorithm basic steps
**Step 1**: Capture all the beacon frames from access points and save them in a .csv file. (File: [wifi_scanner.py](https://github.com/Apipilikas/WiFiAnalyzer/blob/main/wifi_scanner.py))

**Step 2**: Capture all the data frames from every network that we have already captured in Step 1. Save this information in a .csv file. (File: [wifi_data_sniffer.py](https://github.com/Apipilikas/WiFiAnalyzer/blob/main/wifi_data_sniffer.py))

**Step 3**: Process all the imformation given from step 1 and 2. (File: [evaluator.py](https://github.com/Apipilikas/WiFiAnalyzer/blob/main/evaluator.py))

**Step 4**: Create an algorithm consisting of two parts: channel evaluator and neighbor channel evaluator. (File: [evaluator.py](https://github.com/Apipilikas/WiFiAnalyzer/blob/main/evaluator.py))

**Step 5**: Keep history log for every channel. (File: [evaluator.py](https://github.com/Apipilikas/WiFiAnalyzer/blob/main/evaluator.py))
## Explaining the algorithm
### Channel evaluator
Keep in mind that we have the following information for each channel:
+ Percentage of packets comparing to the other channels (*packets*)
+ Percentage of networks comparing to the other channels (*networks*)
+ Percentage of stations comparing to the other channels (*stations*)

The formula we use to evaluate every channel is:

channel<sub>evaluator</sub> = 0.45 * packets + 0.35 * networks + 0.20 * stations
### Neighboring channel evaluator
Having every channel's evaluation, next step is check if there is any interference from the neighboring channels.

The information we need here for each channel is:
+ Channel evaluation (*channel<sub>i</sub>*)
+ Previous channel evaluation (*channel<sub>i-1</sub>*)
+ Next channel evaluation (*channel<sub>i+1</sub>*)

The formula we use to evaluate every channel is:

channel<sub>neighevaluator</sub> = 0.7 ∗ channel<sub>i</sub> + 0.3 ∗ (0.5 ∗ channel<sub>i−1</sub> + 0.5 ∗ channel<sub>i+1</sub>)
### History log
Automatization takes place three times per day, each of which we keep a history log.
|Time|History log file name|
|--|--|
|12:00|morning_measurements.txt|
|16:00|noon_measurements.txt|
|20:00|night_measurements.txt|
Every time that arrives the time to take the measurements:
1. Take the new data from the scanning (*evaluations<sub>new</sub>*)
2. Load the old data from the corresponding history log file (*evaluations<sub>old</sub>*)
3. Write the new history log back to the corresponding history log file following the formula below:

history<sub>evaluations</sub> = 0.8 * evaluations<sub>new</sub> + 0.2 * evaluations<sub>old</sub>

### Code demonstration
For a quick demonstration of the script and algorithm, check the [demonstration_ENG.pdf](https://github.com/Apipilikas/WiFiAnalyzer/blob/main/demonstration_ENG.pdf) file.
