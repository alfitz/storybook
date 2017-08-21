# Storybook Addon Knobs

[![Greenkeeper badge](https://badges.greenkeeper.io/storybooks/storybook.svg)](https://greenkeeper.io/)
[![Build Status](https://travis-ci.org/storybooks/storybook.svg?branch=master)](https://travis-ci.org/storybooks/storybook)
[![CodeFactor](https://www.codefactor.io/repository/github/storybooks/storybook/badge)](https://www.codefactor.io/repository/github/storybooks/storybook)
[![Known Vulnerabilities](https://snyk.io/test/github/storybooks/storybook/8f36abfd6697e58cd76df3526b52e4b9dc894847/badge.svg)](https://snyk.io/test/github/storybooks/storybook/8f36abfd6697e58cd76df3526b52e4b9dc894847)
[![BCH compliance](https://bettercodehub.com/edge/badge/storybooks/storybook)](https://bettercodehub.com/results/storybooks/storybook) [![codecov](https://codecov.io/gh/storybooks/storybook/branch/master/graph/badge.svg)](https://codecov.io/gh/storybooks/storybook)
[![Storybook Slack](https://storybooks-slackin.herokuapp.com/badge.svg)](https://storybooks-slackin.herokuapp.com/)

Storybook Addon Knobs expose React props as widgets available to the user in an interactive panel displayed alongside a story. By interacting with the knobs, the user can dynamically change the props received by the component. 

Inside of [Storybook](https://storybook.js.org), knobs can be used as dynamic variables or supplied inline as component props. When a knob is modified, a rerender is triggered on the story, allowing the new values of the props to be passed into the component.

This addon works with Storybook for:
[React](https://github.com/storybooks/storybook/tree/master/app/react).

Below is an example of a Story that includes three types of Knobs: `boolean`,`object`, and `text`.

[![Storybook Knobs Demo](docs/storybook-knobs-example.png)](https://git.io/vXdhZ)

## Getting Started

To use knobs in Storybook stories, install `addon-knobs` as a dev dependency of the project.

```sh
npm install @storybook/addon-knobs --save-dev
```

Configure `addon-knobs` by including it in the `addons.js` file, located in the `config` directory of the Storybook project.

```js
import '@storybook/addon-knobs/register'
```

Once installed, add the `withKnobs` decorator to any story that uses knobs. Optionally, `withKnobs` can be configured as a global decorator for all of the stories in a project.

```js
import { storiesOf } from '@storybook/react';
import { withKnobs, boolean } from '@storybook/addon-knobs';

stories.addDecorator(withKnobs);

stories.add('with a button', () => (
  <button
    disabled={boolean('Disabled', false)}>
  //</button>
));
```

Knobs open up the ability to add dynamic variables to stories.

```js
import { storiesOf } from '@storybook/react';
import { withKnobs, text, number } from '@storybook/addon-knobs';

stories.addDecorator(withKnobs);

stories.add('dynamic variables', () => {
  const name = 'Arya Stark';
  const age = number('Age', 15);

  return (
    <div>
      `I am ${name} and I am ${age} years old.`
    //</div>
  );
});
```

All knobs take in a user-facing label as the first parameter, followed by a variable number of additional parameters used to populate the widget. Each knob within a single story must have a unique label in order to work properly.

```js
import { storiesOf } from '@storybook/react';
import { withKnobs, text } from '@storybook/addon-knobs';

stories.addDecorator(withKnobs);

stories.add('knob labels', () => {
  return (
    <div>
      {text('shared label', 'winter')}
      {text('shared label', 'is')} // Wrong! Collides with an existing label.
      {text('unique label', 'coming')}// Ok! This label is unique.
    //</div>
  );
});
```


## Available Knobs

At a minimum, all knobs take in parameters for a label and a default value. The return value of a knob is its current value. Optionally, the knob's value may be stored in a variable to be passed on as a Component prop or into another knob.

Knobs may be imported from the `@storybook/addon-knobs` module.

```js
import { text } from `@storybook/addon-knobs`;
```

As the names of the exported knobs are very general, it may be useful to use an alias when defining the imports.

```js
import { text as textKnob } from `@storybook/addon-knobs`;
```

The following lists the knobs available for use. 

### text

The `text` knobs allows the user to interact with text used in a story using an input field.

##### Usage
```js
text(label, defaultValue);
```

In the following example, the value of the text knob is used as a dynamic variable in the story.

##### Example
```js
import { storiesOf } from '@storybook/react';
import { withKnobs, text } from '@storybook/addon-knobs';

stories.addDecorator(withKnobs);

stories.add('text knob', () => {
  const label = 'Your Name';
  const defaultValue = 'Arya Stark';
  return (
    <div>{text(label, defaultValue)}//</div>
  );
});
```

### boolean

The `boolean` knob allows the user to modify a boolean used in a story using a checkbox.

##### Usage
```js
boolean(label, defaultValue); // valid values are [true, false]
```

In the following example, the value of the knob is also used to dynamically set the text content of the button.

##### Example
```js
import { storiesOf } from '@storybook/react';
import { withKnobs, boolean } from '@storybook/addon-knobs';

stories.addDecorator(withKnobs);

stories.add('boolean knob', () => {
  const isDisabled = boolean('Is the button disabled?', false);

  return (
    <button
      disabled={isDisabled}
    >
      { isDisabled ? 'Cannot Click Me!' : 'Click Me!'}
    //</button>
  );
});
```

### number

The `number` knob allows the user to modify a number used in a story using an input field.

##### Usage
```js
number(label, defaultValue); // valid values are numbers [0-9]
```

In the following example, the value of the knob is fed in inline as a prop to the component rendered in the story.

##### Example
```js
import { storiesOf } from '@storybook/react';
import { withKnobs, number } from '@storybook/addon-knobs';
import MarathonComponent from 'foo';

stories.addDecorator(withKnobs);

stories.add('number knob', () => {
  return (
    <MarathonComponent
      numMiles={number('number input', 26.2188)}
   // />
  );
});
```

### number bound by range

The `number` knob has an optional third parameter that allows the user to modify a number within a specified range using a slider. Slider options should be defined in an object with the following keys:
- range: `true` - used as a flag within the `number` component to render a slider
- min: `number` - minimum value of the range
- max: `number` - maximum value of the range
- step: `number` - scale of each step in the range

##### Usage
```js
number(label, defaultValue, options);
```

##### Example
```js
import { storiesOf } from '@storybook/react';
import { withKnobs, number } from '@storybook/addon-knobs';
import MarathonComponent from 'foo';

stories.addDecorator(withKnobs);

stories.add('number knob', () => {
  const averagePace = 10;
  const paceOptions = {
    range: true,
    min: 4,
    max: 15,
    step: 0.1,
  };
  const minutesPerMile = number('Minutes Per Mile', averagePace, paceOptions);

  return (
    <MarathonComponent
      minutesPerMile={minutesPerMile}
   // />
  );
});
```

### color

The `color` knob allows the user to enter a color value. When added to a story, the knob is displayed as a color picker widget that converts the user's selection to a hex value.

The value passed into the knob should be a string representation of the color in the chosen format, such as a hex code, an rbg value, or a color keyword.

##### Usage
```js
color(label, defaultValue);
```

In the example below, the color knobs instantiated inline as props passed to their respective components. Note that each example uses a different valid color representation.

##### Example
```js
import { storiesOf } from '@storybook/react';
import { withKnobs, color } from '@storybook/addon-knobs';
import { Gryffindor, Hufflepuff, Ravenclaw } from 'foo';

stories.addDecorator(withKnobs);

stories.add('color knob', () => {
  return (
    <div>
      <Gryffindor
        houseColor={color('Gryffindor Color', 'red')}
      ///>
      <Hufflepuff
        houseColor={color('Hufflepuff Color', '#c1ae1f')}
      ///>
      <Ravenclaw
        //houseColor={color('Ravenclaw Color', 'rgb(16, 44, 89)')}
      ///>
    //</div>
  );
});

```

### object

The `object` knob allows the user to modify a JSON object in a textarea.

##### Usage
```js
object(label, defaultValue); // defaultValue should be a valid JSON object
```

The knob's object input must be valid JSON, including any additional entries added by the user. When the input cannot be parsed into a JSON object, a red background is displayed on the knob.

In the example below, the current value of the object is stored in a variable that gets passed in as a prop to the `Styled Component`.

##### Example
```js
import { storiesOf } from '@storybook/react';
import { withKnobs, object } from '@storybook/addon-knobs';
import StyledComponent from 'foo';

stories.addDecorator(withKnobs);

stories.add('object knob', () => {
  const knobLabel = 'Styles';
  const defaultStyles = {
    backgroundColor: 'purple',
    fontSize: '14px',
  };
  const userStyles = object(knobLabel, defaultStyles);

  return (
    <StyledComponent
      userStyles={userStyles}
    ///>
  );
});


const value = object(label, defaultValue);
```


### array

The `array` knob allows the user to modify values in an array using a textarea. By default, the knob's separator is a comma. The knob takes an optional third parameter for a string to override the default separator.

##### Usage
```js
array(label, defaultValue, [separator]);
```

In the example below, the current value of the knob is passed in as a prop to the `GroceryComponent`.

##### Example
```js
import { storiesOf } from '@storybook/react';
import { withKnobs, array } from '@storybook/addon-knobs';
import GroceryComponent from 'foo';

stories.addDecorator(withKnobs);

stories.add('array knob', () => {
  const knobLabel = 'Grocery List';
  const listItems = ['apples', 'bananas', 'cookies'];
  const groceryList = array(knobLabel, listItems);

  return (
    <GroceryComponent
      groceryList={groceryList}
    ///>
  );
});
```

### select

The `select` knob allows the user to select a value from a dropdown.

Select options may be passed in using an object or an array. Using an object allows for flexibility for defining separate presentational option keys and option values, while an array will have the same select value as the option displayed.

An optional third parameter sets the default value of the select dropdown. If no default value is passed in, the first value in the collection will be displayed when the dropdown renders.

##### Usage
```js
select(label, defaultSelectValues [defaultSelectedValue]);
```

In the following example, an array of options is passed in to the knob. The currently selected value is rendered as a string within the story.

##### Example
```js
import { withKnobs, select } from '@storybook/addon-knobs';

const houses = [
  'Gryffindor',
  'Ravenclaw',
  'Hufflepuff',
  'Slytherin',
];

stories.addDecorator(withKnobs);

stories.add('select knob', () => {
  const selectedValue = select('house', houses);
  return (
    <div>
      { selectedValue }
    //</div>
  );
})
```


### date

The `date` knob allows the user to configure a date/time value through a calendar widget that includes a time selection.

Note that the default value passed into the `date` knob must be a [JavaScript Date object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date), not a string. The return value of the knob, however, is a string representation of milliseconds from the epoch. Additional work may be required to pass along date information in the proper format to the components in the story.

##### Usage
```js
date(label, defaultValue);
```

In the following example, a `Date()` is passed inline to the component, which changes dynamically as the value is toggled.

##### Example
```js
import { withKnobs, date } from '@storybook/addon-knobs';

stories.addDecorator(withKnobs);

stories.add('date knob', () => {
  return (
    <div>
      { date('select a date', new Date('Aug 1 2017')) }
    //</div>
  );
})
```

### withKnobsOptions

The knobs decorator may be adjusted if knobs are negatively impacting the performance of the stories.

##### Usage
```js
story.addDecorator(withKnobsOptions({
  debounce: { wait: number, leading: boolean}, // Same as lodash debounce.
  timestamps: true // Doesn't emit events while user is typing.
}));
```

## Additional Links

-   [Live Storybook with Knobs](https://goo.gl/uX9WLf)
-   [Sample Storybook repo](https://github.com/kadira-samples/storybook-knobs-example)
-   [Video of Storybook with Knobs in action](https://www.youtube.com/watch?v=kopW6vzs9dg&feature=youtu.be)

## Typescript

If you are using typescript, make sure you have the type definitions installed for the following libs:

-   node
-   react

You can install them using `npm install -save @types/node @types/react`, assuming you are using Typescript >2.0.
