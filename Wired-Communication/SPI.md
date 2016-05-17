#Advantages of SPI#

1. It’s faster than asynchronous serial

2. The receive hardware can be a simple shift register

3. It supports multiple slaves

#Disadvantages of SPI#

1. It requires more signal lines (wires) than other communications methods

2. The communications must be well-defined in advance (you can’t send random amounts of data whenever you want)

3. The master must control all communications (slaves can’t talk directly to each other)

4. It usually requires separate SS lines to each slave, which can be problematic if numerous slaves are needed.
