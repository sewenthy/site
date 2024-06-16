---
layout: post
title: Urban Gardening with Automations
date: 2024-06-16 00:00:00
description: Initial work for the garden and some planning for future automations
tags: zigbee gardening urban home-assistant zha butterfly-pea bee
categories: gardening home-automations
---

After my graduation in 2023, one of the biggest part I miss about Yale-NUS is the green campus that everywhere I look there's always some small piece of gardens.  My suitemates are even more into plants so when I walk into their rooms, there are always some potted plants with even some long green leafy vines growing from the top of his bookcase.  I was never a fan of having dirt spills from the plants onto my floor and more importantly I never felt responsible enough to take care of them so I never had any while I was at school.  

Since I moved out I have been playing a lot with home automations using [home assistant](https://www.home-assistant.io/) using the [Zigbee Home Automation (ZHA)](https://www.home-assistant.io/integrations/zha/).  I realized that while I still might forget to water my plants, I can try to get my garden to water itself--and I can make cool graphs with all my sensors data.  

# Picking the plants
I have been trying to have a more vegetarian diet so I wanted to priorities my favorites such as [eggplants (brinjal)](https://gardeningsg.nparks.gov.sg/page-index/edible-plants/brinjal/), [tomatoes](https://gardeningsg.nparks.gov.sg/page-index/edible-plants/tomato/) and [winged beans](https://gardeningsg.nparks.gov.sg/page-index/edible-plants/winged-bean/).  After some quick googling, I found that the Singapore government encourages all these gardening efforts and even have amazing resources on how to get them growing.  

Though as I read through I realized I forgot about one important thing: bees.  Aside from winged beans most of the veggies I like need pollination and so needs some kind of pollinators.  As I live on 20th floors, I wasn't sure if they would come all the way up.  I needed something to check first so I decided to start small: [butterfly peas](https://gardeningsg.nparks.gov.sg/page-index/edible-plants/butterfly-pea/).  These are even [considered weeds](https://gardeningsg.nparks.gov.sg/page-index/edible-plants/butterfly-pea/) in some cases because they grow so easily so I'm definitely making my life easier and they have the added benefit of being a bees attractor.  

# Getting supplies and starting the seedlings
After consulting more internet resources, I found out I needed the following:  
* some loamy soils\*  
* pots of at least 20cm deep\*  
* trellis (for the vines to climb)  
* seedlings tray  
* seeds  

\*as adviced by [NParks](https://gardeningsg.nparks.gov.sg/page-index/edible-plants/butterfly-pea/).  

I got the seedling tray that have some holes in the bottom since I'm completely new at this so I might overwater the seedlings and I also the lid to protect it while it's sitting on my balcony.  

I got the soil and seed, from a local seller for a combined of ~SGD5 and the rest I got off of AliExpress which totals to ~SGD10.  

I only just started to sow the seeds yesterday and we will see the result soon!  
<div class="fake-img l-body">
  {% include figure.html path="assets/img/seedling_tray_starting.jpeg" class="img-fluid rounded z-depth-1" %}
</div>

# Minor automations
For now, the only sensor data I have is from 1 moisture sensor level using zigbee (~SGD20).  I had some issue with setting it up with home assistant as it is ZHA does not fully understand how to interpret the data coming from the model, `_TZE204_myd45weu`.  Following the guide from HA community [here](https://community.home-assistant.io/t/ts0601-tze200-ga1maeof-soil-moisture-sensor-and-zha/590913), I found out how to get ZHA to understand it with a custom "quirk" modified from this [original handler](https://github.com/zigpy/zha-device-handlers/blob/92c9fbc6d01a5d86f78d64183302b906aa7d8215/zhaquirks/tuya/ts0601_sensor.py).

```diff
-         MODELS_INFO: [("_TZE200_myd45weu", "TS0601")],
+         MODELS_INFO: [("_TZE204_myd45weu", "TS0601")],
```

After this fix and [enabling HA to use the quirks](https://community.home-assistant.io/t/how-to-setup-local-zha-quirks/341226/2?u=sewenthy), I managed to get the sensor moisture and temperature readings.  

I recorded the before and after watering moisture and temperature of the seedling tray, and I thought it was quite cool even though it's quite a simple graph currently.  

<div class="fake-img l-body">
  {% include figure.html path="assets/img/soil_moist_temp_graph.jpg" class="img-fluid rounded z-depth-1" %}
</div>

<div class="fake-img l-body">
  {% include figure.html path="assets/img/seedling_tray_moisture_sensor.jpeg" class="img-fluid rounded z-depth-1" %}
</div>

# Plans for more automations

## Bees Checker
One thing I want to get going is running a small computer vision model to check if the garden are receiving any pollinators--i.e. whether my butterfly pea plan works or not.  This will help me decide on what kind of plants I can do after.  Of course, I can just wait and chance upon some bees pollinating the garden but that wouldn't be as fun as struggling for hours to get a model to process many images taken by a SGD25 IP camera and see if it can find the bees.  

My current plan is to find some light model off of [huggingface](https://huggingface.co/), ideally something I can run locally with a small overworked GPU.  

## MySensors & Drip Irrigations
Since the moisture sensor I got was quite expensive for 1 sensor, and I would want to expand my garden afterwards having multiple pots, sensors (much more data points for cool graphs) and having some kind of drip irrigations working off of that data, I need a more cost effective method to get some readings.  I found some capacitive sensors on Aliexpress for <SGD1 each and it works with [Arduino](https://www.arduino.cc/). Great!  It's also a good thing I got the current zigbee sensor because it is quite trusted, well-tuned and I think the readings are accurate.  I can fine-tune the data of the Arduino output to be similar to the current sensor.  

Having all those data are well-and-good but I also need to have it connect to home assistant somehow (otherwise, no cool graphs).  This is when I chance upon [MySensors](https://www.mysensors.org/about/iot) which will also supposedly work with home assistant.  

I got some equipments from AliExpress again:
* zigbee soil moisture and temperature sensor  
* zigbee water pump  
* drip irrigation pipes  
* Nodemcu V2 ESP8266 (recommended by MySensors)  
* Arduino UNO R3 with starter kit  
* 5 pcs of capacitive soil moisture sensor  
* jumper wires and breadboards  

This all went for ~SGD90 (ah, the price I pay for graphs).  

So this is my plan for the next few weeks--getting Arduino to read moisture sensors and pass it to my home assistant server.  If all goes well then I'll try making home assistant run the smart pump based on the moisture sensors data.  This will be slightly tricky as my balcony sadly do not have any water outlet.  My plan is to use some kind of bucket (possibly with rain catchment into the bucket too), drill a hole at the bottom and use the pressure to run the pump.  

### OCaml for Arduino?
I love OCaml so the obvious next steps is to maybe have the code I write to integrate these sensors and home assistant in OCaml. Although it looks like there's not many widely used projects--I found a few that I made a fork of namely: [OMicroB](https://github.com/sewenthy/OMicroB) and [firmata](https://github.com/sewenthy/firmata).  I have not done much other than trying to get it to compile, but it's on the back of my mind.  
