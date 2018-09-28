# Imported Mutants

This is a small project showcasing what happens when you mutate an import.

```js
import * as jsonld from 'jsonld'

jsonld.documentLoader = async (url, callback) => {
  // magic 
  return theMagic
}

```

## Running

Imported Mutants consists of two script files: `a.js` and `b.js`.

These two scripts do exactly the same: require three dependencies and `console.log` them. But they do so in different order.

They only differ in one aspect: script `a` first imports the three dependencies, then logs them.

```js
const kidA = require('kida')
const amnesiac = require('amnesiac')
const thekingoflimbs = require('thekingoflimbs')

console.log('Kid A', kidA)
console.log('Amnesiac', amnesiac)
console.log('The King of Limbs', thekingoflimbs)
```

Script `b` imports one dependency, then logs it, imports the next one, and so on.

```js
const kidA = require('kida')

console.log('Kid A', kidA)

const amnesiac = require('amnesiac')

console.log('Amnesiac', amnesiac)

const thekingoflimbs = require('thekingoflimbs')

console.log('The King of Limbs', thekingoflimbs)
```

One would expect these two scripts to have the same results (identical stdout). But if we run them we'll see that's not the case:


```
$ node a
Kid A { everything: 'in the right place', morning: 'bell' }
Amnesiac { packt: 'Like Sardines in a Crushd Tin Box',
  kidA: { everything: 'in the right place', morning: 'bell' } }
The King of Limbs { amnesiac:
   { packt: 'Like Sardines in a Crushd Tin Box',
     kidA: { everything: 'in the right place', morning: 'bell' } } }

$ node b
Kid A { everything: 'in the right place' }
Amnesiac { packt: 'Like Sardines in a Crushd Tin Box',
  kidA: { everything: 'in the right place', morning: 'bell' } }
The King of Limbs { amnesiac:
   { packt: 'Like Sardines in a Crushd Tin Box',
     kidA: { everything: 'in the right place', morning: 'bell' } } }
```

This is happening because one of the dependencies, `amensiac`, is importing and mutating `kida`, which we too happen to be using in our scripts.

Even though programs are almost always written with imports first, statements afterwards (as in script `a`), projects are also usually pretty big, spanning several files and directories, and we have very little control over the order in which dependencies are imported (more similar to script `b`).