# UART-Communication
In this project I Transmitted 8 bit ASCII data through serial communication using UART protocol from BASYS-3 FPGA board to USB port of PC which can be viewed through teraterm software and also interfaced 4 seven segment LED display in above FPGA board

# DEBOUNCING CIRCUIT
When you press a button, there is a chance that the button will not simply go from open to close. Since a button is a mechanical device, the contacts can bounce. For a short period after the button is pressed the value you read from an IO pin may toggle between 0 and 1 a few times before settling on the actual value.
To debounce a button, you just need to require that for a button to register as being pressed, it must look like it's being pressed for a set amount of time. In this case, being pressed is when the value of the button is 1. If you read enough 1's in a row it is safe to assume that the button has stopped bouncing and you can register one button press. If you fail to do this and you are using the button to increment a counter, then the counter may increase by more than 1 per button press since it will appear that each bounce was a separate press.
Firstly, we use two flip-flops (reg button_ff1 & reg button_ff2) to synchronize the button signal to the clock domain “clk”. When the push-button is pushed or released, we increment or decrement the counter “count”. The counter has to reach threshold before we decide that the push-button state has changed. We implement this by conditional statements:
Condition 1: Button flip-flop 2 (reg button_ff2) is high “1” and count “count” isn't at the count limit.
This makes sure that we won't count up at the limit. If this condition matches, we will count up (this means btn pressed)
Condition 2: Button flip-flop 2 (reg button_ff2) is low “0” and the count has at least 1 in it.
This makes sure no subtraction when count is 0. If this condition matches, we will count down (btn released)
Now, we need to decide when the button is pressed or not. To do this, we create a constant (parameter) “threshold” gauge how long button pressed to determine it is in pressed state. If the count is greater the threshold, the debounced signal “dbsign” will be high “1”. Otherwise, it will be low “0”.

# UART COMMUNICATION PROCESS
In UART communication, two UARTs communicate directly with each other. The transmitting UART converts parallel data from a controlling device like a CPU into serial form, transmits it in serial to the receiving UART, which then converts the serial data back into parallel data for the receiving device. Only two wires are needed to transmit data between two UARTs. Data flows from the Tx pin of the transmitting UART to the Rx pin of the receiving UART:
UARTs transmit data asynchronously, which means there is no clock signal to synchronize the output of bits from the transmitting UART to the sampling of bits by the receiving UART. Instead of a clock signal, the transmitting UART adds start and stop bits to the data packet being transferred. These bits define the beginning and end of the data packet so the receiving UART knows when to start reading the bits.
When the receiving UART detects a start bit, it starts to read the incoming bits at a specific frequency known as the baud rate. Baud rate is a measure of the speed of data transfer, expressed in bits per second (bps). Both UARTs must operate at about the same baud rate. The baud rate between the transmitting and receiving UARTs can only differ by about 10% before the timing of bits gets too far off.
The UART that is going to transmit data receives the data from a data bus. The data bus is used to send data to the UART by another device like a CPU, memory, or microcontroller. Data is transferred from the data bus to the transmitting UART in parallel form. After the transmitting UART gets the parallel data from the data bus, it adds a start bit, a parity bit, and a stop bit, creating the data packet. Next, the data packet is output serially, bit by bit at the Tx pin. The receiving UART reads the data packet bit by bit at its Rx pin. The receiving UART then converts the data back into parallel form and removes the start bit, parity bit, and stop bits.
Finally, the receiving UART transfers the data packet in parallel to the data bus on the receiving end.
START BIT: 
The UART data transmission line is normally held at a high voltage level when it’s not transmitting data. To start the transfer of data, the transmitting UART pulls the transmission line from high to low for one clock cycle. When the receiving UART detects the high to low voltage transition, it begins reading the bits in the data frame at the frequency of the baud rate.
DATA FRAME: 
The data frame contains the actual data being transferred. It can be 5 bits up to 8 bits long if a parity bit is used. If no parity bit is used, the data frame can be 9 bits long. In most cases, the data is sent with the least significant bit first.
PARITY: 
Parity describes the evenness or oddness of a number. The parity bit is a way for the receiving UART to tell if any data has changed during transmission. Bits can be changed by electromagnetic radiation, mismatched baud rates, or long distance data transfers. After the receiving UART reads the data frame, it counts the number of bits with a value of 1 and checks if the total is an even or odd number. If the parity bit is a 0 (even parity), the 1 bits in the data frame should total to an even number. If the parity bit is a 1 (odd parity), the 1 bits in the data frame should total to an odd number. When the parity bit matches the data, the UART knows that the transmission was free of errors. But if the parity bit is a 0, and the total is odd; or the parity bit is a 1, and the total is even, the UART knows that bits in the data frame have changed.
STOP BITS: 
To signal the end of the data packet, the sending UART drives the data transmission line from a low voltage to a high voltage for at least two bit durations.

# STEPS OF UART TRANSMISSION
1. The transmitting UART receives data in parallel from the data bus
2. The transmitting UART adds the start bit, parity bit, and the stop bit(s) to the data frame
3. The entire packet is sent serially from the transmitting UART to the receiving UART. The receiving UART samples the data line at the pre-configured baud rate
4.  The receiving UART discards the start bit, parity bit, and stop bit from the data frame
5.  The receiving UART converts the serial data back into parallel and transfers it to the data bus on the receiving end

# TERA TERM
Tera Term is one of the more popular Windows terminal programs. It's open source, and simple to use. 
Making a Connection
Go to "TeraTerm:File > New connection" within the program. Here, you can select which serial port you'd like to open up. Select the "Serial" radio button. Then select your port from the drop-down menu.
That'll open up the port. TeraTerm defaults to setting the baud rate at 9600 bps. 
The blank window with the blinking cursor is where data is both sent (by typing it in) and received.
Clear Buffer and Clear Screen:
If you want to clear your terminal screen you can use either the "Clear buffer" or "Clear screen" commands. Both are located under the Edit menu.
Clear screen will do just that, blank out the terminal screen, but any data received will still be preserved in the buffer.Clear buffer deletes the entire buffer of received data.
