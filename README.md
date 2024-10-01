# SNMP Prober Script

This project contains a Python script designed to probe an SNMP (Simple Network Management Protocol) agent and calculate the rate of change of various OIDs (Object Identifiers) between successive samples. The script supports SNMP counters of types COUNTER32, COUNTER64, GAUGE, and OCTET_STR, and addresses common issues like counter wraps and device reboots. The script also ensures the requested sampling frequency is maintained even if the agent does not respond in time.

## Features

- *Supports Multiple OIDs*: The script can probe multiple OIDs at once and handle OIDs of type COUNTER32, COUNTER64, GAUGE, and OCTET_STR.
- *Counter Wrap Handling*: Properly addresses the issue where counters wrap around when they reach their maximum value.
- *SNMP Agent Reboot Detection*: The script handles cases where the SNMP agent restarts using the sysUpTime OID.
- *Maintains Sampling Frequency*: The script ensures that the sampling frequency is adhered to even if the device experiences timeouts or slow responses.
- *Extended Support for OCTET_STR*: In the updated version, the script handles OCTET_STR types, printing them as they are received.

## Usage

The script is invoked as follows:

bash
./prober.py <Agent IP:port:community> <sample frequency> <samples> <OID1> <OID2> ... <OIDn>


- Agent IP:port:community: The SNMP agent's IP address, port, and community string.
- sample frequency: The frequency of sampling in Hz (between 0.1 and 10 Hz).
- samples: The number of successful samples to collect before terminating. A value of -1 means it will run indefinitely until interrupted.
- OIDn: The OIDs to probe (e.g., IF-MIB::ifInOctets.2 for interface 2 or 1.3.6.1.2.1.2.2.1.10.2).

### Example

bash
./prober.py 18.219.51.6:1611:public 1 5 1.3.6.1.2.1.2.2.1.10.2 1.3.6.1.2.1.1.5.0


This will probe the agent at IP 18.219.51.6, port 1611, with community string public, at 1Hz for 5 successful samples, probing the specified OIDs.

### Output Format

The script outputs the sample time (in UNIX time) and the rate of change for each OID between two successive samples:

bash
Sample time | ROID1 | ROID2 | ... | ROIDn


- Sample time: Timestamp of the last sample in UNIX time.
- ROID*: Rate of change for OID* between the last two samples.

### Extended Functionality

For OCTET_STR OIDs, the string is simply printed, while for COUNTER32, COUNTER64, and GAUGE, the rate of change is computed and printed as per the earlier functionality.

### Example Output

bash
1504083911 | 2124 | 72 (+50) | Hello | 281761
1504083912 | 2471 | 90 (+18) | ello H | 450782


In this example, one of the OIDs is an OCTET_STR, and the others are counters with their respective rate of changes displayed.

## Setup

### Prerequisites

- Python 3.x
- Install required SNMP libraries using:

bash
pip install easysnmp


### Running the Script

Make the script executable:

bash
chmod +x prober.py


Then run it using the command format provided above.

## Testing

You can test your solution against a simulated SNMP agent at 18.219.51.6:

- Port 1611: Standard configuration.
- Port 1612: Configuration with 64-bit counters.
- Port 1613: Test for extended types like OCTET_STR.

### Example

bash
./prober.py 18.219.51.6:1611:public 1 10 1.3.6.1.2.1.2.2.1.10.2


This will probe the simulated agent on port 1611, collecting 10 samples at 1Hz.

## Known Issues

- *Timeouts*: The script handles timeouts from the SNMP agent and continues to maintain the sampling frequency.
- *Agent Reset*: The script prints an "Agent has RESET" message when it detects that the SNMP agent has restarted.

## License

This project is licensed under the MIT License.
