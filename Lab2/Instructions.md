
# PROTIOT Course Lab 2: Advanced Network Simulation with Contiki-NG Cooja

## Introduction
Building upon our initial session, Lab 2 delves deeper into the Contiki-NG Cooja network simulator's capabilities, analyzing more complex scenarios. This lab introduces Wireshark as an additional investigative tool, enhancing our exploration of IoT network protocols such as IEEE 802.15.4, 6LowPAN, and optionally, RPL.

Please note that this activity can be completed individually or in pairs.

## Lab Activities

### Activity 1: Simple Client-Server Network
- **Task:** Create a simple network with a UDP client and UDP server.
- **Objective:** Identify the MAC layer protocol, the access method used by the motes, and understand the Radio Duty Cycle.
    1. Add a Cooja mote `udp-server.c` and a Cooja mote `udp-client.c` by compiling the Contiki process/firmware from `/home/networkedss/Desktop/contiki-ng/examples/rpl-udp/`
    2. Ensure both motes are within the same coverage zone.
    3. Start the simulation (e.g., 20x speed) and stop after a while. Using “Mote Output” and “Radio Messages” tools, identify the MAC layer protocol and access method. Discuss your findings in the report.
    4. Under “Tools -> Mote radio duty cycle,” observe and explain the features related to node functionality. How does this impact network performance?

- **Additional Activity:** Modify the `udp-server.c` and `udp-client.c` scripts to change the messages exchanged (e.g., modify the 'hello' message). After making the changes, simply re-build the simulation scenario and re-run the simulation. Observe the new messages exchanged in the `Mote Output` tab and include a screenshot of these updated messages in your report.

### Activity 2: Higher Scale 6LoWPAN Network
- **Task:** Create a complex network with 5 nodes and 1 border router to explore characteristics of 802.15.4, 6LoWPAN, and optionally RPL.
- **Objective:** Use Wireshark and Cooja to analyze the network's behavior and how it enables communication between simulated nodes and the external world. Also, in this tutorial while you will learn how to simulate a Routing Protocol for Low-Power and Lossy Networks (RPL) border router in Cooja, you will learn to use it to allow communications between emulated nodes inside Cooja and processes running in the “outside world”.
    1. Set up a new simulation with a `border-router.c` from `/home/networkedss/Desktop/contiki-ng/examples/rpl-border-router/` and five `udp-client.c` motes.
    2. Arrange the nodes so that 1, 2, 3 are in the same communication coverage, 3, 5, 6 are in the same communication coverage, and 2, 4, 5 are in the same communication range (please have a look at the picture below for your reference).
    3. Run the simulation with the support of the key Cooja tools analyzed earlier and observe the patterns of communication. Document your remarks in your report.

![image](https://github.com/robertmora/PROTIOT_2024/assets/8244995/218fef50-b222-4258-bf66-bad97a8ef712)

##### Connect the Border Router to the Outside World

- **Open the Serial Socket:** In Cooja, right-click on the RPL border router node (Node 1). Navigate to `Mote tools for Contiki 1` and select `Serial Socket (SERVER)`, then click `Start` to open the serial socket server.

- **Terminal Setup:** Open a new terminal window. Change the directory to the RPL border router example with `cd /home/networkedss/Desktop/contiki-ng/examples/rpl-border-router` and execute `make TARGET=zoul connect-router-cooja`. This initiates `tunslip6`, creating a virtual tunnel interface (commonly `tun0`) that connects your simulation to the "outside world" — your operating system, virtual machine, or Docker container.
   - **Troubleshooting:** If you encounter errors, ensure the folder `/home/networkedss/Desktop/contiki-ng/gcc-arm-none-eabi-9-2020-q2-update/bin` is added to your PATH environment variable.

- **Observing Communication Patterns:** After connecting the border router, re-run the simulation. Note any changes in the pattern of messages exchanged and document your observations. What changed, and why do you think these changes occurred?

- **Testing Connectivity with `ping6`:** With the simulation running, use `ping6` in a terminal to test connectivity to the border router and other nodes. Determine the IP of three nodes, ping them, and capture screenshots of your tests for inclusion in your report.

- **Accessing the Border Router's Webserver:** The border router hosts a local webserver, which lists all the connected nodes. With the simulation active, use a web browser to visit `http://[fd00::201:1:1:1]/`. Please note that this URL may change based on your environment, so adjust as necessary. Document accessing the border router's status page.

Remember, the success of these steps depends on the simulation running in Cooja. Pay close attention to any errors or unusual behavior, as these can provide valuable insights into the network's operation and the border router's role within it.

Additional information about this process can be found [here](https://docs.contiki-ng.org/en/develop/doc/tutorials/Cooja-simulating-a-border-router.html).

##### Recording Simulation Output for Wireshark Analysis

Now, you will record the simulation output in a `.pcap` file for analysis in Wireshark. Follow these steps to ensure accurate capture and analysis:

1. **Open the Radio Message Window:** Ensure the "Radio Messages" window is open in Cooja, set to analyze 6LoWPAN traffic for pcap tracing.

2. **Running the Simulation:** If necessary, reload the simulation to start fresh, then let it run for at least 200 seconds to gather adequate data for analysis.

3. **Locating and Opening the `.pcap` File:** After the simulation, open Wireshark and navigate to the file located at `/home/networkedss/Desktop/contiki-ng/tools/cooja/radiolog-XXX.pcap` (replace `XXX` with the specific code for your recent log). Note that there may be multiple and old logs stored, so select the most recent one corresponding to your simulation.

4. **Analyzing the Capture in Wireshark:** Proceed to analyze the captured traffic in Wireshark. While you may encounter some errors within Wireshark, focus on the relevant data for your analysis.

5. **Using Cooja for Additional Insights:** Though the primary analysis will occur in Wireshark, remember that Cooja provides valuable tools and information that can complement your findings.

In the following sections, you'll be guided through specific questions and analysis points related to the Wireshark capture. This approach ensures a comprehensive understanding of the netwo

### Wireshark Analysis

#### IEEE 802.15.4 Analysis
1. **Locating the UDP Packet:** Begin by identifying the first UDP packet within your Wireshark capture. Expand the Frame Control Field (FCF) to analyze its components.
2. **PAN ID Compression Field:** Explain the significance of the PAN ID compression field within the frame. Report the PAN ID as identified in the capture.
3. **Addresses:** Determine and document the destination and source addresses. Verify whether the PAN ID field is present and report your findings.

#### 6LoWPAN Analysis
Locate the first UDP packet again, this time focusing on the FCF (Frame Control Field).

- **6LoWPAN Analysis:** Examine a 6LoWPAN packet in Wireshark, highlighting its key components and their roles, as discussed in lectures.
- **Header Compression:** Determine whether header compression is applied to the packet. Discuss the implications of this compression.
- **Addressing Relationship:** Explore the relationship between IEEE 802.15.4 addresses and IPv6 addresses. How are they correlated or translated?
- **Header Reconstitution:** Attempt to reconstitute the non-compressed header. Detail the steps involved in this process, highlighting the conversion or translation mechanisms.

#### RPL Analysis (OPTIONAL Activity)
1. **Filter for ICMPv6:** Use the `icmpv6` filter in Wireshark to isolate packets relevant to RPL, the routing protocol under analysis.
2. **Analyzing Routing Dynamics:** Examine how communication coverage influences routing decisions within the network. Consider applying additional filters for IPv6 if necessary to isolate relevant traffic.
3. **Practical Application:** Conduct this analysis for both the border router and a single selected UDP client. Document any patterns or behaviors observed that illustrate the routing protocol's functionality and efficiency.

## Submission Guidelines

Compile a report summarizing your findings, insights, and reflections on the lab activities. Document as requested through the instructions. Attach the `.pcap` file stored during the Wireshark analysis, the `.csc` file of the network of 6 nodes you built, and any requested screenshots. Include any other material you deem necessary to support your conclusions.

### Submission Details
- **Deadline:** 19/April/2024, 23:59 CET.
- **Format:** PDF, to be submitted via an assignment submission system in Moodle, which I will open for this purpose. Attachments should include the `.pcap` file, `.csc` file of your network setup, requested screenshots, and any additional supportive materials.

### Seek Support
Don't hesitate to ask for help. Collaborate with peers and instructors to enhance your learning experience. Your engagement and inquiry are encouraged throughout this process.


