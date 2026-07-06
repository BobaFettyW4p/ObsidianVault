#### The Estimation Problem
- we have defined quantities such as:
	- the mean function
	- covariance
	- autocovariance
- but how do we estimate these quantities from observed data?
	- the challenge is we usually observe each random variable only one
		- where do repeated samples come from?
![[screenshots/Pasted image 20260705165619.png]]

#### Ensemble Averages
- imagine repeating the same experiment many times under identical conditions
- each repetition produces another realization of the same process
- if we have $m$ realizations, the ensemble estimate of the mean at time $t$ is:
$$\mu_t \approx \bar{x}_t = \frac{1}{m} \sum_{j=1}^m x_{tj}$$
- we average across realizations, not across time

![[screenshots/Pasted image 20260705165927.png]]

#### Why Ensembles are Rare
- ensemble averages are the ideal way to estimate statistical quantities
- Unfortunately, we usually observe only one realization of  a process
	- Ex.
		- one century of temperatures
		- one history of the stock market
		- one patient's ECG
		- one earthquake recording
- so, how can one realization tell us about the underlying random process?
	- Make additional assumptions
- 