# API_design_documentation_exercise

1. We are implementing NoSQL
    - We are implementing NoSQL because it stores data in simple straightforward forms.

# How we would structure our Schema:

```ruby
db.createCollection("People",{
    validator:{
        $jsonSchema: {
            bsonType:"object",
            required: ["name", "age", "num_of_people"],
            properties: {
                name: {
                    bsonType:"string",
                    description: "must be a string and is required"
                },
                age: {
                    bsonType:"number",
                    minimum: 0,
                    description: "must be a number and is required"
                },
                num_of_people: {
                    minimum: 1,
                    description: "must be a number and is required"
                },
            }
        }
    }
})
```
```ruby
db.createCollection("house",{
    validator:{
        $jsonSchema: {
            bsonType:"object",
            required: ["address", "owner"],
            properties: {
                address: {
                    required: ["postcode", "street_address"],
                    properties:{
                        postcode:{
                            bsonType: "string"
                            description: "must be a string if the field exists"
                        },
                        street_address:{
                            bsonType: "string",
                            description: "must be a string if the field exists"
                        }
                owner: {
                    bsonType:"string",
                    description: "must be a string and is required"
                        }
                    }
                },
            }
        }
    }
})
```

# NoSQL Function to search for people within certain age brackets and with specific household sizes

```ruby
db = connect("localhost:8080/people")

function ageBracket(){
    db.people.find($and:[{age:{$gt:17}}, {age:{$lt:100}, {num_of_people:{$gt:2}}}])
}
```

# The requests our API should be capable of handling

## Example routers to get data on people:

```ruby
router.get('/people', (req, res) => {
    const people = People.all
    res.status(200).json({data: people})
});

router.get('/people/:id', (req, res) => {
    try {
        const peopleId = parseInt(req.params.id);
        const peopleEntry = Entry.findById(peopleId)
        if (!peopleEntry) {
            throw new Error('This person does not exist!')
        }
        res.json(peopleEntry);
    } catch (err) {
        res.status(404).send({ message: err.message })
    }
})

```

## Example routers to get data on houses
```ruby
router.get('/houses', (req, res) => {
    const people = House.all
    res.status(200).json({data: house})
});

router.get('/houses/:id', (req, res) => {
    try {
        const housesId = parseInt(req.params.id);
        const housesEntry = Entry.findById(housesId)
        if (!housesEntry) {
            throw new Error('This person does not exist!')
        }
        res.json(housesEntry);
    } catch (err) {
        res.status(404).send({ message: err.message })
    }
})

router.get('/houses/address', (req, res) => {
    const people = House.address
    res.status(200).json({data: house})
});

router.get('/houses/owner', (req, res) => {
    const people = House.owner
    res.status(200).json({data: house})
});
```

## Example post and delete routers:

### Post:
Backend:
```ruby
router.post('/people', (req, res) => {
    const data = req.body
    const newEntry = Entry.create(data)
    res.status(201).send(newEntry);
    res.status(201).json(newEntry);
});
```

Frontend:
```ruby
const postEntry = async (name, age, num_of_people) => {
    await fetch(`https://made_up_webpage.com/people`, {
        method: 'POST',
        headers: {
            "Content-Type": "application/json"
        },
        body: JSON.stringify({
            "name": name,
            "age": age,
            "num_of_people": num_of_people
        })
    })
}
```
### Delete
Backend:
```ruby
router.delete('/:id', (req, res) => {
    const peopleId = parseInt(req.params.id);
    const peopleToDestroy = People.findById(peopleId);
    peopleToDestroy.destroy();
    res.status(204).send();
})
```

## Example to update data:

### Update people:
```ruby
router.put('/people/:id', (req, res) => {
    
    const peopleId = parseInt(req.params.id);
    
    const peopleEntry = Entry.findById(peopleId)

    if(req.body.name) {
        peopleEntry.update(peopleId, "name", req.body.name)
    } 
    if(req.body.age) {
        peopleEntry.update(peopleId, "age", req.body.age)
    } 
    if(req.body.num_of_people) {
        peopleEntry.update(peopleId, "num_of_people", req.body.num_of_people)
    } 
    
    res.status(201).send(peopleEntry);
})

```
