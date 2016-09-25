# Immutable.js for beginners

This documentation is trying to help people who starts using [immutable.js](https://facebook.github.io/immutable-js/) but at the same time feeling confused by the original overwhelming documentation.  

**Worth Mentioning:** The documentation syntax of [immutable.js](https://facebook.github.io/immutable-js/) is written with [Typescript](http://www.typescriptlang.org/docs/handbook/basic-types.html), so if you are consfused, it is not your fault :)

**Note:** This will be a long term maintaining documentation, and hopefully it will be a community driven effort to make other developers' life easier when learning immutable.js.  **Pull Request are welcomed !!!**


## Map

Assume:

* you have imported your immutablejs somewhere  
* and, you have the following data structure

```
import { Map } from 'immutable'

let me = Map({
  name: 'roy',
  hobby: 'reading'
})
```

The following will show case basic operation for map

```
me.name // undefined, you can't reference the properties directly
me.get('name') // roy

me.set('name', 'iroy2000') // setting values
me.get('name') // roy ( what ??? )

// !!! Remember in immutable.js when you ""mutate" the data, 
// it does not modify the original value, but it will return a new one
// -----------------------------------------------------------------------

me = me.set('name', 'iroy2000') // setting values
me.get('name') // iroy2000 ( ah !!! )

me = me.update('name', item => item.toUppercase)
me.get('name') // IROY2000
```

The following to show case you can merge with plain object

```
let map2 = Map({
  age: '> 20'
})

let mapPlainObject = {
  gender: 'male'
}

// ImmutableJS treat JavaScript Array or Object as an Iterable
// ----------------------------------------------------------------
me.merge(map2, mapPlainObject)  // now the map includes age and gender
```

**Equality Checking**

Note that the equality checking is apply to not just Map only but to others as well.

Assume the have the following data structure

```
import { Map } from 'immutable'

let config = {
  name: 'roy',
  hobby: 'reading'
}

let me1 = Map(config)

let me2 = Map(config)


me1 == me2 // false
me1.equals(me2) // true


// If you just want to compare if they have same keys
// ----------------------------------------------------------------
map2.keySeq().equals(me.keySeq())

```

**Note:** If you don't understand what is Iterable, it is a collection of objects that allow you to iterate through ( or loop through ) with functions like `map()`, `filter()` or native js `for ... in`.  Here is the official documentation in immutablejs on [Iterable](https://facebook.github.io/immutable-js/docs/#/Iterable), and here is a [stackoverflow](http://stackoverflow.com/questions/18884249/checking-whether-something-is-iterable) link if you need more insight.


The following show case some common use case for a map

```

map1.has(['name'])  // true

map1.map((value, key) => `$value is cool`)

```

**Dealing with Nested structure**

Assume you have the following structure for your map

```
// The following show case you can create immutable data struture 
// directly from plain js object using fromJS helper function
// Just remember "array" -> "List" while "object" --> "Map"
// ----------------------------------------------------------------

import { fromJS } from 'immutable'

let me = fromJS({
  name: 'roy',
  profile: {
    gender: 'male',
    language:'javascript'
  }
})
```

The following will explain how to performing deep values getting / setting

```
me = me.setIn(['profile', 'language'], 'awesome javascript')

// The following two "gets" are the same
// ------------------------------------------
me.get('profile').get('language') // awesome javascript
me.getIn(['profile', 'language']) // awesome javascript

me = me.mergeDeep({
  profile : {
    hobby: 'reading'
  }
})

me.getIn(['profile', 'hobby'])  // reading

me = me.updateIn(['profile', 'hobby'], item => .toUpperCase())

me.getIn(['profile', 'hobby'])  // READING

// The following will grab the profile data
// and delete the key = "hobby" entry in it
// -------------------------------------------
me.updateIn(['profile'], profile => profile.delete('hobby'))


```

## List

#### Assume you have the follwing data structure

In immutable.js, most of the data structure shares a lot of the same functionalities, because they are inheriting from the same data structure - [Iterable](https://facebook.github.io/immutable-js/docs/#/Iterable)


It supports javascript array like operations, for example, `pop`, `push`, `concat`, `shift` ...

```
import { List } from 'immutable'

let items = List([1,2,3,4])

items = items.push(5)  // 1,2,3,4,5

items = items.pop() // 1,2,3,4

items = items.concat(5, 6)  // 1,2,3,4,5,6

items = items.shift() // 2,3,4,5,6


```


```
import { fromJS } from 'immutable'


// Remember "array" -> "List" while "object" --> "Map"
// ------------------------------------------------------

let me = fromJS({
  name: 'roy',
  friends: [
    { 
      name: 'captain america',
      properties: {
        equipment: 'shield'
      }
    },
    { 
      name: 'iron man',
      properties: {
        equipment: 'armor'
      } 
    },
    { 
      name: 'thor',
      properties: {
        equipment: 'hammer'
      }
    }
  ]
})


```

```

me.get('friends').get(0).get('name') // captain america
me.getIn(['friends', 0, 'name']) // captain america

me.get('friends').get(0).get('properties').get('equipment')  // shield
me.getIn(['friends', 0, 'properties', 'equipment']) // shield


me = me.setIn(['friends', 0, 'properties', 'equipment'], 'glove');

let hulk = {
  name: 'hulk',
  properties: {
    equipment: 'body'
  } 
}

// now you have new friends
me = me.update('friends', friends => friends.push(hulk))


let friendYouWantToKnowTheIndex = me.get('friends').findIndex(friend => {
 return friend.get('name') === 'captain america' 
})

me = me.setIn(['friends', friendYouWantToKnowTheIndex, 'equipment'], 'shield')

// How about shuffling all your friends ?
// A better version would be a custom comparator
// -----------------------------------------------
list = me.update('friends', 
    friends => friends.sortBy(() => Math.random())
)

```

The following show case some common use case for a List

```

let heroSalaryList = Immutable.List([
  {name: 'Thor', salary: 1000},
  {name: 'Iron Man', salary: 500},
  {name: 'Hawkeye', salary: 300}
])

// Iterate through the list and reduce the list to a value
// For example, add all salary of a hero 
// ( and divide by number of hero )
// ----------------------------------------------------------
let averageSalary = heroSalaryList.reduce((total, hero) => {   
  return total + hero.salary
}, 0) / heroSalaryList.count()

averageSalary == 600 // true

// fitler the list whose salary cannot be divided by 500
// ----------------------------------------------------------
let filteredHeroSalaryList = heroSalaryList.filter(
  hero => hero.salary % 500 > 1
)

filteredHeroSalaryList.count() // only 1 left

filteredHeroSalaryList.get(0).get('name') // Hawkeye


```

## Other Data Structure

#### Set
An array of unique elements, and it will remove any duplicates.

```
import { Set } from 'immutable'

let set1 = Set([1,1,2,2,3,3])

set1.count() // 3
set1.toArray() // 1,2,3

```

#### Record
Record let you create a javascript class that comes with default values.

```
import { Record } from 'immutable'

let Villian = Record({ 
   skill: 'do very bad thing'
})

let joker = new Villian({
  skill: 'playing poker'
})

joker.toJSON() // { skill: 'playing poker' }

// It will fall back to default value
// if you remove it
// -----------------------------------------------
joker = joker.remove('skill')  

joker.toJSON() // { skill: 'do very bad thing' }

// Values provided to the constructor not found 
// in the Record type will be ignored.
// -----------------------------------------------

let batwoman = new Villian({
  hairstyle: 'cool'
})

batwoman.toJSON() // { skill: 'do very bad thing' }

```


## Resources
* [immutable-docs by brentburg](http://brentburg.github.io/immutable-docs/api)
* [Immutablejs overview](http://www.darul.io/post/2015-07-06_immutablejs-overview)
* [Immutable live tutorial](http://blog.klipse.tech/javascript/2016/03/30/immutable.html)


