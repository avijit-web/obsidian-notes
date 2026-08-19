---
tags:
  - database
  - mongodb
---
## inc operator
this is used to increment multiple fields in a document

example

```

db.students.updateMany({},{$inc:{age:2}})
```

## max and min operator
example

```
db.students.updateOne({name:"Sita",{$max:{age:50}}})
```

this only updates if the age of site is less than 50

```

db.students.updateOne({name:"Sita",{$min:{age:23}}})

```
this only works if age of sita is greater than 23

## mul

this operator is used to multiply a field

example
```
db.students.updateOne({name:"Sita",{$mul:{age:2}}})
```

this multiplies age of sita * 2

## set and unset

these operators are used to set a field or remove a field

example

```
db.students.updateOne({name:"Sita",{$unset:{age:2}}})
```
this removes the age field

```
db.students.updateOne({name:"Sita",{$unset:{age:30}}})
```

this again creates the age field and sets the age

## rename

this operator renames a field

```
db.students.updateOne({},{$rename:{age:"studentAge"}})
```

this renames the age field to studentAge

## upsert

inserts or updates the field

example

```
db.students.updateOne({name:"Golu",{$set:{age:100}},{upsert:true}})
```

this will find a document where name is golu if does not find it then it will insert it  and then set the age as 100


# UPDATING NESTED ARRAYS


example data

```

{
  _id: ObjectId("63a28500782db8f12a7c877b"),
  name: 'Akshit',
  hobbies: ['TV Shows'],
  hasMacBook: true,
  bio: 'I am savage boi.',
  experience: [
    { company: 'Amazon', duration: 2 },
    { company: 'Google', duration: 3 }
  ],
  age: 26
}


query=>

db.students.find({experience:{$elemMatch:{duration:{$lte:1}}}})

finds the students whose experience is less than equal to 1

db.students.find({experience:{$elemMatch:{duration:{$lte:1}}}},{$set:{"experience.$.neglect":true}})

this updates only first matched object in the experience array and sets a field named neglect :true

db.students.find({experience:{$elemMatch:{duration:{$lte:1}}}},{$set:{"experience.$[].neglect":true}})


this updates all the objects in the experience array and sets field named neglect :true


db.students.find({experience:{$elemMatch:{duration:{$lte:1}}}},{$set:{"experience.$[e].neglect":true}},{arrayFilters:[{"e.duration":{$lte:1}}]})




```


## Push

```
{
  _id: ObjectId("6396a083f3b30b3e904d7953"),
  name: 'Ram',
  Hobbies: ['Walk', 'Cricket'],
  identity: { hasPanCard: false, hasAdhaarCard: true },
  bio: 'I do nothing.',
  experience: [
    { company: 'KPMG', duration: 1, neglect: true },
    { company: 'EY', duration: 1.5, neglect: 1 },
    { company: 'TCS', duration: 0.5, neglect: true }
  ],
  age: 14
}

db.students.updateOne({name:"Ram"},{$push:{experience:{company:"Meta",duration:2}}})

this will push meta experience in the experience array

db.students.updateOne({name:"Ram"},{$addToSet:{experience:{company:"Meta",duration:2}}})

this will only push non duplicate items


db.students.updateOne({name:"Ram"},{$pull:{experience:{company:"Meta",duration:2}}})

this removes the meta company array ..removes multple if matched


db.students.updateOne({name:"Ram"},{$pop:{experience:1}})

removes the last item in the experience array

db.students.updateOne({name:"Ram"},{$pop:{experience:-1}})

removes the first item in the experience array

```



