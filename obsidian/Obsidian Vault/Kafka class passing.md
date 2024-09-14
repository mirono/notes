Here is what I suggest:
* Use json value serializer
* Pass class name as key
```python
@dataclass  
class User:  
    cls: str = field(default="User", init=False)  
    name: str  
    favorite_color: str  
    favorite_number: int
```
Producer:
```Python
send(TOPIC, key=user.cls.encode('utf-8'), value=user.__dict__)
```
Consumer will create the class dynamically:
```Python
key = msg.key.decode("utf-8")  
cls = globals()[key] 
user = cls(**msg.value)
```
So we don't need a class factory with 
```Python
if class_name == ".....":
	msg = User(...)
elseif class_name == "....":
	masg = Animal(...)
...
```


In this case:
producer:
PRODUCER.send(TOPIC, value=asdict(user))

consumer:
cls = globals()[msg.value["cls"]]  
msg.value.pop("cls")  
user = cls(**msg.value)




#kafka