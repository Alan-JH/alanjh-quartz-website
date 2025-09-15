---
title: TJ UAV Avalon X
date: 2023-09-11
---
#TJUAV #SUAS

The iteration of the electronics bay (nicknamed the "Ebay") on Avalon X is one of my favorite contributions to the UAV team my Senior year.

The Ebay is situated on the rear of the aircraft and houses all the electronics except for the camera and its gimbal, and the RC receivers which are mounted in the nose and tail of the aircraft for better signal. It includes a Pixhawk 2.4.8 for autopilot and flight control, a Raspberry Pi 4 for image taking and processing, a GPS/Compass unit, an RFD900X telemetry radio, and a Bullet AC 5GHz wifi antenna.

![[ebay1.jpg]]
![[ebay2.jpg]]![[ebay3.jpg]]

The old Ebay had a lot of unused space, and the full height tail cone print had very little ground clearance on the aircraft. It used two vertical plywood sheets with velcro attachment points. This was acceptable for Avalon Mk3.5 for the 2022 SUAS competition, which we built in a matter of months and just needed to be able to fly. We also needed to be able to swap out electronics as we hadn't finalized the configuration.

![[ebay-old.jpg]]
![[avalon-old.jpg]]

The initial idea with the new Ebay was to remove the tail cone entirely, saving weight, and cutting down on unused space by making the rear bulkhead a half height bulkhead and making the foam paneling angle upwards into it. Another main motivation was to solidify mounting of the electronics. I moved from individual servo extensions to bundling all the connections to the wing, tail, and nose in three sleeved cables with Molex 8 pin connectors on the ends, which cut down on our setup time by making the wiring process as easy as plugging in one connector instead of five or six servo extensions.

At the suggestion of a friend, I also designed two PCBs to replace the vertical plywood sheets, which included connections between the Pixhawk and RFD900X, voltage regulator boards, and mounting for several components including the Raspberry Pi. Notably, there is no velcro in the new design except for the battery mount, since the battery needs to be easily swapped. Since the two PCBs sandwich the wifi antenna, I made sure not to use a ground plane so as not to block RF from the antenna. The RFD900X antenna mounting was moved to the front bulkhead.

The final result is a lighter, more compact ebay that gives the plane significantly more ground clearance (with the help of taller landing gear as well) and much easier setup and teardown procedures, that also looks much sleeker.

![[avalonx-rear.jpg]]