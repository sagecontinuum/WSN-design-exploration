# Design Evaluation

In this first step, the focus is on understanding the commodity options for weatherproof enclosures to inform balancing several key design parameters:  manufacturability (including complexity, timeline, sourcing, etc), reliability, servicability, functionality (runs Sage workloads well for our locations), and cost.

The first three test units:

## BigBoy: K001

[Dashboard for Environmental Monitoring of K001](http://165.124.33.252:8820)

This unit is configured to be well cooled, and easy to fit all the needed components.

**Enclosure:**  20x16x12 Fiberglass NEMA4X

[Altelix NF201612](https://altelix.com/altelix-20x16x12-industrial-din-rail-fiberglass-enclosure-nema-4x-ip66/?sku=NF201612-DIN-)

**Cooling Technology:**  Stratus 44W/C compact heat exchanger, NEMA 4X

[Stratus TE30-030-24D-4X](https://www.automationdirect.com/adc/shopping/catalog/enclosure_thermal_management_-a-_lights/air-to-air_heat_exchangers/te30-030-24d-4x)

**Sensing:** Two SHT45 USB devices measure temperature and relative humidity inside the enclosure and outside in the environment.

[SHT45 USB Sensor](https://www.adafruit.com/product/6260)

**Condensation Management:**  None (yet)

**Construction:**  Hand cut holes in the enclosure door, mounted HX, and added cable glands for Ethernet, Power, a PTFE vent, and an external USB sensor.

**Location:**  Mounted at the Apiary, in front of the TCS building at Argonne National Laboratory.

**Operation:**  A software-defined thermostat uses the SHT45 to cycle the HX fan and start and stop cooling.


## Slim: K002 (under construction)

[Dashboard for Environmental Monitoring of K002]()

This unit is configured to be minimally cooled, and easy to fit all the needed components.  It uses the **smallest** HX that Stratus makes.

**Enclosure:**  20x16x8 Fiberglass NEMA4X (4" thinner than BigBoy).  The enclosure is also a little easier to handle, with locking enclosure mechanisms.

[Altelix NFC201608](https://altelix.com/altelix-20x16x8-industrial-din-rail-enclosure-fiberglass-nema-4x/?sku=NFC201608-DIN)

**Cooling Technology:**  Stratus 22W/C compact heat exchanger, NEMA 4X (~2.5" thinner)

[Stratus TE20-015-24D-4X](https://www.automationdirect.com/adc/shopping/catalog/enclosure_thermal_management_-a-_lights/air-to-air_heat_exchangers/te20-015-24d-4x)

**Sensing:** Two SHT45 USB devices measure temperature and relative humidity inside the enclosure and outside in the environment.

[SHT45 USB Sensor](https://www.adafruit.com/product/6260)

**Condensation Management:**  None (yet)

**Construction:**  Hand cut holes in the enclosure door, mounted HX, and added cable glands for Ethernet, Power, a PTFE vent, and an external USB sensor.

**Location:**  Mounted at the Apiary, in front of the TCS building at Argonne National Laboratory.

**Operation:**  A software-defined thermostat uses the SHT45 to cycle the HX fan and start and stop cooling.


## Zephyr: K003 (under construction)

[Dashboard for Environmental Monitoring of K003]()

This unit is configured to be ***vented***, in a compact box that is lighter and easier to mount and handle.

**Enclosure:**  20x16x8 Fiberglass NEMA4X (before vent holes), 3X with vents.  Enclosure is noticably smaller and easier to manage.  Locking enclosure mechanisms.

[Altelix NFC161208VFD2](https://altelix.com/altelix-16x12x8-vented-fiberglass-weatherproof-nema-enclosure-with-24-vdc-cooling-fan/?sku=NFC161208VFD2)

**Cooling Technology:**  Stratus 22W/C compact heat exchanger, NEMA 4X (~2.5" thinner)

[Stratus TE20-015-24D-4X](https://www.automationdirect.com/adc/shopping/catalog/enclosure_thermal_management_-a-_lights/air-to-air_heat_exchangers/te20-015-24d-4x)

**Sensing:** Two SHT45 USB devices measure temperature and relative humidity inside the enclosure and outside in the environment.

[SHT45 USB Sensor](https://www.adafruit.com/product/6260)

**Condensation Management:**  None (yet)

**Construction:**  Hand cut holes in the enclosure door, mounted HX, and added cable glands for Ethernet, Power, a PTFE vent, and an external USB sensor.

**Location:**  Mounted at the Apiary, in front of the TCS building at Argonne National Laboratory.


### Pete:  Still Editing....




## Key Design Questions

### What are the nominal power loads?

To properly size the cooling task, peak theoretical values are not entirely helpful. There is a tremendous difference between cooling 90 W and cooling 180 W. If 95% of all common AI tasks today only drive the node to ~100 W, we have a lot more room to explore software-defined power management, caching compute jobs until the cool of the evening, and simply throttling the power and slowing down results that are not time critical. SGT is a testbed, and dynamic power management is both an ideal use of real-time software resource management and a good avenue for future exploration.

However, first we need a clear understanding of Thor power loads across a range of scientific tasks, and of the static load that comes from cooling, power supply losses, fans, etc.

Our suite of applications should include the following:

- LLM that uses significant power (produces a stream of tokens)
- Classifier (BioCLIP, BirdNET, etc.) — often produces a vector
- Classic video analytics (OpenCV, etc.)
- Autonomous robot control (closed-loop, latency deadline) (GR00T, etc.)

Each of the examples should be scaled up with multiple instances to stress the GPU and see where the nominal power envelopes lie, including stressing the SSD.


### How do the cooling technologies compare?

There are important tradeoffs between the HX and vented solutions, including the rate of cooling, reliability and maintenance, construction complexity, environmental protection, and cost.

### How should we explore heating?

**TBD**... a plan needs to be developed

### How will we fit in the WSN Power and Networking Box?

**TBD**... a plan needs to be developed

### What are the key design questions issues for the Sage Branch (the RPi in the sky)

The UIC students put together a protoype of a RPi in the sky.  The next step is to assemble all the pieces in an enclosure and measure power and work through cooling.

The plan needs further development.


### Should we explore cold start after power interruption?

Under normal operating conditions, the use of either a heater or the cooling mechanisms would keep the interior of the enclosure above dew point and stable.

However, one problem that occurs after a power interruption, for example after a storm, is that all of the internal components are cold.

The accepted solution to these events is to detect cold start, run the internal vent and heaters until internal humidity and dew point have returned to normal.  This process essentially evaporates any condensation on circuit boards that would cause a short if power were immediately applied.

The simplest solution is a small arduino-sized "slow-start" controller that is completely sealed and protected from condensation.  It could determine when it was safe to turn components on.







