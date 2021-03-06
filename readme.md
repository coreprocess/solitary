# solitary

Isolates private implementations within classes.

![npm downloads total](https://img.shields.io/npm/dt/solitary.svg) ![npm version](https://img.shields.io/npm/v/solitary.svg) ![npm license](https://img.shields.io/npm/l/solitary.svg)

Ever wanted a genuinely private scope within your classes, e.g. to protect methods from being called by derived classes accidentally? This small library offers a convenient approach to isolate private implementations within classes.

## Installation

Install the `solitary` module via

```sh
npm install solitary --save
```

or

```sh
yarn add solitary
```

## Usage

The module provides a single function as default export. Here is the signature:

```js
import solitary from 'solitary';
solitary(Class<PrivateClass>)
  => { pvt: PublicClass  => PrivateClass,
       pub: PrivateClass => PublicClass
     }
```

### Example

An example is worth a thousand words. This illustration promotes the decorator library [bindthis](https://github.com/coreprocess/bindthis) too. Of course, you could use `solitary` without it.

```js
import solitary from 'solitary';
import bindthis from 'bindthis';
import React from 'react';

const { pvt, pub } = solitary(
  // truly private stuff
  @bindthis
  class {
    onScroll() {
      // get some property
      const someProp = pub(this).props.someProp;
      ...
      // do something with the root node
      if(this._root) {
        ...
      }
    }

    setRoot(node) {
      // store root node for later use
      this._root = node;
    }
  }
);

export default class ExampleComponent extends React.Component {
  componentDidMount() {
    window.addEventListener('scroll', pvt(this).onScroll);
  }

  componentWillUnmount() {
    window.removeEventListener('scroll', pvt(this).onScroll);
  }

  render() {
    return (<div ref={pvt(this).setRoot}>...</div>);
  }
};
```
