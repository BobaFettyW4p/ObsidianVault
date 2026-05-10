- what could cause a pipeline to stall and deviate from ideal behavior?
- *structural hazard* - when two stages of the pipeline at a given time need access to the same hardware resource
- instructions overlap such that a new instruction enters the pipeline at each stage
	- a potential structural conflict occurs when two separate instructions in the pipeline need to reference memory at the same time
	- it's possible to avoid by writing on clock down beat and reading on clock upbeat
>Suppose that data references constitute 40% of the mix, and the ideal CPI of the pipelined processor, ignoring the structural hazard, is 1. Assume that the processor with the structural hazard has a clock rate that is 1.05 times higher than the clock rate of the processor without the hazard. Disregarding any other performance losses, is the pipeline with or without the structural hazard faster, and by how much?

$$
\text{Average instruction time} = \text{CPI} \times \text{clock cycle time}
$$

$$
= (1+0.4 * 1) \times \frac{\text{Clock cycle time}}{1.05} = 1.3 \times \text{Clock cycle time}_{ideal}
$$

- as seen, the faster clock does not compensate for the structural hazards, and the processor with the structural hazard is 1.33x slower than the one without