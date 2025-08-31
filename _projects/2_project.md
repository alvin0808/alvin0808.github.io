---
layout: page
title: Espresso Machine Disassembly
description: Espresso machine disassembly & analysis about pump
img: assets/img/Espresso_machine.png
importance: 3
category: work
giscus_comments: false
---

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="/assets/img/Espresso_machine_structure.png" title="Pump Disassembly" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Espresso machine disassembly process to identify structure and constraints.
</div>

### Introduction

- The espresso machine pump was selected as the main analysis subject due to its role in pressurizing water flow.
- The disassembly process allowed observation of connections, spring-damper structure, and constraints, although internal parts could not be fully measured.
- This limitation required theoretical modeling combined with experimental verification.

### Theoretical Modeling

<div class="row">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid path="/assets/img/accurate_model.png" title="Pump Noise Measurement" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid path="/assets/img/simplified_model.png" title="Pump Noise Measurement" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Left: accurate modeling Right: Assumptions
</div>
- The pump piston was modeled as a spring-damper dynamic system.
- Assumptions included estimated elastic modulus and damping values due to limited access to internal parts.
- MATLAB simulation was used to predict piston movement, expected flow rate, and noise characteristics under different pressures.

### Experiments

<div class="row">
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid path="/assets/img/model_matlab.png" title="Pump Noise Measurement" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm-6 mt-3 mt-md-0">
    {% include figure.liquid path="/assets/img/matlab_simulation.png" title="Pump Noise Measurement" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Experimental setup and result for pump flow rate using MATLAB.
</div>

- Flow Rate Measurement

  - Measurements at 0 bar, 4 bar, and 6 bar.
  - Observed ratio: 1 : 1.36 : 1.65 (close to theoretical 1 : 1.3 : 1.6).

- Noise Measurement (dB)
  - 0 bar: 72.4 dB
  - 4 bar: 70.5 dB
  - 6 bar: 64.5 dB
  - Noise decreased as extraction pressure increased.

### Results and Discussion

- Theoretical assumptions and MATLAB predictions were supported by experimental results.
- Minor discrepancies were due to parameter estimation errors, such as unknown spring stiffness.
- Despite limited disassembly, the dynamic system approach was effective.
- The workflow demonstrates system modeling, simulation, and experiment as a transferable methodology.

### Conclusion

- The pump analysis combined reverse engineering, dynamic modeling, and experimental validation.
- Key insights:
  - Flow rate and noise can be predicted with a spring-damper model.
  - Experimental results support the theoretical assumptions.
- Future work: full disassembly and direct measurement of material and spring properties to refine the model.
