Firmware
========

vtol_fw_switch

This is for development of a VTOL fixed wing plane. The plane launches and
lands by standing on its tail with 4 propellers pointed upward. In
this MC mode, it will be controlled by the multicopter routines.

After taking off it pitches forward to build up speed and after passing stall speed,
it changes to 'plane' mode and uses the fixedwing(fw_) modules to control it.

So it is necessary to run both the MC and FW modules at the same time and switch to
the needed controller via an RC switch on the transmitter.

It is necessary to have new topics for the modules to communicate with each other since
they now must run simultaneously. These topics are created...
ORB_DECLARE(plane_vehicle_attitude_setpoint);
ORB_DECLARE(plane_vehicle_rates_setpoint);
ORB_DECLARE(plane_actuator_controls_0);
ORB_DECLARE(plane_vehicle_attitude);
ORB_DECLARE(mc_vehicle_attitude_setpoint);
ORB_DECLARE(mc_vehicle_rates_setpoint);
ORB_DECLARE(mc_actuator_controls_0);
ORB_DECLARE(mc_vehicle_attitude);

This isolates the plane modules in one group and the mc modules in another group.

The 'active' controller must then publish to the actual topics for mavlink and other modules. 

The MC and FW modules must be modified. For the given 4 topics, the module must subscribe to and publish to it's 
own topics(mc_xxx or plane_xxx). orb_subscribe's and orb_copie's must be modified with the appropriate macro like this
orb_subscribe(ORB_ID_PLANE(vehicle_attitude_setpoint));

orb_advertises are not needed and should be commented out. But orb_publishes must be modified like this...
plane_orb_publish(vehicle_attitude_setpoint_ID, &att_sp);

The plane_orb_publish function will publish to plane_vehicle_attitude_setpoint and will conditionally publish to 
vehicle_attitude_setpoint depending on the setting of the plane/mc transmitter switch.

There are axis rotations that are necessary so each module set(MC or PLANE) feels that the px4fmu is mounted 
in the proper for it. 


