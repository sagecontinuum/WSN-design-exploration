# Wild Sage Node Design Study

Wild Sage Nodes (WSN) live outdoors and need a weatherproof enclosure.

The goal of this design study is to gain experience with commodity weatherproof computer enclosures, in order to inform and guide the selection of key parameters for a final design that balances manufacturability (complexity, timeline, sourcing), reliability, serviceability, functionality (runs Sage workloads well at our locations), and cost.

Ideally, the final design would support a wide range of deployment sites and configurations while still respecting these constraints.

**Methodology**: Build three to five real prototypes using several different commodity enclosures and technologies, and gather experience, data, and insights to guide the selection of key design requirements, parameters, and components.

**Core components**: The basic WSN building block is simple:

+ Power supply
+ NVIDIA Thor on an AVerMedia carrier
+ Power distribution unit (PDU)
+ Gateway router

**Environmental expectations**:

+ Operating temperature range: −20 °C to 40 °C (outdoor ambient)
+ Non-operating (storage) temperature range: −40 °C to 100 °C
+ Operating relative humidity: 20% – 95%
+ Solar gain: high plains of Colorado
+ Internal heat from electronics: ~170 W

## Enclosure Technology

Broadly, enclosures for electronics fall into several well-understood categories. A short overview of each approach, along with its advantages, disadvantages, and challenges, is given below.

### Sealed — Passive Cooling

Previous WSNs used sealed, passive cooling. That classic design was both very functional and resilient. The enclosure supported the Jetson NX GPU. The watertight box used a small PTFE port to equalize pressure, which over time also brings the interior humidity toward the outdoor average.

![](images/Jetson-NX-WSN.png)

Heat is dissipated through the skin — the total surface area of the enclosure. This method works well when the dissipated heat is small relative to the enclosure's surface area, as is often the case with a sealed PTZ camera or a small network switch in a waterproof enclosure. The heat from the electronics inside is easily rejected to the external surface. The previous WSN was at the limit of cooling for the box size; under significant solar gain, such as in Texas, the box became too warm.

Scaling up does not help as much as it appears. Surface area sets the cooling capacity, but for a geometrically similar box, volume grows as the 3/2 power of area: doubling the dissipation requires twice the surface area and **2.8 times the volume**. Equivalently, the surface-to-volume ratio falls as 1/L, so achievable watts per liter decline as total dissipation rises. Larger boxes also intercept proportionally more solar gain, which partially cancels the added cooling.

For ~170 W Thor nodes, an enclosure would need to be impractically large to dissipate heat through its surface alone.

**Advantages**: Very simple for low-power devices. Maintenance free.

**Disadvantages**: Impractical at the Thor design point.

### Sealed — Active Cooling

Commercial products exist for cooling enclosures with a heat exchanger (HX). An air-to-air HX is simple and is a commonly deployed technology. The sealed electronics compartment (often NEMA 4X rated) shields the electronics from harmful environments such as marine/coastal (Hawaii), agricultural fertilizer, and sulfuric vapors (volcanoes). The HX can be built to withstand the harsher external environment while cooling the sealed enclosure.

The diagram below shows the overall design of HX systems.

![](images/Air-to-Air-Heat-Exchanger-diagram.jpg)

An example of this style of commodity product is the [Stratus line of HX units](encstratusheatexchangers.pdf), which can be attached to existing enclosures. **BigBoy** (K001), an experimental system exploring the cooling capacity of a 44 W/°C compact HX, is pictured below.

![](images/BigBoy.jpg)

As an active cooling solution, the HX fan must cycle on and off based on the internal enclosure temperature. The most common commodity solution is a thermostat with a physical set point (dial). For BigBoy (V1.0), we are using the PDU to switch the HX fan under software control, driven by an internal enclosure temperature sensor.

Note that the HX and the condensation-control strategy are coupled. At 44 W/°C, an internal load of 38 W idle produces an internal-to-ambient temperature rise of well under 1 °C whenever the HX fan runs. Running the HX therefore erases the thermal margin that internal dissipation would otherwise provide against condensation, and the internal-loop faces of the HX core are clamped near ambient by construction, making them the coldest interior surfaces in the box. The HX core is consequently the first place condensation will form, and it requires a drain.

**Advantages**: Provides *very effective* active cooling to a sealed enclosure.

**Disadvantages**: Costly; requires custom modification of the selected enclosure to mount the HX. By itself, the HX provides only cooling, not active dew point/condensation management. Requires periodic maintenance and cleaning.

### Vented — Active Cooling

Vented boxes expel heated enclosure air through an exhaust vent and draw in cooler outside air through an intake vent.

These enclosures are a very common design across a wide range of electronics and telecommunications equipment. The enclosure below starts as a NEMA 4X box, with two vent holes cut for exhaust and intake. A rain shroud and air filter are intended to bring the vented enclosure to NEMA 3RX / IP24.

![](images/vented-box.webp)

As with the Sealed — Active Cooling design, the fans must be activated based on the internal enclosure temperature, using either a classic thermostat or software on the Sage node monitoring an internal sensor.

![](images/Dell-in-box.jpg)

Vented enclosures are used for a wide range of electronics, from routers and switches to servers such as the Dell pictured above.

**Advantages**: Very cost effective; easy to service; cooling can be very effective with powerful fans; a wide range of pre-built enclosures exists, with custom vents cut before the enclosure ships.

**Disadvantages**: Outside air is brought into contact with the electronics. In environments where the air is caustic or abrasive, lifespan is shortened. Periodic maintenance is required on the air filter.

# Managing Condensation

All three designs above require careful management of moisture and condensation. None of these enclosures are *hermetically sealed*, so water vapor will *eventually* pass in and out of any of them. When a box is warmed in the sun, the air inside expands and equalizes pressure through the seals; when the air cools, it contracts and draws air back in. Sealed boxes breathe.

A small PTFE (Teflon/Gore-Tex) vent gives that breathing a controlled path, so diurnal pressure differentials do not build. Without a PTFE vent, the cable glands and door seals flex on every cycle to relieve the pressure, and gasket compression set is cumulative and permanent — an unvented box does not stay sealed, it degrades into a box with an uncontrolled, unfiltered vent at the worst possible location.

Condensation occurs when any interior surface falls below the dew point of the air in contact with it. The gap closes from either side: *the surface gets colder*, or *the air gets wetter* — adding moisture raises the dew point until it meets the surface. The severe cases are those where both happen at once.

The two routes have different mitigations: colder surfaces are addressed with heat and insulation, wetter air with sealing, drainage, and vapor management. **A heater does nothing about moisture getting in.**

### Top Panel (surface cooling)

On a clear night the enclosure's top surface has a direct line of sight into deep space, which is effectively at −270 °C. Anything with a view of the sky radiates heat straight out into that void and gets very little back, so the top panel loses roughly 40–80 W/m² net and can fall several degrees below the surrounding air temperature — the same effect that puts dew on a car roof but not on its sides. For WSNs, a clear night is a significant condensation challenge: the inner face of the top panel will drop below the dew point of the air inside, and condensation will form there first. Clouds block this effect by radiating back, but we need to design for beautiful starry nights.

A few passive solutions work well and are common in commodity designs. [An insulating hat](https://www.nvent.com/en-us/hoffman/products/encessh3015) reduces solar gain during the day and provides some blocking of the sky view factor at night. More effective is internal insulation bonded to the interior surface of the enclosure, which keeps the exposed interior surface close to the internal air temperature rather than to the cold panel.

The insulation must be **closed-cell, with vapor-tight sealed seams and edges**. This is the same problem as insulating below-ambient chilled-water piping, where the trade uses closed-cell elastomeric with sealed joints for exactly this reason. Open-cell insulation, or closed-cell with unsealed edges, moves the condensation *behind* the insulation onto a panel that is now colder than before, where it cannot dry and cannot be inspected.

A common and inexpensive solution is shown below:

![](images/insulated.png)

Insulated enclosures can seem counterintuitive. Why insulate a box we are trying to cool? In a vented box, moving air is the heat path: at 40 CFM the airflow carries about 23 W/°C, while a box with 1 m² of surface area conducts only about 4–6 W/°C. Uninsulated walls conduct solar gain in during the day and dump heat to the sky at night. Insulation is inexpensive. It reduces passive cooling capacity slightly and buys two things: it keeps the sun from heating the box, and it keeps the top panel's inner face from becoming cold enough to condense.

### Heating (increasing the dew point margin)

If any interior surface — including the electronic components themselves, not just the enclosure skin — falls below the dew point of the air in contact with it, condensation will form on that surface.

A very common active mitigation is heating the interior. Heating does **not** change the dew point: the moisture content of the air is unchanged, so the dew point is unchanged. What heating does is raise surface and air temperatures above the dew point, opening the margin and lowering relative humidity. Saturated 10 °C air warmed 12 °C still has a 10 °C dew point, but its relative humidity falls to about 48%, and every surface in the box is now well clear of saturation.

A margin of 5 °C to 10 °C between the coldest interior surface and the dew point is a reasonable target. Ideally the interior is heated ***only*** when the margin needs opening, which requires measuring both interior humidity and the temperature of the coldest surface. A common and cheap (but power-hungry) alternative is a resistive heating element on a thermostat; note that snap-action enclosure thermostats carry roughly ±3 °C setpoint tolerance and a ~6 °C deadband, which is too coarse for dew point control.

![](images/heater.png)

The image above shows (left) a 50 W or 100 W heater mounted on a DIN rail, a common way to warm the inside of an enclosure, and (right) a heating plate that mounts behind the electronics mounting surface.

Two control requirements follow. First, the heater and the vent fan must be interlocked so they never run simultaneously: at 40 CFM the ventilation term is roughly 23 W/°C, which swamps a 100 W heater. Second, both intake and exhaust louvers need backdraft shutters, or stack effect will drain heater output continuously even with the fan off.

### Drainage

Any enclosure that can condense must be able to drain. A NEMA 4X or IP-rated enclosure rejects liquid water from outside, but vapor still enters through pressure differentials and gasket permeation; once it condenses, the same rating that kept liquid out now keeps it in. Every design in this study needs a drain at the low point, and the sealed/HX design needs one at the HX core specifically.

Next page: [Design Evaluation](./Design_Evaluation.md)
