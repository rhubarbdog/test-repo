<h1>Logging Data at high frequencies</h1>
</br>
This requires 2 pyboards one measuring the data and transmitting it to a
second. The second pyboard writes the data to SD card. I have tried using a
Raspberry Pi to record the data but could not acheive the high baudrate
required to save time in the measure and transmit cycle.
</br>
</br>
One pyboard requires files `data_logger.py` and `log_Tx.py`, the other
`data_logger.py` and `log_Rx.py`.  The pyboards communicate using UART so
require the Tx terminal of the "log_Tx" pyboard pin Y1 to be connected to the
Rx terminal of the "log_Rx" pyboard pin Y2. They also need to share a ground
connection, if they don't share a power source you need to connect GND on one
pyboard to GND on the other.
</br>
</br>
Using pyboard v1.1 and 1 sensor I have acheived data rates near 6kHz but 8% of
the results are at a slower frequency of 5.4kHz.
</br>
</br>
Press and release the USR switch to start the tranmitter `log_Tx.py`. The
transmitter calls the `.begin()` method and returns. Keep it running with a
loop controlled by pressing the USR switch to terminate. The `.end()` method
is optional it stops the tranmitter and restores garbage collection.
</br>
</br>
`log_Rx.py` starts by calling the `.begin()` method. Press the USR switch to
terminate. You must call the `.end(wait = 0)` method, it writes the remaining
buffers to file and closes it.
</br>
</br>
Start the logger `log_Rx.py` before `log_Tx.py` it wont record anything until
the transmitter sends it. How ever if you allow `log_Tx.py` to start first
results will be missed and in the worst case the data will be corrupt.
</br>
</br>
The transmitter `logger_Tx` has a `.timed()` method. It's use is to determine
the period in micro seconds of the  `.transmit()` method. Using a REPL prompt
or the `pyboard.py` command initiate you logger with a guessed frequency.
`log = data_logger.logger_Tx(1, ('X1', 'X2', 'X3'), 12, 400)` sets up a
transmitter on UART 1 with 3 ADC pins using timer 12 at 400Hz. Make multiple
calls to `log.timed()` to determine the usual period and outlier period. The
fisrt call takes about 250 micro seconds longer, this time isn't repeated in
the data set and can be ignored.
</br>
</br>
I have only tested this using UART 1 and UART 6 i beleive they run at the
highest baudrate.