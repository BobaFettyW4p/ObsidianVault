
### Database design phases
- identifying and analyzing user requirements
- develop conceptual data model
- evaluate and perform normalization
- choosing a DBMS
- develop the logical model-writes schema
- develop the physical model
- develop a security plan
- evaluate physical model and perform tuning


### Establishing table structures
-  a table within the database represents each *subject*
	- each table is composed of fields, which represent the characteristics that define or describe the subject of the table

### Identifying subjects
- you can identify subjects by looking for *nouns* within the sentences that make up the response
	- subjects are always represented by nouns and identify a person, place, thing, or even
- there are some nouns that represent a *characteristic* of a person, place, thing, or event, however


#### Example
>“As an account representative, I’m responsible for ten clients. Each of my
clients makes an appointment to come into the showroom to view the
merchandise we have to offer for the current season. Part of my job is to
answer any questions they have about our merchandise and make
recommendations regarding the most popular items. Once they make a
decision on the merchandise they’d like to purchase, I write up a sales order
for the client. Then I give the sales order to my assistant, who promptly fills
the order and sends it to the client.”

> “As an *account representative*, I’m responsible for ten *clients*. Each of my
clients makes an *appointment* to come into the *showroom* to view the
*merchandise* we have to offer for the current *season*. Part of my *job* is to
answer any questions they have about our merchandise and make
recommendations regarding the most popular *items*. Once they make a
decision on the merchandise they’d like to purchase, I write up a *sales
order* for the client. Then I give the sales order to my assistant, who
promptly fills the order and sends it to the client.”

- therefore, our list of subjects is as follows:
	- Account Representative
	- Appointment
	- Assistant
	- Clients
	- Items
	- Job
	- Merchandis
	- Sales Order
	- Season
	- Showroom
- our list of subjects can be referred to as the *Preliminary Field List*
	- once we have created the Preliminary Field list, we can begin to determine whether a certain set of fields defines a particular subject
		- this collection of fields may outline a table in our database
![[../attachments/Pasted image 20260110103133.png]]

#### Four Levels of Data
- **Real world**
	- enterprise in its environment
- **Conceptual Model**
	- entities, entity sets, attributes, relationships
		- often represented as an ER diagram
- **Logical model of database-intension**
	- metadata, data about data
	- record types, data item types, data aggregates
	- schema - stored in system catalog (data directory or dictionary)
- **Data occurrences - extension**
	- database itself
	- data instances
	- files

![[../attachments/Pasted image 20260110150654.png]]
