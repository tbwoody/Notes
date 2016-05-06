#*CAN BUS*
CAN is short for Controller Area Network. It is a simple protocol used in manufacturing and in the aumobile industry. A vehicle is full of little embedded systems and controller units (ECUs). These all communicate using the CAN protocol.

CAN runs on two wires, CAN HIGH (CANH) and CAN LOW (CANL). CAN uses differential signaling. it means that when a signal comes in it raises the voltage on one line and drops the other line an equal amount. Differential signaling is used in environments that must be fault-tolerant to noise.

#*CAN PACKET*
There are two types of CAN packets, standard and extended. Standard is a simple format. There are three key elements to this packet:

##Arbitration ID
This is an identifier. It is not really a source or destination ID like in a network packet but more of a "subject ID". It is technically the ID of the device trying to communicate but one device can send multiple arbitration IDs. If two CAN packets are sent at the same time, the one with the lower arbitration ID wins.

##IDE
Identifier extension. This bit is always 0 for standard CAN.

##DLC
Data Length Code. This is the size of the data.

##Data
This is the data itself. The max size is 8 bytes. This is variable length but some tystems pad the end.

An Arbitration ID is a broadcast message and different controllers filter out only the ones they care about. All controllers on the same network see every packet. There is no indication which controller sent what. Extended CAN packets are very similar to normal CAN packets but chain multiple packets together to make a longer message. 


#*CAN OPEN*
CANOpen is a protocol put on top of CAN. The key information for CANOpen is that it breaks down the 11-bit identifier to a 4-bit function code and 7-bit node id. This combo is known as a Communication Object Identifier or COB-ID. A broadcast message on this system has 0x for both the function code and the node id.


