![[Pasted image 20240716160337.png]]

## Suggested flow

`changed IDs` = set of ids from ID mapper notification
`requests` = get list of open requests from db
`for each request:`
	`responses` = list of completed responses (i.e. have all their pages) not fully reconciled from the db
		`for each response:`
			get all `entity identification (and identification type)` and build a set of ids from it
				`intersect` = the intersection between this set and the `changes` set
					`if intersect:`
						post a message to the bus about the need to reconcile a response with the `response id` and the intersect ids

So the process will loop on all required requests, each of their not reconciled responses and will output a "reconcile" message to the bus if the repponse ids (all or some) are present in the notification list of ids.

## Sql

Selecting responses:
```sql
SELECT disclosure.response ... TBD
```

Get Ids for a response:
```sql
... TBD
```

## Reconcile Message

```python
@dataclass(frozen=True)  
class ReconcileResponse(Event):
	response_id: str
	changed: list(Identifier)
```
Where id is defined like in the mapper:
```python
@dataclass(frozen=True)  
class Identifier:  
    """Internal representation of a identifier with its type."""  
    type: str  
    id: str
```


#issuer_id #shareholder_insights
