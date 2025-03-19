# PROTIOT Course Lab 3: LoRa and LoRaWAN

**Note:** While the instructions are designed with the virtual machine used in previous lab exercises in mind, students have the option to install the tools on their own machines. This can be done through the repositories of the tools used: ChirpStack (https://github.com/chirpstack/chirpstack) and LWN Simulator (https://github.com/UniCT-ARSLab/LWN-Simulator/). For students who have Docker installed, leveraging docker-compose makes running these tools straightforward. For detailed instructions on how to install and run the tools, please refer to the respective tool repositories. This approach might offer more flexibility and ease of use for those familiar with Docker environments.

## Introduction

In this lab, you will learn about LoRa and LoRaWAN, focusing on the deployment of a complete platform, including Gateways and a network server. We will utilize the ChirpStack suite (https://www.chirpstack.io/) for LoRa and LoRaWAN, alongside the LWN Simulator for Gateways and LoRa devices (https://github.com/UniCT-ARSLab/LWN-Simulator).

Overall, this lab aims to provide a hands-on experience with setting up and analyzing a LoRaWAN network, understanding the roles and configurations of devices, gateways, and network servers within the ecosystem.

## Setting Up the Environment

Before diving into the lab activities, some preliminary actions are required, especially for participants of the APPIOT course who have engaged in activities with the MQTT mosquitto broker or any service running on ports 1883 and 8000.

### Preliminary Steps

```bash
# Stop everything running on port 8000
# Requires root to kill processes
sudo lsof -ti:8000 | xargs -r sudo kill -9

# Stop everything running on port 1883
# Requires root to kill processes
sudo lsof -ti:1883 | xargs -r sudo kill -9

# Create a folder for Lab 3
mkdir -p /home/networkedss/Desktop/protiot_lab3
cd /home/networkedss/Desktop/protiot_lab3

# Clone the chirpstack-docker repository
git clone https://github.com/chirpstack/chirpstack-docker.git

# Download LWN Simulator
wget https://github.com/UniCT-ARSLab/LWN-Simulator/releases/download/v1.0.2/lwnsimulator_x86.zip

# Unzip the LWN Simulator
unzip lwnsimulator_x86.zip -d lwnsimulator_x86
```

Note: Running `docker-compose up` in the chirpstack-docker directory and `./lwnsimulator_x86` in the lwnsimulator_x86 directory will occupy the terminal. Consider running them in separate terminals or using tmux/screen.

```bash
# Start ChirpStack
cd chirpstack-docker
sudo docker-compose up
(sudo docker-compose down can be used to stop ChirpStack execution)

# Start LWN Simulator
cd ../lwnsimulator_x86
./lwnsimulator_x86
```

Once the setup is complete, you'll proceed with setting up ChirpStack and the LoRa simulator.

## Accessing the Tools

ChirpStack and the LWN Simulator are accessible through web interfaces. For ChirpStack, enter `127.0.0.1:8080` in a browser (credentials: admin/admin). For the LWN Simulator, use `127.0.0.1:8000`.

### Setting Up the LWN Simulator

1. Ensure the Gateway Bridge address is `127.0.0.1` and the port `1700`.
2. Add a new virtual Gateway in the Gateways tab with the following settings:
   - Name: [Give a name for the Gateway]
   - MAC address: [Generate a random value]
   - Keep Alive: 30 seconds
   - Location: [Select a geographical location, e.g., EURECOM's coordinates 43.6148 Latitude and 7.0740 Longitude]
   - State: Ensure "Active" is ticked.

### Integrating with ChirpStack

1. Navigate to the web interface of ChirpStack and add a Gateway Profile matching the MAC address created in the LWN Simulator.
2. Switch back to the LWN Simulator to add and configure a new device. Pay attention to settings such as the region (EU868), OTAA support, and ADR enabling.

This preliminary setup is just the beginning. Further instructions will guide you through more detailed configurations and lab activities.


## Switch again to the LWN Simulator

### Setting Up Devices

1. Navigate to the **Devices** tab and add a new device with the following configurations:
   - **General Settings:**
     - Give a name to the device.
     - Generate a random **DevEUI** (note its significance in the context of LoRaWAN), and select **EU868** as the region.
     - Ensure the "Active" button is ticked to enable the device.
   - **Activation:**
     - Opt for **OTAA** (Over The Air Activation), then generate a random **AppKey**, necessary for securing device-network communications.
     - Choose **Class A** for the device class (feel free to take a look at the differences between Class A, B, and C devices in the configuration setup).
   - **Frame's Settings:**
     - Set all the configuration options available.
   - **Features:**
     - Enable **ADR** (Adaptive Data Rate) and discuss its function and benefits.
     - Set the antenna range, keeping in mind the Gateway's location previously set.
   - **Location:**
     - Specify a location within range of the Gateway, ensuring the device is placed realistically in relation to its intended coverage area.
   - **Payload:**
     - Configure the payload settings, including **MType** as **ConfirmedDataUp**.

**Note:** Students are encouraged to either keep the default values for the settings or explore different options based on web research or literature. This exploration is an opportunity to deepen understanding of LoRaWAN configurations and their impacts on network behavior and device performance.

### Simulation and Observations

- Initiate the simulation via the web interface and monitor the outputs in the terminal where the simulator is running.
- Observe and differentiate between Gateway and device logs, noting any peculiarities or critical information displayed.
- Conclude the simulation to proceed with further configurations on the ChirpStack suite.

## ChirpStack Suite Configuration

1. **Device Profiles Creation:**
	- In the ChirpStack web interface, navigate to "Device Profiles" and add a new profile. Select LoRaWAN MAC 1.0.3 and the EU868 region.
	- Select the default ADR (Adaptive Data Rate) algorithm for LoRa. This setting optimizes data transmission rates and power consumption based on network conditions.
	- Choose a Regional Parameters Revision. For specifics on the EU868 region values and their implications, refer to the detailed explanation available at [LoRaWAN EU Regional Parameters](https://github.com/brocaar/lorawan/blob/d675789e16abb847a9b386179656de85c38e6134/band/band_eu863_870.go).
 - Set the Uplink Interval to align with the configuration specified in the simulator. This interval determines how frequently the device sends data to the network.

2. **Application and Device Setup:**
   - Under the tenant's "Applications" tab, create a new application and add a device to it, ensuring the **DeviceEUI** matches that in the LWN Simulator.
   - Highlight the process of enabling OTAA and assigning the **AppKey**, correlating these steps with the device's activation method discussed earlier.

### Re-Starting the Simulation and Analysis

- With the setup complete, start the simulation again from the LWN Simulator interface, and closely observe the interactions and data exchange patterns respect to the previous case. Comment about this in your report.
- Transition to the ChirpStack suite's dashboard to view active devices and gateways, including real-time data on network activity and device connectivity.
- Explore the "Gateways" and "Applications" sections for detailed insights into LoRaWAN frames and event logs, offering a comprehensive view of the network's operational dynamics.
	- In particular, within the "Applications" section, click on the name of the device you created, then on the DevEUI. On the Dashboard tab, you'll be able to see many Key Performance Indicators (KPIs) concerning the network created, providing valuable metrics on the performance and health of your network.

## Lab Activities

### Activity 1: Analyzing Log and LoRaWAN Frames

1. **Task:** Observe and analyze the values displayed in the log and LoRaWAN frames.
2. **Instructions:** Utilize the simulator to generate network activity. Pay close attention to the log and LoRaWAN frames, noting the values that are presented. Compare and comment these observed values to the theoretical knowledge and examples discussed in lectures.
3. **Goal:** To understand the practical implications of the theoretical concepts covered in lectures by relating observed values in logs and frames to expected behaviors and network characteristics.

### Activity 2: OTAA vs. ABP Activation

1. **Task:** Compare Over-the-Air Activation (OTAA) and Activation By Personalization (ABP) methods.
2. **Instructions:** Add a new device to the simulator using ABP activation and create the necessary device profile and device on the server side. Ensure this ABP device is the only active device before starting the simulation. Observe the differences in configuration for OTAA and ABP in both the simulator and the ChirpStack web interface. Additionally, explore the gateway's live LoRaWAN frames to identify differences between ABP and OTAA activations.
3. **Goal:** To recognize and understand the practical and operational differences between OTAA and ABP activation methods in LoRaWAN networks, highlighting the implications of each activation method on network behavior and security.

### Activity 3: Use Case Research for Device Configuration (BONUS, +3 points)

1. **Task:** Research specific use cases of LoRaWAN in various sectors (e.g., smart cities, agriculture) to determine optimal device configurations.
2. **Instructions:** Conduct a survey of web resources and academic literature to find a use case in a sector of interest (e.g., smart cities, agriculture, etc.). Analyze the recommendations for device settings, including which class of devices (A, B, C) to use, and how to configure device parameters for optimal performance. Then, compare these recommendations with the configuration options available in the simulator. It's important to note that not every setting or parameter you research will be directly applicable or available in the simulator. Identify which settings can be matched and note any limitations or discrepancies between the theoretical configurations and what the simulator allows you to configure.
3. **Goal:** To apply theoretical knowledge in practical scenarios, making informed decisions about device configuration and class selection based on specific use case requirements and constraints.

### Activity 4: Critical Discussion on Limitations

1. **Task:** Identify and briefly discuss potential limitations of the simulator based on the analyses conducted.
2. **Instructions:** Reflect on your experiences using the simulator and analyzing LoRaWAN network operations through the simulator. Identify any limitations or challenges you encountered, drawing on both the theoretical concepts covered in lectures and your personal observations or feelings about the simulator’s functionality and realism. Consider how these limitations might impact the applicability of your findings to real-world scenarios.
3. **Goal:** To foster a critical perspective on the tools and methods used in the lab, recognizing their limitations and considering improvements or alternative approaches for future work.

## Submission Guidelines

Compile a report (max 3 pages) summarizing findings, insights, and reflections. Include screenshots in the appendix and references.

### Submission Details

- **Deadline**: 30 April 2024, 23:59 CET
- **Format**: PDF
- **Submission Method**: Via the assignment submission system on Moodle. Attachments should include screenshots of the network setup and any additional supportive materials.
