---
layout: post
title: "Inside the Warehouse-Scale Computer: Datacenter Basics"
date: 2024-12-16
author: Darsh Shah
tags: [datacenter, power-distribution, cooling, energy-efficiency, infrastructure]
excerpt: "This book reframes the modern datacenter not as a collection of co-located servers, but as a single warehouse-scale computer requiring holistic design. Chapter 4 dives into the physical fundamentals -- tier classifications, power delivery chains (AC/DC, UPS, PDU), cooling systems (CRACs, chillers, cooling towers), and next-generation container-based designs -- showing that thoughtful engineering at the infrastructure level can yield significant energy savings."
---

*This post is adapted from a paper review I wrote during CMU's 18-845: Internet Services course, where we studied seminal papers in distributed systems, web architecture, and virtualization. I've converted my reviews into blog posts to share them more broadly.*

> **Paper:** *"The Datacenter as a Computer, Second Edition"* by Luiz Andre Barroso, Jimmy Clidaras, and Urs Holzle (Morgan & Claypool, 2013)

## TL;DR

This book reframes the modern datacenter not as a collection of co-located servers, but as a single warehouse-scale computer requiring holistic design. Chapter 4 dives into the physical fundamentals -- tier classifications, power delivery chains (AC/DC, UPS, PDU), cooling systems (CRACs, chillers, cooling towers), and next-generation container-based designs -- showing that thoughtful engineering at the infrastructure level can yield significant energy savings.

## Summary

*The Datacenter as a Computer* takes the concept of a datacenter and reimagines it as a single, massive warehouse-scale computer. The motivation is that modern large datacenters are fundamentally different from the traditional hosting facilities of earlier eras and cannot be viewed simply as a collection of co-located servers. Large portions of the hardware and software resources in these facilities must work in concert to efficiently deliver good levels of Internet service performance -- something that can only be achieved through a holistic approach to their design and deployment. The book describes the architecture of a warehouse-scale computer, covering its hardware design, software design, and other issues related to building and operating one.

This review focuses on Chapter 4: Datacenter Basics. What makes this chapter particularly compelling is the sheer scale at which datacenters are built and sustained -- the massive power requirements alone are mind-boggling. The chapter discusses how datacenters are classified into different tiers (from 1 to 4), how power is delivered to an individual server inside the datacenter through AC/DC lines, UPS units, PDUs, and breakers, and how various cooling systems work, including CRACs, chillers, and cooling towers. It concludes with a look at next-generation container-based datacenter designs.

The overarching goal of the chapter is to make the reader aware of the different design options available and to demonstrate that, with good engineering, significant energy savings in powering servers are achievable.

## Strengths

- **Redundant UPS design mirrors RAID principles.** The paralleling of UPS units so that the load of a failed device can be picked up by other devices is a clever approach. It effectively mimics the RAID concept to provide redundancy for availability at the power delivery level.

- **Fault containment through circuit-level protection.** Each circuit is protected by its own breaker, so a short in a server or power supply will trip only the breaker for that circuit -- not the entire PDU or even the UPS. This is a well-thought-out design decision for fault containment.

- **Insightful CFD visualizations.** The Computational Fluid Dynamics (CFD) analysis showing recirculation paths and temperature stratification for a rack is highly informative. It helps the reader understand how rack temperature varies with the amount and temperature of cool air present in the datacenter.

## Weaknesses

- **Missing cost data.** The author should have provided concrete numbers about datacenter costs -- equipment, labor, and power. For example: if a datacenter has *x* servers per rack and *y* racks, then the total equipment cost is *z*, the cost to set up AC power along with PDUs, UPS units, and breakers is *w*, and the ongoing power cost is *d* per hour. Such figures would have conveyed the true scale far more effectively.

- **Incorrect figure reference.** There is an error on page 53: the text states "As shown in Figure 4.2, cold air flows to the servers, heats up, and eventually reaches a heat exchanger to cool it down again," but Figure 4.2 actually depicts traditional AC vs. DC distribution efficiency, not the cooling and heat exchange cycle. The proofreader should have caught this.

- **Insufficient coverage of container-based datacenters.** How many server racks are present in one container? The author should have provided more detail about container-based datacenter designs, given how prominently they feature as a next-generation approach.

## Discussion Questions

- **UPS battery specifications.** How many battery cells, and what type of batteries (lithium-ion, lead-acid, etc.), are used inside an Uninterruptible Power Supply? It would be interesting to know the approximate duration for which such cells can sustain power-hungry machines during an outage.

- **Voltage conversion losses.** What is the noise and energy loss during the voltage switching operation from 430V three-phase to 110V single-phase?

- **Renewable energy and DC efficiency.** Since new datacenters are increasingly built near rivers, lakes, and wind farms that generate renewable electricity, how much would overall efficiency improve given the availability of a direct DC source? The chapter cites 76% efficiency, but with a DC source it could be significantly higher.

- **Cooling tower water replenishment.** As water flows down a cooling tower, some of it evaporates, drawing energy from the remaining water and cooling it. After repeated cycles, the water level decreases. How is new water added to the system? This is never explained. Additionally, why does hot water pass through the chiller on its way to the cooling tower? Since the cooling tower's purpose is to reduce water temperature, does this initial pass through the chiller assist with cooling, or does it waste the chiller's energy?

- **Impact of ambient temperature on datacenter design.** What effect does environmental temperature have on a datacenter? If one builds a datacenter in Nevada (a hot desert) versus Norway (cold most of the year), does the outside temperature meaningfully reduce cooling costs and power requirements? Or are datacenter walls sufficiently insulated from external conditions?

- **Liquid nitrogen cooling.** Could liquid nitrogen cooling be used in datacenters? What would the power savings be if each rack had a liquid nitrogen cooling tower attached to it?
