# Beacon Health Check Aggressor Script

Joe Vest (Twitter: @joevest)

## Description

This aggressor script uses a beacon's note field to indicate the health status of a beacon. This is done by ...

- Keeping track of each beacon's current sleep time
- Calculating health based on the "max expected checkin time"
- Updating the note field with an approriate icon every minute using the event `heartbeat_1m`

### Heath Checks and Icons

#### ☑ - symbol indicates beacon as active and working

Calculated as last checkin is less than max expected check-in time (sleep + (sleep * jitter))

i.e., A beacon with `sleep 300 20` would have a max expected check-in time of 300 + (300 * .01 * 20) or 360). 

#### 💀 - symbol indicates beacon has died

Calculated as last checkin time is greater then max expected check-in time * 2 (dead time)

i.e., A beacon with `sleep 300 20` would have a dead time of 720. 

#### ❎ - symbol indicates beacon has been killed by an operator.

If beacon['alive'] is false, then an opertor killed this beacon and the beacon acknowleded this command.

#### ⚠ - symbol indicates beacon as dying

Calculated as last checkin is greater than the max expected check-in time and less than the dead time. 

#### ⏸ - symbol indicates the beacon as paused. 

This is a linked beacon that has been disconnected but is still considered alive. These beacons depend on a parent for communication. Instead of marking these dead, they are marked paused. There is no great way to track disconnected beacons.

The following shows what these icons look like in the Cobalt Strike client

![](beacon_health_status.png)

## Usage

Load the beaconhealth.cna file through the script manager.

## What you should know about the script

- It uses the Note field. You are free to edit as you like. The symbol will be updated during a health check
- Linked beacons do not have a sleep. Sleeps are tracked by the parent of a linked beacons
- Sleep settings are not verified before tracking. Setting sleep on a dead beacon may cause the symbol to change
- The health check is designed to monitor longer running beacons.
  - Health is measured every minute
  - This may still be too aggressive
  - You can adjust the timing by changing the heartbeat event that kicks off a check.