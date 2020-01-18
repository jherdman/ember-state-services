# Ember State Services

[![Build Status](https://travis-ci.org/stefanpenner/ember-state-services.svg?branch=master)](https://travis-ci.org/stefanpenner/ember-state-services)
[![Code Climate](https://codeclimate.com/github/stefanpenner/ember-state-services/badges/gpa.svg)](https://codeclimate.com/github/stefanpenner/ember-state-services)
[![Ember Observer Score](http://emberobserver.com/badges/ember-state-services.svg)](http://emberobserver.com/addons/ember-state-services)
[![npm version](https://badge.fury.io/js/ember-state-services.svg)](http://badge.fury.io/js/ember-state-services)

This addon introduces a state management pattern for your ambitious applications.

State management is one of the most complex aspects of large application design and when done wrong often leads to bugs and errors. EmberJS contains 2 high-level avenues for storing state: controllers (long-term state) and components (short-term state). Controllers are singletons and any state you set on them will stay there until your application is reloaded or you override the previous value. Components on the other hand are created and destroyed as they enter/leave the DOM and any state that is set on them will be removed/reset each time they are recreated. As you build more complex applications you will find yourself needing a way to have some sort of middle ground solution. Something that has properties of both long-term state and short-term state. This is what ember-state-services sets out to provide.

An example could be a master/detail experience where the detail view is a component which allows editing of content. It would be unfortunate if navigating would lose un-saved changes (short-term state); it would also be unfortunate if the state between the edit components were to leak between each other (long-term state). Instead, the addon issues a unique state per reference key, which keeps management safe and easy.

[![](https://i.vimeocdn.com/video/539171813_640x358.jpg)](https://vimeo.com/141974064)

## Installation

```shell
ember install ember-state-services
```

## Setup

### State file

```js
/*
 * First create a state file that returns an object within app/states/<STATE_NAME>.js
 */
import EO from '@ember/object';

export default class MyState extends EOb {
};
```

## Usage

### Component

```js
import Component from '@ember/component';
import { action } from '@ember/object';
import stateFor from 'ember-state-services/state-for';

export default class MyComponent extends Component {
  tagName = 'form';

  /*
  * stateFor returns a computed property that provides a given
  * state object based on the 'email' property. Whenever email
  * changes a new state object will be returned.
  *
  * This allows us to create private state, accessible only to those
  * with access to the given model (email in this case) and the state
  * bucket (<STATE_NAME> in this case).
  */
  @stateFor('<STATE_NAME>', 'email') data;

  @action
  submitForm() {
    // apply changes to the email model
    this.email.setProperties(this.data);
  }
};
```

### Template

```js
<label>Subject: <Input @value={{this.data.subject}} /></label><br>
<label>from:   <Input @value={{this.data.from}} /></label><br>
<label>body:   <Textarea @value={{this.data.body}} /></label><br>
<button {{on "click" (fn this.submitForm)}}>Submit Form</button>
```

## Advanced

### Setting initial state

```js
import EO from '@ember/object';

export default class MyStateObject extends EO {
  static initialState(/* instance */) {
    return {
      foo: 'bar',
      hello: 'world'
    };
  }
}
```

### Using ember-buffered-proxy

```js
import BufferedProxy from 'ember-buffered-proxy/proxy';

const MyStateObject = BufferedProxy.extend();

MyStateObject.reopenClass({
  initialState() {
    return {
      content: {
        foo: 'bar',
        hello: 'world'
      }
    };
  }
});

export default MyStateObject;
```

Learn more about buffered proxy: https://github.com/yapplabs/ember-buffered-proxy

## Example Demo

```
git clone git@github.com:stefanpenner/ember-state-services.git
cd ember-state-services
npm i; bower i
ember server
```

Then visit the demo app: http://localhost:4200 in your browser
