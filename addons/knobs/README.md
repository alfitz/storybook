# Storybook Addon Knobs

[![Greenkeeper badge](https://badges.greenkeeper.io/storybooks/storybook.svg)](https://greenkeeper.io/)
[![Build Status](https://travis-ci.org/storybooks/storybook.svg?branch=master)](https://travis-ci.org/storybooks/storybook)
[![CodeFactor](https://www.codefactor.io/repository/github/storybooks/storybook/badge)](https://www.codefactor.io/repository/github/storybooks/storybook)
[![Known Vulnerabilities](https://snyk.io/test/github/storybooks/storybook/8f36abfd6697e58cd76df3526b52e4b9dc894847/badge.svg)](https://snyk.io/test/github/storybooks/storybook/8f36abfd6697e58cd76df3526b52e4b9dc894847)
[![BCH compliance](https://bettercodehub.com/edge/badge/storybooks/storybook)](https://bettercodehub.com/results/storybooks/storybook) [![codecov](https://codecov.io/gh/storybooks/storybook/branch/master/graph/badge.svg)](https://codecov.io/gh/storybooks/storybook)
[![Storybook Slack](https://storybooks-slackin.herokuapp.com/badge.svg)](https://storybooks-slackin.herokuapp.com/)

Storybook Addon Knobs expose React props as widgets to the user. By interacting with the knobs, the user can dynamically change the props received by the component. Inside of [Storybook](https://storybook.js.org), knobs can be used as dynamic variables or supplied inline as component props. Knobs are displayed in an interactive panel alongside the story.

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

All knobs take in a user-facing label as the first parameter, followed by a variable number of additional parameters to populate the widget. Within every story, each knob must have a unique label to work properly.

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

The text knobs allows the user to interact with text used in a story using an input field.

##### Usage
```js
text(label, defaultValue);
```

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

The boolean knob allows the user to modify a boolean used in a story using a checkbox.

##### Usage
```js
boolean(label, defaultValue); // valid values are [true, false]
```

In the following example, the value of the knob is also used to dynamically set the button's text content.

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
      { isDisabled ? 'Can\'t Click Me!' : 'Click Me!'}
    //</button>
  );
});
```

### number

The number knob allows the user to modify a number used in a story using an input field.

##### Usage
```js
number(label, defaultValue); // valid values are numbers [0-9]
```

In the following example, the value of the knob is fed in as a prop to the component rendered in the story.

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

The number knob has an optional third parameter to allow the user to modify a number within a range using a slider. Slider options should be defined in an object with the following keys:
- range: `true` - used as a flag in the component to render a slider
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

The color knob allows the user to enter a color value. When added to a story, the knob is displayed as a color picker widget that converts the user's selection to a hex value.

The value passed into the knob should be a string representation of the color in the chosen format, such as a hex code, an rbg value, or a color keyword.

##### Usage
```js
color(label, defaultValue);
```

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

The object knob allows the user to modify a JSON object in a textarea.

##### Usage
```js
object(label, defaultValue); // defaultValue should be a valid JSON object
```

The knob's object input must be valid JSON, including any additional entries added by the user. When the input cannot be parsed into a JSON object, a red background is displayed on the knob.

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

Allows you to get an array from the user.

```js
import { array } from '@storybook/addon-knobs';

const label = 'Styles';
const defaultValue = ['Red']

const value = array(label, defaultValue);
```

> While editing values inside the knob, you will need to use a separator.
> By default it's a comma, but this can be override by passing a separator variable.
>
> ```js
> import { array } from '@storybook/addon-knobs';
>
> const label = 'Styles';
> const defaultValue = ['Red'];
> const separator = ':';
> const value = array(label, defaultValue, separator);
> ```

### select

Allows you to get a value from a select box from the user.

```js
import { select } from '@storybook/addon-knobs';

const label = 'Colors';
const options = {
  red: 'Red',
  blue: 'Blue',
  yellow: 'Yellow',
};
const defaultValue = 'red';

const value = select(label, options, defaultValue);
```

> You can also provide options as an array like this: `['red', 'blue', 'yellow']`

### date

Allow you to get date (and time) from the user.

```js
import { date } from '@storybook/addon-knobs';

const label = 'Event Date';
const defaultValue = new Date('Jan 20 2017');
const value = date(label, defaultValue);
```

### withKnobs vs withKnobsOptions

If you feel like this addon is not performing well enough there is an option to use `withKnobsOptions` instead of `withKnobs`.
Usage:

```js
story.addDecorator(withKnobsOptions({
  debounce: { wait: number, leading: boolean}, // Same as lodash debounce.
  timestamps: true // Doesn't emit events while user is typing.
}));
```

## Additional Links

-   [Live Storybook with Knobs](https://goo.gl/uX9WLf)
-   [Sample Storybook repo](https://github.com/kadira-samples/storybook-knobs-example).
-   [Video of Storybook with Knobs in action](https://www.youtube.com/watch?v=kopW6vzs9dg&feature=youtu.be).

## Typescript

If you are using typescript, make sure you have the type definitions installed for the following libs:

-   node
-   react

You can install them using `npm install -save @types/node @types/react`, assuming you are using Typescript >2.0.
