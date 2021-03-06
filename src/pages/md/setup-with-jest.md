# Setup with Jest

If you test a component that has react-spring's render-props in it, you might come across this error:

```bash
path\to\project\node_modules\react-spring\renderprops.js:1
    ({"Object.<anonymous>":function(module,exports,require,__dirname,__filename,global,jest){import _objectWithoutPropertiesLoose from '@babel/runtime/helpers/esm/objectWithoutPropertiesLoose';
```

And interestingly, your component works fine on a browser, and no errors are to be found in the console!

This is because Jest is using the ESM file instead of the CJS one, which is what Node understands. Normally, Jest will look for the main field in `package.json`, which might explain why it works in the previous version of react-spring.

## Solution

To make sure Jest picks up the CJS file instead of the ESM file, add this the the Jest configuration file (`jest.config.js`):

```js
module.exports = {
  moduleNameMapper: {
    'react-spring': '<rootDir>/node_modules/react-spring/web.cjs',
    'react-spring/renderprops': '<rootDir>/node_modules/react-spring/renderprops.cjs'
  }
}
```

Or if you prefer to do it in `package.json`:

```js
"jest": {
  "moduleNameMapper": {
    "react-spring": "<rootDir>/node_modules/react-spring/web.cjs",
    "react-spring/renderprops": "<rootDir>/node_modules/react-spring/renderprops.cjs"
  }
}
```

## Ok, let's try it out!

In `Thing.test.js` copy the following (Notice we are using Enzyme for this example)

```js
import React from 'react'
import {shallow} from 'enzyme'
import {Spring, animated} from 'react-spring/renderprops'

const Thing = () => (
  <Spring native from={{opacity: 0}} to={{opacity: 1}}>
    {style => <animated.div style={style}>It works!</animated.div>}
  </Spring>
)

describe('react-spring component test', () => {
  it('works!', () => {
    expect(shallow(<Thing />).exists()).toBe(true)
  })
})
```

And Hooray!

![nh](https://user-images.githubusercontent.com/43169879/53028522-f005e880-345e-11e9-9b91-c9d6610c146e.png)
