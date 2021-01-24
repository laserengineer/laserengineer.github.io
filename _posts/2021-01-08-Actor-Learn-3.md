---
title: LabVIEW AF_3 AF Hello World!
categories:
  - Study
  - LabVIEW
tags:
  - LabVIEW
  - Actor
---
<span style="color:red">
<span style="font-size: 150%">
First rule of coding - Just write the code. </span>
</span>

[Source](https://www.youtube.com/watch?v=2k3ZDwJolbA&list=PLmF-6jvwRvVNFzBjzh4bQDjFbv6lShcth), [LabVIEW Sample Code](https://github.com/laserengineer/LabVIEW-Study.git)

## AF Hello World!

### First rule of coding - Just write the code

Show up Actor Front panel should only be used for debug.

The "Stop Message" was "Sent" directly after the "Send PopUp Message".

In launch all the steps are finished very very fast, but the PopUp fuction still runs asynchronouly until we click the stop.

However, "Stop" was only dequeued and acted upon OK buttion is clicked on the dialog.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/3/Takeaway.png"> </p>


Actor stop based on an error code, for most part they stop for error code "43".

Any error code will stop the actor.

<p align="center"> <img src="/assets/images/LabVIEW Actor Framework/3/First Actor.png"> </p>

The last part of code is completed first and then 5 seconds later "Hello World!"
