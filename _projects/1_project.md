---
layout: page
title: "Automated Filming Device"
description: An automated filming device with face-traking and remote control for one-person media creators.
img: assets/img/device.png
importance: 3
category: work
related_publications: false
---

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="/assets/img/device2.png" title="Automated Filming Device" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
A portable and user-friendly filming device that enables **solo creators** to record hands-free using **face tracking, servo-based rotation, and remote control**.

---

### Awards

- **Grand Prize (1st Place), HD Hyundai Mechatronics Competition** (Prize: **KRW 3,000,000**)

---

### Background: Rise of One-Person Media

- Growing demand for content creation without professional filming crews.
- Key challenges:
  - Abrupt and unstable camera angles
  - Continuous attention required during filming

---

### Product Overview

- **Problem:** Existing tools (tripods, selfie sticks) do not support automatic tracking or flexible control.
- **Solution:** A "Sunflower"-inspired device with **automatic face-tracking rotation** (vertical & horizontal) and **manual remote control**.
- **Value:** Hands-free filming, ease of use, portable design.

---

### Hardware

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="/assets/img/device3.png" title="Automated Filming Device Compoents" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
- **Components:**  
  - Wide-view camera  
  - Dual servo motors (vertical/horizontal rotation)  
  - Ball transfer unit (reduces friction, distributes weight)  
  - Raspberry Pi controller  
  - IR sensor for remote input  
- **Design features:**  
  - Assembly-friendly structure  
  - Camera placed at lower front to minimize rotation effects  
  - Easy disassembly and portability

---

### Software

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="/assets/img/device4_software.png" title="Automated Filming Device Compoents" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
- **Face Tracking:** Detects facial movements and adjusts servo angles with PID control.  
- **Remote Control:**  
  - Key 0 -> On/Off  
  - Keys 2/8 -> Up/Down rotation  
  - Keys 4/6 -> Left/Right rotation  
  - Key 5 -> Stop  
  - Key 9 -> Exit  
- **Servo Motor Control:**  
  - Uses a PD (Proportional-Derivative) controller  
- **User Interface:**  
  - Real-time LED indicators confirm recognition and recording status  
  - No need to check phone during use  
- **Underlying Framework:**  
  - LIRC (Linux Infrared Remote Control) processes IR signals  
  - NEC IR protocol used for reliable key input

Code available in "[GitHub repository](https://github.com/alvin0808/face-traker)".

---

### Usage Scenarios

<div class="row">
  <div class="col-sm-5 mt-3 mt-md-0">
    {% include figure.liquid path="/assets/img/device_revelation.gif" title="Device Revelation" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-7 mt-3 mt-md-0">
    {% include figure.liquid path="/assets/img/device_film_result.gif" title="Filming Result" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Left: Device operation video  Right: Output film
</div>
- Recording short-form videos (Reels, Shorts)  
- Cooking shows and live demonstrations  
- Online lectures  
- Pet or baby surveillance  
- Exercise and posture correction

---

### Market & Outlook

- **Advantages over competitors:**
  - Both vertical & horizontal rotation (others: horizontal only)
  - Manual remote control option
  - Easy assembly/disassembly
- **Expected Benefits:** Affordable, portable, and adaptable for various solo creators.

---

### Conclusion

- **Simple & Portable**
- **User-Friendly**
- **Reasonable Price**
- **Attractive Design**

Future potential: integration with advanced vision features (motion analysis, semantic recognition) to support broader applications in smart media and robotics.
