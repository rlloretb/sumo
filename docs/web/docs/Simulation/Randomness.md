---
title: Simulation/Randomness
permalink: /Simulation/Randomness/
---

Stochasticity is an important aspect of reproducing reality in a
simulation scenario. There are multiple ways of adding stochasticity to
a simulation. They are described below.

# Random number generation (RNG)

Sumo implements the [Mersenne
Twister](http://en.wikipedia.org/wiki/Mersenne_twister) algorithm for
generating random numbers. This random number generator (RNG) is
initialized with a seed value which defaults to the (arbitrary) value
**23423**. This setup makes all applications deterministic by default as
the sequence of random numbers is fixed for a given seed. The seed may
be changed using the option **--seed** {{DT_INT}}. When using the option **--random** the seed will be
chosen based on the current system time resulting in truly random
behavior.

The simulation uses multiple RNG instances to decouple different
simulation aspects

- randomness when loading vehicles (typeDistribution, speed
  deviation,...)
- probabilistic flows
- vehicle driving dynamics
- vehicle devices

The decoupling is done to ensure that loading vehicles does not affect
simulation behavior of earlier vehicles. All RNGs use the same seed.

# Vehicle type and route distributions

The easiest way of dynamically modifying vehicle behavior is to choose
the type or the route from a distribution on loading. Each type / route
has to be given explicitly with an assigned probability, see [route and
vehicle type
distributions](../Definition_of_Vehicles,_Vehicle_Types,_and_Routes.md#route_and_vehicle_type_distributions).
This gives on the one hand fine grained control of the results but on
the other hand makes it hard to model something like for instance a
uniform distribution of vehicle lengths between 5m and 7m.

# [Speed distribution](../Definition_of_Vehicles,_Vehicle_Types,_and_Routes.md#speed_distributions)

By default, vehicles in SUMO adhere to the maximum speed defined for the
lane they are driving on (if the maximumSpeed of their vehicle type
allows it). This behavior can be modified using the `<vType>`-attribute `speedFactor` which
makes vehicles drive with that factor of the current speed limit. The
attribute also allows the specification of the parameters of a normal
distribution with optional cutoffs. The random value is selected once
for each vehicle at the time of its creation. Using a speed deviation is
the recommended way for getting a heterogenous mix of vehicle speeds.

# Car-Following

The default [car-following model
*Krauss*](../Definition_of_Vehicles,_Vehicle_Types,_and_Routes.md#car-following_models)
supports stochastic driving behavior through the `vType`-attribute `sigma` (default
0.5). When this value is non-zero drivers will randomly vary their speed
based on the RNG described above. Other car-following models also use
this attribute.

# Departure times

The departure times of all vehicles may be varied randomly by using the
option **--random-depart-offset** {{DT_TIME}}. When this option is used each vehicle receives a random offset
to its departure time, equidistributed on \[0, {{DT_TIME}}\].

# Flows with a fixed number of vehicles

The [DUAROUTER](../DUAROUTER.md), [DFROUTER](../DFROUTER.md)
and [JTRROUTER](../JTRROUTER.md) applications support the option **--randomize-flows**.
When this option is used, each vehicle defined by a `<flow>`-element will be
given a random departure time which is equidistributed within the time
interval of the flow. (By default vehicles of a flow are spaced equally
in time).

# Flows with a random number of vehicles

Both [DUAROUTER](../DUAROUTER.md) and [SUMO](../SUMO.md)
support loading of `<flow>` elements with attribute `probability`. When this attribute is
used (instead of `vehsPerHour,number`, or `period`), a vehicle will be emitted randomly with the
given probability each second. This results in a [binomially
distributed](https://en.wikipedia.org/wiki/Binomial_distribution) flow
(which approximates a [Poisson
Distribution](https://en.wikipedia.org/wiki/Poisson_distribution) for
small probabilities). When modeling such a flow on a multi-lane road it
is recommended to define a `<flow>` for each individual lane.

# Departure and arrival attributes

The `<flow>`, `<trip>` and `<vehicle>` elements support the value "random" for their attributes `departLane`, `departPos`,
`departSpeed` and `arrivalPos`. The value will be chosen randomly on every insertion try (for the
departure attributes) or whenever there is a need to revalidate the
arrival value (i.e. after rerouting).

# Further sources of randomness

- The tool [randomTrips.py](../Tools/Trip.md#randomtripspy)
  allows generating traffic between random edges. It also supports
  randomizing arrival rates.
- [OD2TRIPS](../OD2TRIPS.md) adds randomness when drawing
  individual trips from an O/D-Matrix
- [DUAROUTER](../DUAROUTER.md) adds randomness when performing
  [Demand/Dynamic_User_Assignment](../Demand/Dynamic_User_Assignment.md)
- [Simulation routing can be
  randomized](../Demand/Automatic_Routing.md#randomness) to
  ensure usage of alternative routes.