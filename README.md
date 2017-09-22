# Immutable.js for beginners

This documentation is trying to help people who starts using [immutable.js](https://facebook.github.io/immutable-js/) but at the same time feeling confused by the original overwhelming documentation.  

**Worth Mentioning:** The documentation syntax of [immutable.js](https://facebook.github.io/immutable-js/) is written with [Typescript](http://www.typescriptlang.org/docs/handbook/basic-types.html), so if you are consfused, it is not your fault :)

**If you are looking for basic concept**, please look at the last section `Resources`, I put up a few links to hook you up with some important concepts in Immutable / Immutablejs.

## Table of Contents
* [Map](#map)
* [List](#list)
* [Other Data Structure](#other-data-structure) (Set, Record, Seq)
* [Resources](#resources)
* [Contributions](#contributions)

## Map

Assume:

* you have imported your immutablejs somewhere  
* and, you have the following data structure

```javascript
import { Map } from 'immutable'

let me = Map({
  name: 'roy',
  hobby: 'reading'
})
```

The following will show case basic operation for map

```javascript
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

```javascript
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

```javascript
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

```javascript

map1.has(['name'])  // true

map1.map((value, key) => `$value is cool`)

```

**Dealing with Nested structure**

Assume you have the following structure for your map

```javascript
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

```javascript
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

**Note** `fromJS` is an convenience function that covert nested Objects and Arrays into immutable `Map` and `List` automatically. So if you are using `fromJS`, do not apply or mix `Map` or `List` as you could introducde unintentional bugs.


## List

#### Assume you have the follwing data structure

In immutable.js, most of the data structure shares a lot of the same functionalities, because they are inheriting from the same data structure - [Iterable](https://facebook.github.io/immutable-js/docs/#/Iterable)


It supports javascript array like operations, for example, `pop`, `push`, `concat`, `shift` ...

```javascript
import { List } from 'immutable'

let items = List([1,2,3,4])

items = items.push(5)  // 1,2,3,4,5

items = items.pop() // 1,2,3,4

items = items.concat(5, 6)  // 1,2,3,4,5,6

items = items.shift() // 2,3,4,5,6

```


```javascript
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

```javascript
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

```javascript
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
An array ( or iterable type ) of unique elements, and it will remove any duplicates.

```javascript
import { Set } from 'immutable'

let set1 = Set([1,1,2,2,3,3])

set1.count() // 3
set1.toArray() // 1,2,3

// You can perform subtract, intersect and union on a set.
// But if your set is objects, in order to do that safely, 
// you will have to create variables before creating the set.

const set2 = Set(['hello', 'world', 'what', 'up']);

set2.subtract['hello']  // it will remove the value from the set

// consider the following case, which the set contains objects
const greet1 = { 'hello': 'world' };
const greet2 = { 'what': 'up' };

const greetSet = Set([greet1, greet2]);

greetSet.subtract([{'hello': 'world'}]);  // !!! It won't work !!!

greetSet.subtract([greet1]);  // It will work

```

#### Record
Record let you create a javascript class that comes with default values.

```javascript
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

#### Seq ( Lazy Operation )
Lazy operation means the chain methods ( operations ) are not exeucted until it is requested. And it will stop the execute when the return items fulfill the request.

```javascript
import { Seq } from 'immutable'


// Note: The order of the items are important
// ------------------------------------------------------------------
var femaleHero = Seq.of(
	  {name:'thor', gender: 'male'},
	  {name:'hulk', gender: 'male'},
	  {name:'black widow', gender: 'female'}
	)
    .filter(hero => hero.gender == 'female')
    .map(hero => `${hero.name} is a ${hero.gender}`)
                              

// Only performs as much work as necessary to get the result
// ------------------------------------------------------------------
femaleHero  // Nothing will be called

// This will iterate all three items until the 
// first "true" return.  All three hero are iterated
// util we found one "female hero" to return.
// ------------------------------------------------------------------
femaleHero.get(0)  // return 'black widow is a female'


// If we change the order of the Seq, the first "true" will
// return and the rest of the operations will not even get called.
//
// For example: 
// The request of getting first "female hero" is fulfilled,
// the progrm terminates, which means the next operation of 
// `filter()` and `map()` will not even get called.
// ------------------------------------------------------------------

{name:'black widow', gender: 'female'},
{name:'thor', gender: 'male'},
{name:'hulk', gender: 'male'}

```


## Resources

#### Readings
* [Introduction to Immutablejs](https://auth0.com/blog/intro-to-immutable-js/)
* [Immutablejs overview](http://www.darul.io/post/2015-07-06_immutablejs-overview)

#### Toolings
* [Immutable live tutorial](http://blog.klipse.tech/javascript/2016/03/30/immutable.html)
* [immutable-docs](http://brentburg.github.io/immutable-docs/api)

## Contributions
**Pull Request are welcomed !!!** Hopefully it will be a **community driven** effort to make other developers' life easier when learning immutable.js 

PR could include more examples, better introduction, presentations, resources ... etc.


