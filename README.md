# APSystems ECU proxy for cloudless operation

# Description
This short script should enable APSystems ECU users to work cloudless. 
Under normal circumstances, the ECU will stop functioning when apsystems.com domains are blocked. This Python 3 script causes the ECU to get simulated responses (as if the response came from the EMA cloud). APSystems have put a lot of work into keeping your data on the EMA cloud correctly up to date but did not give users the option not to do so without the ECU going down.

Every five minutes the ECU sends recent data to ecu.apsystemsema.com alternately via port 8995, 8996 and 8997. These ports are opened for a short amount of time to await response and is then being closed again. So EMA site swiftly responds with a timestamp of the last received data (this is usually the recent data - 5 minutes) to the ECU. The ECU now knows it has fully synchronized with the EMA site and also is aware of having an internet connection with EMA. When the inverters are offline, the ECU sends pull requests to EMA for missing data (if applicable). The EMA site responds with a timestamp of missing data after which the ECU will respond serving the missing data to EMA. 
This is of course not the complete functional description of how the ECU works, but this part covers the most important functions to keep the ECU running and handle exceptions on normal operations during the up- and downtime of inverters. This works the same for unattended OTA firmware updates, the ECU sends a pull request and if firmware is available leaves the port open to download and install firmware. 


# Why cloudless
- Firmware updates are being pushed to my ECU-R without any notice or release notes 
- I want to prevent others from being able to shut down my PV installation
- All parameters (even more than the EMA cloud holds) is present and is being read from the ECU using the HomeAssistant integration at https://github.com/ksheumaker/homeassistant-apsystems_ecur
- Ability to privately own the produced data

# Disadvantage
- No large repository of historical data and (trend-)analysis at EMA
- No ECU and Inverter (Over The Air) firmware updates

# Advantage
- You can use this method to optain the data directly from the ECU without having to scrape the EMA website and then push it to PVOutput for example (you will be missing some inverter parameters like signal strengths and temperatures though)
- No unsollicited OTA firmware updates for ECU or Inverters

# How to use
1. DNS rewrite \*.apsystemsema.com to a local host that is running this script (check if a ping to ecu.apsystemsema.com resolves to your host IP-address)
2. Block all (future) communication with APSystems and EMA system. Current list of used domains include:
* apsystemsema.cn
* ecuna.apsema.com
* ecueu.apsema.com
* ecu2.apsema.com
* \*.apsystemsema.cn
* \*.apsema.com
* \*.apsystems.com
* ...
3. Run the script for example with PyCharm or from within terminal type: python3 main.py at a host which is continuously running (Raspberry Pi or something)
4. You're done

# Firmware history
My current version: ECU_R_1.2.17Z30

# To Do
- Clean up code
- Bugfixes
- Error handling and shutting clean
- Integrate with Home Assistant
- More testing (compatibility with ECU-C)
- Optionally enable full proxy forwarding data to the EMA site as well
