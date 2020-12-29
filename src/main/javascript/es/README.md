### [GO TO BACK](../README.md)

# ES6 ~ ES11
https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Object_initializer

### Shorthand property names
```javascript
const name = "mook"
const age = 20

const ellie1 = {
    name: name,
    age: age
}

const ellie2 = {
    name,
    age
}

console.log(ellie1, ellie2) // 동일
```

### Destructuring assignment
```javascript
const person = {
    name: 'mook',
    age: 20,
}
const array = [1, 2]

{
    const name = person.name
    const age = person.age
    console.log(name, age)

    const first = array[0]
    const second = array[1]
    console.log(first, second)
}

{
    const { name, age } = person
    const [ first, second ] = array;
    console.log(name, age)
    console.log(first, second)
}

{
    const { name: personName, age: personAge } = person
    conosole.log(personName, personAge)
}

```

### Spread Syntax
```javascript
const obj1 = { key: 'key1' }
const obj2 = { key: 'key2' }
const array = [obj1, obj2]

{
    const arrayCopy = []
    array.forEach((e) => {
        arrayCopy.push(e)    
    })
}

{
    const arrayCopy = [...array]
    console.log(array, arrayCopy)
}

{
    const arrayCopy2 = [...array, { key: 'key3' }]
    console.log(array, arrayCopy, arrayCopy2)
}

{
    const obj3 = { ...obj1 }
    console.log(obj3)
}
```

### Default parameters
```javascript
{
    function printMessage(message) {
        if(message == null) {
            messagee = 'defaultMessage'
        }
        console.log(message)
    }
    printMessage('hello')
    printMessage()    
}

{
    function printMessage(message = 'defaultMessage') {
        console.log(message)
    }
    printMessage('hello')
    printMessage()    
}
```

### Template Literals
```javascript
const alphabet1 = 'a'
const alphabet2 = 'b'

{
    console.log('today alphabet is' + alphabet1 + 'and' + alphabet2)
}

{
    console.log(`today alphabet is ${alphabet1} and ${alphabet2}`)    
}
```

### Optional chaining
```javascript
const person1 = {
    name: 'mook',
    job: {
        title: 'S/W Engineer',
        manager: {
            name: 'Son'
        }
    }   
}
const person2 = {
    name: 'Han'
}

{
    function printManager(person) {
        console.log(person.job.manager.name)
    }
    printManager(person1)
    printManager(person2) // <- error Exception
}

{
    function printManager(person) {
        console.log(
            person.job
            ? person.job.manager
                ? person.job.manager.name
                : undefined
            : undefined
        )
    }
}

{
    function printManger(person) {
        console.log(person.job
            && person.job.manager
            && person.job.manager.name)
    }
}

{
    function printManager(person) {
        console.log(person.job?.manager?.name)
    }
}
```

### Nullish Coalescing Operator
```javascript
// Logical OR operator
// false: false, '', 0, null, undefined
{
    const name = 'mook'
    const userName = name || 'Guest'
    console.log(userName)
}

{
    const name = null
    const userName = name || 'Guest'
    console.log(userName)    
}

{
    const name = ''
    const userName = name || 'Guest'
    console.log(userName) // <- Bug!! expected '' but actually 'Guest'
}

{
    const name = 0
    const userName = name || 'undefined'
    console.log(userName) // <- Bug!! expected 0 but actually 'undefined'
}

{
    const name = ''
    const userName = name ?? 'Guest'
    console.log(userName) 
}

{
    const name = 0
    const userName = name ?? 'undefined'
    console.log(userName)
}
```
