# WDSC-HVAC-Control-Plugin
Vera - WDSC HVAC Control Plugin (a.k.a If door is opened for 5 minutes...turn off air)

This is a Vera 3 (UI5) plugin I wrote.  This code and accompanying documentation was originally published to the MIOS Trac SVN repo at:
http://code.mios.com/trac/mios_wdsc-hvac 

I have moved it here so that it can be with the other code/projects I have on Github.

Note:  Currently does not work on UI7.  

**Issues**:  Please check the existing issues before submitting a new one-- duplicate issues will be closed without comment.  This plugin does not work on UI7 at this time.   

**Pull requests**:  Please submit all PRs against the develop branch.  Include both a description and any instructions on what features you added/fixed in your PR.  

-------

_Current version: 1.2 - 03 October 2013_

 Code originally posted to: ​http://forum.micasaverde.com/index.php/topic,15982.msg121764.html#msg121764

 * Current information on new versions may be posted to the forum before it appears here. Check the forum post if you can't find the options listed here. 
    
-------

## Features:

-Will restore HVAC system to Off, AutoChangeOver, HeatOn, CoolOn, Normal Mode (turn off Energy Saving Mode), or do nothing when sensors are no longer in a tripped state.

-Will set your thermostat to one of the modes defined in OnMode, OffMode, HighTempMode and LowTempMode. Depending on how the plugin has been configured will dictate what mode your thermostat will be set to.

-OffMode is normally used when a Door or Window sensor is opened for the amount of time defined. If the plugin catches that a door or window sensor has been tripped, it will then set the thermostat to OffMode.

-HighTempMode and LowTempMode are used when the plugin detects that a temperature sensor has tripped. Depending on if it was a high or low temperature sensor, the thermostats will be set to the mode defined in HighTempMode or LowTempMode.

-OnMode is used the conditions defined above have cleared. Additionally, the plugin will check to see if the thermostats had their modes changed, and reset the mode to what is defined in OnMode. 

---

## Variables:

The following variables are exposed once the plugin has initialized:

**Sensors** = a list of Sensors that will be checked.

* _**Format:** Needs to be entered in the format of [sensor id#]="name"_

Example:

    42=UPHall_W, 31=GRFrnt_D, 41=GRLanai_D, 38=UPLBckBR_W, 39=UPRBckBR_W, 36=MastBR_D, 32=LFrtBR_D, 34=LBkBR_D, 35=LwHall_D, 40=LwHall_W
    
**Thermostats** = a list of thermostats that will be controlled by this plugin.

* _**Format:** Needs to be entered in the format of [thermostat id#]="name"_

Example:

    4=GRThermostat, 20=LHThermostat

**HighTempSensors** = a list of temperature sensors with a high temperature tripping point. The tripping point is defined as anything that is above the value defined.

* _**Format:** Needs to be entered in the format of [temperature sensor id#]=[tripping point]_

Example:

    45=80, 78=90

 **LowTempSensors** = a list of temperature sensors with a low temperature tripping point. The tripping point is defined as anything that is below the value defined.

* _**Format: Needs to be entered in the format of temperature sensor id#]=[tripping point]_

Example:

    45=32, 78=65

**Refresh** = number of seconds to wait between checking the sensors and controlling the thermostats. Default is set to 300 seconds (5 minutes). Min = 300, Max = 86400. Anything set/passed outside the min/max range will be ignored, and the default of 300 will be used. Setting this to 0 will disable the automatic polling.

Note: To "unset" the high or low temperature sensors, replace the values of the variables with "*undefined*" (without the quotes). This will "turn off" the checking of the high or low temperature sensors by the plugin. This can only be done for the temperature sensors, the door/window senors are mandatory and you currently cannot use this plugin to respond to temperature sensors only.

The following are the temperature modes that you can set. Note that OnMode and OffMode are mandatory.

**OnMode** = What mode should thermostats be restored to when sensors are closed (no longer tripped)

**OffMode** = What mode should thermostats be set to when sensors are open (tripped). Also can be though of as "Door/Window Sensor" mode, since this is the mode your thermostat will be set to when a Door/Window Sensor is tripped.

**HighTempMode** = What mode should thermostats be set to when a high temperature sensor has been tripped 

Valid modes are:
0 = Off (default for OffMode, 99 for rest)
1 = HeatOn
2 = CoolOn
3 = Energy Savings Mode
4 = Normal mode (turns off Energy Savings Mode)
5 = AutoChangeOver
6 = AuxHeatOn
7 = EconomyHeatOn
8 = EmergencyHeatOn
9 = AuxCoolOn
10 = EconomyCoolOn
11 = BuildingProtection
99 = Do nothing

**VerboseDebug** - Turns on the logging of additional messages for troubleshooting

Valid values are:
0 = Off (default)
1 = On

-- This will dump the contents of the String 2 Table routines as it converts the information in Sensors and Thermostats, useful for troubleshooting if you have entered something wrong in these variables.

All log messages start with "WDSC HVAC" for easy identification.

**StartupCheck** - Normally, the plugin will wait 300 seconds (5 minutes) when it starts before doing a check of the sensors. You can set this variable to run a check right after it has finished initializing.

Valid values are:
0 = Off (default)
1 = On

**PriorityMode** = Defines if Window/Door or Temperature events have priority for controlling your thermostats

0 = Set to OffMode (default)
1 = Temperature (any)
2 = High temperature
3 = Low temperature

When using PriorityMode, the plugin assumes after the check that if no temperature sensors are tripped, that the system should be placed into OffMode.

**SafeMode** = Defines if the more complex checking used when two or more sensors is tripped should be run even when a single sensor has tripped. When set, if PriorityMode is 0, your system will default to OffMode even if a temperature sensor has been tripped.

0 = Off
1 = On (default)

**EnergySavingsMode** = Defines how your thermostats should be set to EnergySavingsMode.

0 = Use SwitchPower service only (default)
1 = Use EnergySavingsMode operating mode only 

----

## Functions

**ForcePoll** = Allows for you to call the polling/checking option in your own scripts/scenes

**RestartPolling** = Allows you to change to interval for polling. Same rules apply as for setting Refresh variable. You can use this to start or stop polling in your own code/scenes.

Example:

    luup.call_action("urn:wdsc-hvac-org:serviceId:WDSC_HVAC1", "RestartPolling",{ delay= 0 }, 66)

Note: How to call the functions in this plugin have changed. Previously, they were incorrectly defined as urn:wdsc-hvac-org:device:WDSC_HVAC1. Anyone updating to this code will need to change their LUUP code to reflect this change.

This code would turn off polling for your WDSC device (assuming your plugin is device #66) 

----

## Warnings

    -- ******************************************************
    -- Window, Door, and Sensor HVAC Control (WDSC HVAC) plugin v1.2
    -- Code originally by mcvflorin and posted to
    -- http://forum.micasaverde.com/index.php/topic,4327.45.html
    -- Packaged in plugin form by SOlivas, with modifications 
    --
    -- History:
    -- 1.0 - Initial version released. 
    --       mcvflorin's code with modifications to turn thermostats to specified modes (On or Off) during check if
    --       door or window sensor is tripped.  Currently does not support internal motion sensors in this 
    --       version.  May be added in a future release.
    -- 1.1 - Implemented urn:upnp-org:serviceId:SwitchPower1 to make it easy to trigger scenes when OnMode and 
    --       OffMode fire.  Also added icon in .json file.
    -- 1.2 - Implemented temperature sensor support.  By defining temperature sensors, a high and low
    --       limit and an action to do if each it reached or exceeded, you can control your HVAC system
    --       based on climate conditions.  Direct Twilio Plugin support has been removed.
    --		 Also standardized the values used to control the settings across all modes.
    --
    --
    -- While I do not claim copyright for this plugin, I do feel that the following from the BSD license
    -- is appropriate, since this is controlling your thermostat connected to your HVAC equipment and there 
    -- is always possibility for things to not work in the way in which they were intended.
    --
    -- ** USE AT YOUR OWN RISK!!  IF YOUR HVAC EQUIPMENT MELTS DOWN, BREAKS, OR CATCHES FIRE/EXPLODES 
    -- ** YOU HAVE BEEN WARNED!  
    --
    --THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED 
    --WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A 
    --PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR
    --ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED
    --TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) 
    --HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING 
    --NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE 
    --POSSIBILITY OF SUCH DAMAGE.
    --
    -- Information:
    --
    -- ** Implements code from the following:
    -- 
    -- ** A modified version of the String 2 Lua code found at: 
    -- **    http://www.gymbyl.com/programming/s2t.html
    --
    -- Plugin settings:
    -- VerboseDebug will toggle the displaying of additional log messages for troubleshooting
    -- 0 = Disabled (default)
    -- 1 = Enabled
    --
    -- OnMode, OffMode, HTMode, LTMode can be one of the following:
    -- 0  = Off (default for OffMode, 99 for rest)
    -- 1  = HeatOn
    -- 2  = CoolOn
    -- 3  = Energy Savings Mode
    -- 4  = Normal mode (turns off Energy Savings Mode)
    -- 5  = AutoChangeOver
    -- 6  = AuxHeatOn
    -- 7  = EconomyHeatOn
    -- 8  = EmergencyHeatOn
    -- 9  = AuxCoolOn
    -- 10 = EconomyCoolOn
    -- 11 = BuildingProtection
    -- 99 = Do nothing
    --
    -- PriorityMode defines if Window/Door or Temperature events have priority for controlling your HVAC
    -- 0 = Window/Door (default)
    -- 1 = Temperature (any)
    -- 2 = High temperature 
    -- 3 = Low temperature
    --
    -- EnergySavingsMode has the following options:
    -- 0 = Use SwitchPower service only (default)
    -- 1 = Use EnergySavingsMode operating mode only
    --
    -- ******************************************************

----

## Pre-SVN Repository Bug Fixes

1. Made a fix to the string 2 table code and added in a verbose debug flag for troubleshooting. 

2. Fixed an error where device ID number was not being converted to a number, but passed as a string when using luup.call_action and luup.variable_get, causing them to fail. 

3. Fixed the format of what the strings in Sensors and Thermostats should be, before I just re-formatted what was in the old code without realizing the the routine I use to convert won't remove the square brackets around the numbers. 

4. There was a piece of code held over from my implementation in a scene, so that if the Thermostat was Off, it would never change to the state specified in OnMode. This has been fixed. 

5.  Added in checks to see what the thermostat is already set at, and if it is the same setting as the target mode for OnMode or OffMode, it skips setting the thermostat again to the same mode. Shouldn't make a difference, but it is a precaution if your thermostat does something each time it changes into a certain mode. 

----

Changes in Version 1.1:

* Implemented urn:schemas-upnp-org:service:SwitchPower:1 to make it easier to trigger scenes.
* By default, the readVariable messages are now turned off in the logs. Set VerboseDebug to turn back on. 

Changes in Version 1.2:

Support was added for external temperature sensors. You can define multiple sensors as high temp or low temp with a trip point. When the temperature drops below this tripping point, the mode defined in HighTempMode for high temperature or LowTempMode for low temperature will trigger. This will remain valid until the high or low temp mode clears, which will result in the plugin resetting to OnMode. Currently when OffMode or any of the high or low temp modes trip, it registers as the plugin turning off using the PowerSwitch service. A future version will implement way to distinguish what exactly caused the plugin to trip (i.e., what mode it is in).

There are numerous changes in this release. I've re-done some of the code used to control the thermostat, standardizing the values used in the OnMode, OffMode, HighTempMode, and LowTempMode settings.

Fixed a problem where the device was incorrectly defined. It was urn:wdsc-hvac-org:device:WDSC_HVAC1, which is not correct for a Service. It is now defined as urn:wdsc-hvac-org:serviceId:WDSC_HVAC1, so all calls to the plugin need to be updated to reflect this change.

Direct Twilio support was removed from this version as well. To use Twilio to send notifications, you will have to setup a trigger and then use LUUP to send the notification. 
