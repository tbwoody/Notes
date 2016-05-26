
The Serial Peripheral Interface (SPI) bus is a synchronous serial communication interface specification used for short distance communication, primarily in embedded systems. The interface was developed by Motorola and has become a de facto standard. Typical applications include Secure Digital cards and liquid crystal displays.

SPI devices communicate in full duplex mode using a master-slave architecture with a single master. The master device originates the frame for reading and writing. Multiple slave devices are supported through selection with individual slave select (SS) lines.

Sometimes SPI is called a four-wire serial bus, contrasting with three-, two-, and one-wire serial buses. The SPI may be accurately described as a synchronous serial interface,[1] but it is different from the Synchronous Serial Interface (SSI) protocol, which is also a four-wire synchronous serial communication protocol, but employs differential signaling and provides only a single simplex communication channel.

#Advantages of SPI#

1. It’s faster than asynchronous serial

2. The receive hardware can be a simple shift register

3. It supports multiple slaves

#Disadvantages of SPI#

1. It requires more signal lines (wires) than other communications methods

2. The communications must be well-defined in advance (you can’t send random amounts of data whenever you want)

3. The master must control all communications (slaves can’t talk directly to each other)

4. It usually requires separate SS lines to each slave, which can be problematic if numerous slaves are needed.
