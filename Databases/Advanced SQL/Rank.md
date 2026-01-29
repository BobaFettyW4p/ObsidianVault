![[../attachments/Pasted image 20260128074447.png]]

![[../attachments/Pasted image 20260128074457.png]]

### Ranking Functions
- there are multiple ranking functions available in the SQL standard, with each one taking a different approach to how ties are handled
	- *row_number*
		- returns a unique number for each row, with rankings arbitrarily assigned in case of a tie
	- *rank*
		- returns the same ranking in case of a tie, with gaps in the rankings
	- *dense_rank*
		- returns the same ranking in case of a tie, with no gaps in the rankings
![[../attachments/Pasted image 20260128074655.png]]