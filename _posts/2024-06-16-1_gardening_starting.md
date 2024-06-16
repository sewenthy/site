---
layout: post
title: Urban Gardening with Automation
date: 2024-06-16 00:00:00
description: Initial work for the garden and some planning for future automation
tags: zigbee gardening urban home-assistant zha butterfly-pea bee
categories: gardening home-automation
---

After my graduation in 2023, one of the biggest part I miss about Yale-NUS is the green campus that everywhere I look there's always some small piece of gardens.  My suitemates are even more into plants so when I walk into their rooms, there are always some potted plants with even some long green leafy vines growing from the top of his bookcase.  I was never a fan of having dirt spills from the plants onto my floor and more importantly I never felt responsible enough to take care of them so I never had any while I was at school.  

Since I moved out I have been playing a lot with home automation using [Home Assistant (HA)](https://www.home-assistant.io/) using the [Zigbee Home Automation (ZHA)](https://www.home-assistant.io/integrations/zha/).  I realized that while I still might forget to water my plants, I can try to get my garden to water itself--and I can make cool graphs with all my sensor data.  

# Picking the plants
I have been trying to have a more vegetarian diet so I wanted to priorities my favorites such as [eggplants (brinjal)](https://gardeningsg.nparks.gov.sg/page-index/edible-plants/brinjal/), [tomatoes](https://gardeningsg.nparks.gov.sg/page-index/edible-plants/tomato/) and [winged beans](https://gardeningsg.nparks.gov.sg/page-index/edible-plants/winged-bean/).  After some quick googling, I found that the Singapore government encourages all these gardening efforts and even has amazing resources on how to get them growing.  

However, as I read through I realized I forgot about one important thing: bees.  Aside from winged beans most of the veggies I like need pollination and so need some kind of pollinators.  As I live on the 20th floors, I wasn't sure if they would come all the way up.  I needed something to check first so I decided to start small: [butterfly peas](https://gardeningsg.nparks.gov.sg/page-index/edible-plants/butterfly-pea/).  These are even [considered weeds](https://gardeningsg.nparks.gov.sg/page-index/edible-plants/butterfly-pea/) in some cases because they grow so easily so I'm making my life easier and they have the added benefit of being a bees-attractor.  

# Getting supplies and starting the seedlings
After consulting more internet resources, I found out I needed the following:  
* some loamy soils\*  
* pots of at least 20cm deep\*  
* trellis (for the vines to climb)  
* seedlings tray  
* seeds  

\*as advised by [NParks](https://gardeningsg.nparks.gov.sg/page-index/edible-plants/butterfly-pea/).  

I got the seedling tray that has some holes in the bottom since I'm completely new at this so I might overwater the seedlings and I also the lid to protect it while it's sitting on my balcony.  

I got the soil and seed, from a local seller for a combined of ~SGD5 and the rest I got off of AliExpress which totals to ~SGD10.  

I only just started to sow the seeds yesterday and we will see the result soon!  
<div class="l-body">
	{% include figure.html path="assets/img/seedling_tray_starting.jpeg" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>

# Minor automation
For now, the only sensor data I have is from 1 moisture sensor level using Zigbee (~SGD20).  I had some issues with setting it up with HA as it is ZHA does not fully understand how to interpret the data coming from the model, `_TZE204_myd45weu`.  Following the guide from the HA community [here](https://community.home-assistant.io/t/ts0601-tze200-ga1maeof-soil-moisture-sensor-and-zha/590913), I found out how to get ZHA to understand it with a custom "quirk" modified from this [original handler](https://github.com/zigpy/zha-device-handlers/blob/92c9fbc6d01a5d86f78d64183302b906aa7d8215/zhaquirks/tuya/ts0601_sensor.py).

```diff
-         MODELS_INFO: [("_TZE200_myd45weu", "TS0601")],
+         MODELS_INFO: [("_TZE204_myd45weu", "TS0601")],
```

After this fix and [enabling HA to use the quirks](https://community.home-assistant.io/t/how-to-setup-local-zha-quirks/341226/2?u=sewenthy), I managed to get the sensor moisture and temperature readings.  

I recorded the before and after watering moisture and temperature of the seedling tray, and I thought it was quite cool even though it's quite a simple graph currently.  

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/soil_moist_temp_graph.jpg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/seedling_tray_moisture_sensor.jpeg" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

# Plans for more automation

## Bees Checker
One thing I want to get going is running a small computer vision model to check if the garden is receiving any pollinators--i.e. whether my butterfly pea plan works or not.  This will help me decide on what kind of plants I can do after.  Of course, I can just wait and chance upon some bees pollinating the garden but that wouldn't be as fun as struggling for hours to get a model to process many images taken by a SGD25 IP camera and see if it can find the bees.  

My current plan is to find some light model off of [huggingface](https://huggingface.co/), ideally, something I can run locally with a small overworked GPU.  

## MySensors & Drip Irrigations
Since the moisture sensor I got was quite expensive for 1 sensor, I would want to expand my garden afterward with multiple pots, and sensors (much more data points for cool graphs) as well as having some kind of drip irrigation working off of that data. I need a more cost-effective method to get some readings.  I found some capacitive sensors on Aliexpress for <SGD1 each and it works with [Arduino](https://www.arduino.cc/). Great!  It's also a good thing I got the current Zigbee sensor because it is quite trusted, and well-tuned.  I can fine-tune the data of the Arduino output to be similar to the current sensor.  

Having all that data is well-and-good but I also need to have it connect to HA somehow (otherwise, no cool graphs).  This is when I chance upon [MySensors](https://www.mysensors.org/about/iot) which will also supposedly work with HA.  

I got some equipment from AliExpress again:
* Zigbee soil moisture and temperature sensor  
* Zigbee water pump  
* drip irrigation pipes  
* Nodemcu V2 ESP8266 (recommended by MySensors)  
* Arduino UNO R3 with the starter kit  
* 5 pcs of capacitive soil moisture sensor  
* jumper wires and breadboards  

This all went for ~SGD90 (ah, the price I pay for graphs).  

So this is my plan for the next few weeks--getting Arduino to read moisture sensors and pass it to my HA server.  If all goes well then I'll try making HA run the smart pump based on all the moisture sensors data.  This will be slightly tricky as my balcony sadly do not have any water outlet.  My plan is to use some kind of bucket (possibly with rain catchment into the bucket too), drill a hole at the bottom and use the pressure to run the pump.  

### OCaml for Arduino?
I love OCaml so the obvious next steps is to maybe have the code I write to integrate these sensors and HA in OCaml. Although it looks like there's not many widely used projects--I found a few that I made a fork of namely: [OMicroB](https://github.com/sewenthy/OMicroB) and [firmata](https://github.com/sewenthy/firmata).  I have not done much other than trying to get it to compile, but it's on the back of my mind.  
