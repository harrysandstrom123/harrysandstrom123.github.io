---
title: "Twostage Ignition Rocket"
date: 2023-06-30
excerpt_separator: "<!--more-->"
categories:
  - blog
tags:
  - Projects
  - Undergrad
---

# Project Overview: Dual-Stage High-Altitude Rocketry

For our undergraduate capstone, my team and I designed and manufactured a high-power rocket tasked with carrying a fragile payload (an egg) to maximum altitude and returning it safely. While the project allowed for single-stage or hybrid designs, we chose to develop a two-stage rocket to challenge ourselves with increased system complexity and advanced avionics integration.

#### Ignition Strategy & Safety

We implemented an electronic ignition system for the second stage. This method was selected over mechanical alternatives because it offered superior reliability and an added safety margin; specifically, the flight computer could inhibit ignition if the rocket’s ascent angle deviated from a safe vertical trajectory.

#### Avionics & Circuit Design

As the Electronics and Ignition Lead, I headed the design and performance validation of the flight package. The core requirement was the precision-timed triggering of the second-stage motor that incorporated safety features, such as no possibility of pre-launch ignition, nor ignition if the rocket did not reach above a threshold altitude upon launch.

## How the System Functions:
To ensure successful ignition, I designed a Capacitor Discharge Circuit (CDC) controlled by a Teensy 4.0 flight computer:

* Energy Storage: The system first pulls power from the onboard battery to charge a high-capacity capacitor. This ensures a high-current burst is available to reliably fire the e-match, regardless of battery fluctuations.

* Trigger Logic: At a pre-programmed interval post-launch, the Teensy 4.0 pulls a digital output pin HIGH. This logic could only occur if a wire acting as a short between two pins on the teensy was removed, so it was used as the launch detection trigger in tandem with an altitude reading to ensure that the vehicle had both left the pad and reached a preset minimum altitude after launch before the ignition could be triggered.

* Switching: This signal activates a transistor (acting as a switch), allowing the stored energy in the capacitor to discharge through the e-match, successfully igniting the second-stage motor.

![Ignition Diagram Photo](/assets/images/ignition_diagram.png){: width="90%"}

![Ignition System Photo](/assets/images/ignition_system.png){: width="90%"}

## Assembled Rocket and Flight

Once all of our flight ignition systems had passed multiple checks and met all criteria we were ready to head to the desert and launch!

![Assembled Photo](/assets/images/ready_for_launch.png){: width="90%"}

![launchpad Photo](/assets/images/launchpad.png){: width="90%"}

Upon recovery of the rocket when we removed the nose cone we saw smoke, which likely came from the second stage ejection charge burning the “recovery wadding” (mostly tissue paper). However we removed the burning parts and everything seemed to be alright inside aside from a slight melt of the camera. We recovered all parts of the rocket including the first stage, which ejected correctly as the second stage ignited.

When comparing our flight trajectory to our simulation our actual apogee of 155 m compared to our prediction of 434m, a deviation of -65% from our prediction. This error can be accounted for by additional weight that was added to our rocket on launch day in the form of our wadding and last minute additions of glue and duct tape to put the launch lugs and re-secure the fins, as well as a higher drag coefficient that we were unable to record. Additionally, our second stage, upon ignition, spiraled, causing its trajectory to become more horizontal and dump a large portion of the vertical velocity that we would have added with the second stage. This can be seen with our peak velocity being 71 m/s as opposed to 106 m/s as projected. We can see in figures 24 and 25 that, while our actual trajectory doesn’t quite follow the simulation, the key timings of the second motor ignition and peak velocity line up fairly well, showing that with some more refinement of our rocket to eliminate the spiral and reduce weight could potentially achieve the predictions made by our simulation. While this number was quite a ways off, we actually had the most accurate altitude prediction out of any team, due to most simulations being very optimistic by the other teams compared to our much more conservative flight model.

![Rocket Apogee](/assets/images/rocket_apogee.png){: width="90%"}

Special thanks to my teammates who helped make this flight happen.

![TA Photo](/assets/images/ta_photo.jpg){: width="90%"}

# Code Appendix

Below is the actual code that ran on the rocket (and 3 others that did not have working second stage ignition software on launch day).

 ``` c
#include <Wire.h>
#include <LSM6.h>
#include <LPS.h>
#include "SparkFunLSM6DS3.h"
#include <SD.h>


LPS ps;       // Pressure sensor instance
LSM6DS3 imu;  // IMU instance


const int led = LED_BUILTIN;                                // Pin for the built-in LED
int ledIter = 0;                                            // Iteration counter for LED blinking
int iter = 0;                                               // Overall iteration counter
const int launch_detect = 4;                                // This is the pin that is used for the pull wire detection.
float launch_alt_arr[] = { 0, 0, 0, 0, 0, 0, 0, 0, 0, 0 };  //initialization of initial altitude. Will be an array that is averaged
float launch_alt_avg = 0;                                   // launch altitude initialization
const int trigger = 6;                                      // trigger pin for arduino
int launch_pad = 1;                                         // initialization of the launch state being false (1 = still connected)
bool state = true;                                          // this is used for launch detection
bool running = true;
const float staging_time = 3 * 1E3;  // how many seconds it takes to stage from launch convert from ms


const int startDelay = 15 * 1000;       // Start delay in milliseconds
const int chipSelect = BUILTIN_SDCARD;  // SD card chip select pin
const int desiredDelay = 50;           // Desired delay between data samples in milliseconds (20 Hz)
long int t0;                            // Initial time
long int t_launch;                      // time of launch
String filename;                        // Name of the data file


void setup() {
  pinMode(led, OUTPUT);                  // Set the LED pin as an output
  pinMode(launch_detect, INPUT_PULLUP);  //this si the switch pin
  pinMode(trigger, OUTPUT);




  Serial.begin(9600);  // Start serial communication
  Wire.begin();        // Initialize I2C communication


  if (!ps.init()) {  // Initialize the pressure sensor
    while (1) {
      Serial.println("Failed to autodetect pressure sensor!");
    }
  }


  ps.enableDefault();  // Enable default settings for the pressure sensor


  if (imu.begin() != 0) {  // Initialize the IMU
    while (1) {
      Serial.println("Failed to detect and initialize IMU!");
    }
  }


  imu.settings.accelRange = 16;   // Set the maximum G force readable by the accelerometer
  imu.settings.gyroRange = 2000;  // Set the maximum degrees per second readable by the gyroscope
  imu.begin();                    // Start the IMU


  if (!SD.begin(chipSelect)) {  // Initialize the SD card
    while (1) {
      Serial.println("Card failed, or not present");
    }
  } else {
    int i = 0;
    filename = "datalog_" + String(i) + ".txt";  // Generate a unique filename
    while (SD.exists(filename.c_str())) {        // Check if the file already exists
      i += 1;
      filename = "datalog_" + String(i) + ".txt";  // Generate a new filename
    }
    Serial.println("Card initialized, logging to: " + filename);
  }


  File dataFile = SD.open(filename.c_str(), FILE_WRITE);  // Open the data file


  if (dataFile) {
    dataFile.println("Time [ms], alt [m] p [mbar], T [c], accel x [g], accel y [g], accel z [g], gyro x [deg/sec], gyro y [deg/sec], gyro z [deg/sec], Launch Pad Status");  // Write the header line to the file
    dataFile.close();
    Serial.println("Time [ms], alt [m], p [mbar], T [c], accel x [g], accel y [g], accel z [g], gyro x [deg/sec], gyro y [deg/sec], gyro z [deg/sec], Launch Pad Status");  // Print the header line to the serial monitor
  } else {
    Serial.println("Error opening datalog.txt");  // If the file couldn't be opened, print an error message
    while (1) {}                                  // Halt the program
  }


  while (millis() - t0 < startDelay) {  // Wait for the start delay to pass
    digitalWrite(led, HIGH);
    delay(50);
    digitalWrite(led, LOW);
    delay(1000);
  }


  t0 = millis();  // Update the initial time
}


void loop() {
  long int t1 = millis();  // Record the current time


  if (ledIter < 5) {  // Only blink every 5 iterations
    ledIter += 1;
  } else {
    digitalWrite(led, HIGH);  // Turn on the LED
    ledIter = 0;
  }


  String dataString = "";  // Initialize an empty string to store the data


  launch_pad = digitalRead(launch_detect);  //measuring wheter launch pin has been pulled (1 is plugged, 0 is pulled)


  //Serial.print("Launch Pad "); // used for debugging
  //Serial.println(launch_pad);


  if ((iter < 50 && launch_pad == 1) || running == false) {  // 1 means that the pin is not in and at this point it decides it is too early and will not launch
    //Serial.println("Launchpad = " + launch_pad);
    running = false;  //permanently prevents the rest of the loop from running
    //Serial.println("No Pin. Halting Code"); // redundant
    dataString = "No Pin. Halting Code"; // if halted want to see this in the file so we know what happened.


  }  // make sure that it cannot launch if the pin isn't in at startup


  if (running) {


    float pressure = ps.readPressureMillibars();             // Read pressure from the sensor
    float altitude = ps.pressureToAltitudeMeters(pressure);  //Get the current altitude
    float temperature = ps.readTemperatureC();               // Read temperature from the sensor


    // checking that it is within the first few stages.
    if (iter < 10) {
      launch_alt_arr[iter] = altitude;
    } else if (iter >= 10 && launch_pad == 0)  //check that it hasn't launched (launch_pad == 0 not launched)
    {
      launch_alt_arr[(iter % 10)] = altitude;
      int sum = 0;
      for (unsigned index = 0; index < 10; index++) {
        sum += launch_alt_arr[index];
      }
      launch_alt_avg = sum / 10.000;     //get the average altitude reading
      Serial.print("Launch Alt Avg: ");  //print out the moving average of launch altitude
      Serial.println(launch_alt_avg);
    }


    if (launch_pad == 1 && state == true) {  //checks that the launch has become unplugged and that it hasn't already run this statement
      t_launch = t1;
      state = false;  //make sure that the count from launch only happens once.
    }


    if (t1 - t_launch >= staging_time && state == false && (altitude - launch_alt_avg) >= 50) {  // check that the time after launch is sufficient, there is a sufficient change in altitude, and that the launch state has been switched
      digitalWrite(trigger, HIGH);
    }


    dataString = String(t1 - t0) + ", " + String(altitude) + ", " + String(pressure) + ", " + String(temperature);  // Concatenate time, pressure, and temperature data


    float ax = imu.readFloatAccelX();  // Read accelerometer data along the X-axis
    float ay = imu.readFloatAccelY();  // Read accelerometer data along the Y-axis
    float az = imu.readFloatAccelZ();  // Read accelerometer data along the Z-axis
    float gx = imu.readFloatGyroX();   // Read gyroscope data along the X-axis
    float gy = imu.readFloatGyroY();   // Read gyroscope data along the Y-axis
    float gz = imu.readFloatGyroZ();   // Read gyroscope data along the Z-axis


    dataString = dataString + ", " + String(ax) + ", " + String(ay) + ", " + String(az);  // Concatenate accelerometer data
    dataString = dataString + ", " + String(gx) + ", " + String(gy) + ", " + String(gz) + ", " + String(launch_pad);  // Concatenate gyroscope data and add launch pad status (0 being plugged in)
  }
  File dataFile = SD.open(filename.c_str(), FILE_WRITE);  // Open the data file


  if (dataFile) {
    dataFile.println(dataString);  // Write the data string to the file
    dataFile.close();
    Serial.println(dataString);  // Print the data string to the serial monitor
  } else {
    Serial.println("Error opening datalog.txt");  // If the file couldn't be opened, print an error message
  }


  long int t2 = millis();  // Record the current time again
  int delta = (t2 - t1);   // Calculate the time taken for the loop


  if (desiredDelay > delta) {
    delay(desiredDelay - delta);  // Wait for the remaining time to achieve the desired delay between data samples
  } else {
    Serial.println("Violated Hz.");  // If the loop takes longer than the desired delay, print a message
  }
  if (ledIter == 0) {
    digitalWrite(led, LOW);  // Turn off the LED
  }
  iter++;
}

 ```
