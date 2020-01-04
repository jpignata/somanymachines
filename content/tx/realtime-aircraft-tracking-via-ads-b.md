---
title: "Mapping Nearby Aircraft Using AWS and a Raspberry Pi"
date: 2019-05-25T09:00:00-04:00
type: post
dek: "Planes in the airspace over the US are broadcasting location
information over radio in plain text and they are just asking to be mapped."
---

![](/images/mapping-nearby-aircraft-using-aws-and-a-raspberry-pi/listen.jpg)
<figcaption>[Rare Historical Photos][0]</figcaption>

Around the turn of the century, increased air travel had started to strain the
air traffic control system causing frequent travel delays and threatening
adverse safety impact. In the United States, the FAA initiatied a program called
[NextGen][9] to modernize the entire air traffic control system for increased
safety, predictability, and efficiency.

A core component of the program is the creation of an ad-hoc radio network of
planes and ground stations called Automatic Dependent Surveillance - Broadcast
(ADS-B). ADS-B periodically broadcasts details acquired from an aircraft's
systems about its position, heading, altitude, speed, and identity over radio.
This allows other aircraft to monitor this data so pilots can visualize nearby
aircraft for self-separation, and allows more data to be fed to air traffic
controllers since ground stations are easier and cheaper to build than radar.

How cheap? Well, anyone can build an ADS-B ground station and receive these
broadcasts from nearby aircraft. [FlightAware][1] offers instructions and a
[software defined radio][2] that can be used with a Mode-S decoder such as
[dump1090][3]. You can get up and running for around $20. A couple of months ago
I hacked together a quick map of traffic near my home:

![](/images/mapping-nearby-aircraft-using-aws-and-a-raspberry-pi/app.png)

Using a Raspberry Pi with a [FlightAware Pro Stick][10], I'm repeating any ADS-B
broadcast I receive to a WebSockets server I have running on an EC2 Spot
instance[^4]. With a small amount of JavaScript hosted on Amazon S3 and the help
of [Leaflet][5], and [Mapbox][6], I'm able to plot where aicraft are in
real-time:

<iframe src="https://somanymachines.com/airplanes"></iframe>
<figcaption>[http://somanymachines.com/airplanes][7]</figcaption>

You can find the little bits of glue code powering it in my [aircraft_map
repo][8].

[0]: https://rarehistoricalphotos.com/aircraft-detection-radar-1917-1940/
[1]: https://flightaware.com/adsb/piaware/build
[2]: https://flightaware.com/adsb/prostick/
[3]: https://github.com/antirez/dump1090
[^4]: EC2 Spot instances are a mechanism to acquire spare EC2 capacity for a significantly discounted rate with the trade-off that the capacity could be reclaimed if demand characteristics change. For anything stateless or low priority, you should prefer Spot over on-demand instances. For around $1 a month, Spot gives me an instance where I can run random processes like this one. The box running this web service has been up in `us-east-2` for over three months without interruption.
[5]: https://leafletjs.com
[6]: https://www.mapbox.com
[7]: https://somanymachines.com/airplanes/
[8]: https://github.com/jpignata/aircraft_map
[9]: https://www.faa.gov/nextgen/
[10]: https://www.amazon.com/dp/B01D1ZAP3C
