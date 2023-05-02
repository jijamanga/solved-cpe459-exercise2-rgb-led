Download Link: https://assignmentchef.com/product/solved-cpe459-exercise2-rgb-led
<br>
This exercise has two parts.

<ol>

 <li>Build a circuit a push button and RGB LED on a breadboard.</li>

 <li>Write ladder logic to control the circuit

  <ol>

   <li>Step through an 8-color sequence with two control modes</li>

   <li>mode 1: push button changes color</li>

   <li>mode 2: colors change cyclically with a timer</li>

  </ol></li>

 <li>Connect to an HMI.

  <ol>

   <li>The HMI is provided. You will need to import it into SCADABr and start it running.</li>

  </ol></li>

</ol>




<h1>2         Build the circuit</h1>

<ol>

 <li>Insert the 3 color LED into the breadboard. The RGB LED has a common cathode pin. It is the longest pin on the device. Connect this pin to ground on the Arduino.</li>

 <li>Connect the R, G, and B pins to a 1K Ohm resistor and connect the other end of the resistor to pins 7, 8, and 12 respectively.</li>

 <li>Connect a button to pin 2 of the Arduino. Refer to exercise one for the appropriate button circuit.</li>

 <li>Connect power and ground rails of the breadboard to the 5V and GND pins of the Arduino.</li>

</ol>

<h1>3         Write Ladder Logic</h1>

<ol>

 <li>Using the OpenPLC Editor create a new project.</li>

 <li>Create the following variables.</li>

</ol>

<table>

 <tbody>

  <tr>

   <td width="105">Name</td>

   <td width="104">Class</td>

   <td width="104">Type</td>

   <td width="107">Location</td>

  </tr>

  <tr>

   <td width="105">count</td>

   <td width="104">Local</td>

   <td width="104">INT</td>

   <td width="107">%MW8</td>

  </tr>

  <tr>

   <td width="105">color0</td>

   <td width="104">Local</td>

   <td width="104">INT</td>

   <td width="107">%MW0</td>

  </tr>

  <tr>

   <td width="105">color1</td>

   <td width="104">Local</td>

   <td width="104">INT</td>

   <td width="107">%MW1</td>

  </tr>

  <tr>

   <td width="105">color2</td>

   <td width="104">Local</td>

   <td width="104">INT</td>

   <td width="107">%MW2</td>

  </tr>

  <tr>

   <td width="105">color3</td>

   <td width="104">Local</td>

   <td width="104">INT</td>

   <td width="107">%MW3</td>

  </tr>

  <tr>

   <td width="105">color4</td>

   <td width="104">Local</td>

   <td width="104">INT</td>

   <td width="107">%MW4</td>

  </tr>

  <tr>

   <td width="105">color5</td>

   <td width="104">Local</td>

   <td width="104">INT</td>

   <td width="107">%MW5</td>

  </tr>

  <tr>

   <td width="105">color6</td>

   <td width="104">Local</td>

   <td width="104">INT</td>

   <td width="107">%MW6</td>

  </tr>

  <tr>

   <td width="105">color7</td>

   <td width="104">Local</td>

   <td width="104">INT</td>

   <td width="107">%MW7</td>

  </tr>

  <tr>

   <td width="105">red_anode</td>

   <td width="104">Local</td>

   <td width="104">BOOL</td>

   <td width="107">%QX100.0</td>

  </tr>

  <tr>

   <td width="105">green_anode</td>

   <td width="104">Local</td>

   <td width="104">BOOL</td>

   <td width="107">%QX100.1</td>

  </tr>

  <tr>

   <td width="105">blue_anode</td>

   <td width="104">Local</td>

   <td width="104">BOOL</td>

   <td width="107">%QX100.2</td>

  </tr>

  <tr>

   <td width="105">button</td>

   <td width="104">Local</td>

   <td width="104">BOOL</td>

   <td width="107">%IX100.0</td>

  </tr>

  <tr>

   <td width="105">mode</td>

   <td width="104">Local</td>

   <td width="104">BOOL</td>

   <td width="107">%QX0.0</td>

  </tr>

  <tr>

   <td width="105">count_en</td>

   <td width="104">Local</td>

   <td width="104">BOOL</td>

   <td width="107"> </td>

  </tr>

  <tr>

   <td width="105">auto_count_en</td>

   <td width="104">Local</td>

   <td width="104">BOOl</td>

   <td width="107"> </td>

  </tr>

 </tbody>

</table>




The 8 color variables (<em>color0-color7</em>) are connected to registers.  These registers can be set by the HMI (SCADABR) via MODBUS.  This will allow the user to control what color is output for each count value.

<em>count</em> is also a register, but this variable will be controlled by ladder logic.  The %MW8 is a read/write type and in this case is used to allow the HMI to read the count and display it.  An attacker might choose to write to this variable!

<em>red_anode</em>, <em>green_anode</em>, and <em>blue_anode</em> are controlled by the ladder logic. These values will drive the Arduino pins connected to the RGB anodes on the RGB LED.

<em>button</em> is an input from the Arduino. When the button is pressed this value will be 1. When the button is released this value will be 0.

<em>mode</em> is an input from the SCADABR HMI.  %QX0.0 is a single bit read/write coil.  <em>mode </em>is not connected to the breadboard. Instead we will control it from the HMI.

<em>count_en</em> is an internal ladder logic variable that is used to advance a counter controlling the count variable. Since it is internal it is not connected to a location.

<em>auto_count_en</em> is another internal ladder logic variable that is used to advance a counter controlling the count variable. Since it is internal it is not connected to a location.

<ol start="3">

 <li>Use a CTU to count repeatedly from 0-7.</li>

</ol>

The CTU is a up-counter.




CU is the enable pin of the CTU block. When CU is high the counter will advance. The counter actually advances once per 0-&gt;1 transition of CU. So, if you leave CU set to high it will not continue to count.   Connect CU to the count_en variable. This is an internal variable we will configure in step 6. To connect a variable to a pin first instance a VAR (drag it on to the ladder edit console).

Configure the VAR properties to select count_en for Expression field and set the Class to Input.  Next connect the VAR instance to the CU pin of the CTU object.

CV is the current value.  We will connect this pin to the count variable we create above.   The CV pin must be connected to a variable of type INT (there are other CTU variants for other variable types).  The Q pin is high CV == PV.   We will use PV to set the maximum value we want our counter to achieve before resetting to 0.

Connect PV to a constant with value 8.  To create a constant instance a VAR (drag it on to the ladder edit console).

Set the Class to Input and the Expression to 8.

Connect the CTU Q pin to the R pin. This will assert the R pin when PV==CV and reset the CTU to 0. Note: The CTU

<ol start="4">

 <li>Use a MUX to select the color to display. The count output from the counter above is the select input (K) of the MUX. The 8 color vectors are connected to IN0-IN7 respectively.</li>

</ol>

Instance a MUX object.  The MUX object is found under the Library (right side of screen) in the “Selection” group of objects.

Notice the types for K, IN0-IN7, and OUT.  K is of type ANY_INT. We don’t set the type but connect variables of the correct type. Our CTU is producing an INT so for K we are good. The type for IN0-IN7 and OUT is listed as ANY. These must all be the same type to avoid errors.  We will use INT for all.

Set the number of inputs to be 9. This is 8 (the number of possible selections: IN0-IN7) plus 1 (the selector input, K).

Connect the K input to the count variable from the CTU object.

Connect IN0-IN7 to color0-color7 respectively.

The MUX acts like a switch (C/C++) statement.

<table width="100%">

 <tbody>

  <tr>

   <td>switch (K) {case 0: OUT = IN0; break;case 1: OUT = IN1; break;case 2: OUT = IN2; break;case 3: OUT = IN3; break;case 4: OUT = IN4; break;case 5: OUT = IN5; break;case 6: OUT = IN6; break;case 7: OUT = IN7; break;}</td>

  </tr>

 </tbody>

</table>

<ol start="5">

 <li>Use Divide-by-2 and Modulo-by-2 to select the bits of the color vector and connect these to red, green, and blue anodes.</li>

</ol>

The output from our MUX object is a 16-bit INT.  We want to select the least significant 3 bits of the MUX output from above and connect them to red_anode (bit 2), green_anode (bit 1), and blue_anode (bit 0).

There are 2 ways to do this (1) write a custom structured text function block or (2) use the DIV and MOD objects and compute the values we need.  We haven’t learned structured text, so, use option 2.

<ul>

 <li>blue_anode = MUX.OUT bit 0 = MUX.OUT % 2</li>

</ul>

<em>Note: The result of this is a type INT. Use an INT_TO_BOOL object to convert before connecting to the blue_anode coil.</em>

<ul>

 <li>green_anode = MUX.OUT bit 1 = (MUX.OUT / 2) % 2</li>

</ul>

<em>Note: The result of this is a type INT. Use an INT_TO_BOOL object to convert before connecting to the green_anode coil.</em>

<ul>

 <li>green_anode = MUX.OUT bit 2 = ((MUX.OUT / 2) / 2) % 2</li>

</ul>

<em>Note: The result of this is a type INT. Use an INT_TO_BOOL object to convert before connecting to the red_anode coil.</em>

The output of the bit selection logic should connect to coils connected to <em>blue_anode, green_anode¸ and red_anode respectively.</em>

The 3 coils should connect to a right power rail.

<ol start="6">

 <li>Create logic to drive the count_en input of the CTU object. This logic will need to determine if the circuit in auto or manual mode and act accordingly.</li>

</ol>

Use a SEL object to select between the button input and the auto_count_en signal based upon the value of the mode input.

Connect the mode signal to the G input of the SEL object.

Connect the button variable to the IN0 input of the SEL object.

Connect the auto_count_en variable to the IN1 input of the SEL object.

<ol start="7">

 <li>Create a clock waveform circuit driving auto_count_en with a 50/50 duty cycle and 1 Hz frequency.</li>

</ol>

In auto mode we would like the CTU object to automatically advance. This will cause the color to change each second.

The circuit above uses an on-delay timer (TON) and set and reset coils to create the desired clock waveform.

The IN input of a TON is an enable signal. When the TON is enabled (IN transitions from 0 -&gt; 1) the timer waits the amount of time specified on the PT input and then turns on the Q output. When the TON is disabled (IN transitions from 1 -&gt; 0) the Q output immediately turns off.  The ET output shows the elapsed time of the timer. ET is the time since the last 0 -&gt; 1 transition on IN.

The circuit above uses set and reset coils. These coils have memory and behave like latches.   When a S (set) coil is set to 1 its associated variable stays set until reset. When a R (reset) coil is triggered (a 0-&gt;1 transition on its input) its associated variable stays reset (value 0) until set. The figure below demonstrates this behavior. The top waveform is auto_count_en. The next two waveforms are TON0.Q and TON1.Q respectively. The short pulse on TON0.Q causes auto_count_en to go high and stay high. The short pulse on TON1.Q causes auto_count_en to go low and stay low.

<strong> </strong>

<h1>4         Connect the HMI</h1>

<ol>

 <li>We will not develop a new HMI for this exercise. Instead we will import an existing HMI.</li>

 <li>The Exercise 2 folder on Piazza includes an exercise2-hmi.json file. Download this file to your PC.</li>

 <li>Navigate to SCADABr.

  <ol>

   <li>Turn on the SCADABr VM.</li>

   <li>Login with your account.</li>

   <li>Start the Apache service by running the following command.<em>sudo /opt/tomcat6/apache-tomcat6-6.0.53/bin/bootstrap.jar</em></li>

   <li>Navigate to SCADABr in your host’s browser.<a href="http://localhost:9000/ScadaBR">http://localhost:9000/ScadaBR</a></li>

   <li>Login to SCADABr (user: admin, password: admin)</li>

  </ol></li>

 <li>Import the exercise2-hmi.json file.

  <ol>

   <li>Click Import/Export on the tool bar.</li>

   <li>Manually paste contents of the exercise2-hmi.json file into the data field.</li>

   <li>Press the Import button marked above.</li>

   <li>Press the Graphical views button.</li>

   <li>You need to update the IP address of the host (your PLC) in the data sources screen.

    <ol>

     <li>Go to data sources screen.</li>

     <li>On the left side look for Host IP. It is set to 10.0.2.5. Change this to your PLC’s IP address.To get your PLC’s IP address.

      <ol>

       <li>Log onto the PLC</li>

       <li>Type the command <strong><em>ip a</em></strong></li>

       <li>Record the IP address</li>

      </ol></li>

     <li>You may need to stop and restart the Data sources.

      <ol>

       <li>Go to the Data sources screen. Press .</li>

       <li>Click the status icon to disable. Press .</li>

      </ol></li>

    </ol></li>

  </ol></li>

</ol>

<ul>

 <li>Click the status icon to enable. Press .</li>

</ul>

<h1>5         Post Exercise Report</h1>

Answer the following questions.

<ol>

 <li>This exercise uses division operations to extract bits from a vector. What might happen if the denominator of the division operation became zero?  Hint: Exception handling.</li>

 <li>Assume causing the LED to flash RED is a signal to an operator that the system was in an alarm state, how might an attacker cause the RGB LED to flash RED by manipulating the color0-color7 variables and changing the mode.</li>

 <li>If you power off the SCADABr HMI VM while the PLC program is running does the PLC program and LED circuit continue to operate? Which if any functionality is lost?</li>

</ol>


