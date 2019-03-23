<h1>Logging Data at high frequencies</h1>
</br>
This requires 2 pyboards one measuring the data and transmitting it to a
second. The second pyboard writes the data to SD card. I have tried using a
Raspberry Pi to record the data but could not acheive the high baudrate
required to save time in the measure and transmit cycle.
</br>
</br>
One pyboard requires files <code>data_logger.py</code> and <code>
log_Tx.py</code>, the other <code>data_logger.py</code> and <code>log_Rx.py
</code>.  The pyboards communicate using UART so require the Tx terminal of
the "log_Tx" pyboard (pin Y1) to be connected to the Rx terminal of the
"log_Rx" pyboard (pin Y2). They also need to share a ground connection, if
they don't share a power source you need to connect GND on one pyboard to GND
on the other.
</br>
</br>
Using pyboard v1.1 and 1 sensor I have acheived data rates near 6kHz but 8% of
the results are at a slower frequency of 5.4kHz. I'm still researching what is
taking those processor cycles and vital micro seconds.
</br>
</br>
Press and release the USR switch to start the tranmitter <code>log_Tx.py
</code>. The transmitter calls the <code>.begin()</code> method and returns.
Keep it running with a loop controlled by pressing the USR switch to
terminate. The <code>.end()</code> method is optional it stops the tranmitter
and restores garbage collection.
</br>
</br>
<code>log_Rx.py</code> starts by calling the <code>.begin()</code> method.
This method loops, press USR switch to terminate. You must call the </br>
<code>.end(wait = 0)</code> method, it writes the remaining buffers to file
and closes it.
</br>
</br>
Start the logger <code>log_Rx.py</code> before <code>log_Tx.py</code> it won't
record anything until the transmitter sends it. How ever if you allow <code>
log_Tx.py</code> to start first results will be missed and in the worst case
the data will be corrupt.
</br>
</br>
The transmitter <code>logger_Tx</code> has a <code>.timed()</code> method.
It's use is to determine the period in micro seconds of the  <code>
.transmit()</code> method. Using a REPL prompt or the <code>pyboard.py</code>
command initiate you logger with a guessed frequency. eg <code>log =
data_logger.logger_Tx(1, ('X1', 'X2', 'X3'), 12, 400)</code> sets up a
transmitter on UART 1 with 3 ADC pins using timer 12 at 400Hz. Make multiple
calls to <code>log.timed()</code> to determine the usual period and outlier
period. The fisrt call takes upto 250 micro seconds longer, this time isn't
repeated in the data set and can be ignored.
</br>
</br>
I have only tested this using UART 1 and UART 6 i beleive they run at the
highest baudrate.