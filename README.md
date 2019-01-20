# LoraWAN power consumption
Repository with details on LoraWAN device power consumption and other relevant subsystems that can aid the decision making process when designing battery powered LoraWAN devices with various functions.

Thus we define the following initial parameters:
 * Active time and power consumption - What is the time the device is turned on per cycle and what is the power consumption.
 * Sleep time and power consumption - What is the power consumption during sleep and its duration.
 * Battery system self-discharge and other sources of power loss - Determinign how the battery will behave over time and loose charge with temprature and time or other parameters.
 
## LoraWAN power consumption 

Recommended reading: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6068831/ and https://www.researchgate.net/publication/321698801_Comparison_of_LoRaWAN_Classes_and_their_Power_Consumption and https://www.theseus.fi/bitstream/handle/10024/146549/Peura_Ukko-Pekka.pdf

Two corner cases to consider, using SF7 and SF12 datarates with the following chipsets:
 * SX1272 (10 byte payload, and both RX windows, 14dBm)
   * SF12 = 0.39J
   * SF11 = 0.24J
   * SF10 = 0.13J
   * SF9 = 0.10J
   * SF8 = 0.075J
   * SF7 = 0.065J
 * SX126x (latest Semtech Lora transceiver)
    * expected to be 30% more efficeint
 
Thus, we can calculate total energy available in a battery and the number of messages that yields, assuming theya re sent continuously. Saft LS 14500 battery has 9.35Wh, which is 33660J. This would yield 86k messsages at SF12 or 517k messages at SF 7.
 
## GPS position power consumption
Assuming the use of Ublox M8 modules, this is a good starting point when using almanach to imprve time to fix: https://forum.u-blox.com/index.php/9238/ultra-low-power-consumption-for-infrequent-fixes and plenty of reading on hackaday.

The key concepts to keep in mind is that the frequency at which fixes are acquired will significantly effect the power consumption.  For example ephemeris data grows stale after four hours, thus having a fix every 2h enables this to be preserved over time. Having a fix every few days is another option but them the cold start process will take a matter of seconds. Based on the following: https://forum.u-blox.com/index.php/2932/time-to-fix-first

From this we can assume 1.95J per cold start and 0.75J per hot start fix as well as some RTC consumption for maintaining hot start capability. 

## System power consumption
Overall system power consumption will depend on the platform configuration, but it is relatively easy to remain in the 5uA range at 3.5V for example with Murata ABZ lora module with STM32L0 inside. This will give consumption per day of 0.067J per day, which equals one lora transmission at SF7.

   
