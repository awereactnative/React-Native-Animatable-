# React-Native-Animatable-
### Intro
Like the following super cool animation in your next React Native app? 
![image](https://res.cloudinary.com/dh6l45sly/image/upload/v1658612767/awereactnative/react-native-animable/36341976-06326ad6-13f7-11e8-8fe1-ab947bbea5c8_q7rd6s.gif)

Lets see how we can do that.
# Demo
![36341974-f697e5d8-13f6-11e8-8e2a-21d8c2a4b340](https://user-images.githubusercontent.com/86215353/181424535-9972c3c5-cbfe-463b-80bd-8cd9b52375c5.gif)



### Installation 
Simply run following command in your React Native project,

```
npm install react-native-animatable --save
```

### Usage
To animate things you must use the createAnimatableComponent composer similar to the Animated.createAnimatedComponent. The common components View, Text and Image are precomposed and exposed under the Animatable namespace. If you have your own component that you wish to animate, simply wrap it with a Animatable.View or compose it with:

```
import * as Animatable from 'react-native-animatable';
MyCustomComponent = Animatable.createAnimatableComponent(MyCustomComponent);
```

Animations

```
<Animatable.Text animation="zoomInUp">Zoom me up, Scotty</Animatable.Text>
```

### Looping

To make looping animations simply set the iterationCount to infinite. Most animations except the attention seekers work best when setting direction to alternate.

```
<Animatable.Text animation="slideInDown" iterationCount={5} direction="alternate">Up and down you go</Animatable.Text>
<Animatable.Text animation="pulse" easing="ease-out" iterationCount="infinite" style={{ textAlign: 'center' }}>❤️</Animatable.Text>
```

Animatable looping demo.

#### Properties
*Note: Other properties will be passed down to underlying component.*

| Prop | Description | Default |
|---|---|---|
|**`animation`**|Name of the animation, see below for available animations. |*None*|
|**`duration`**|For how long the animation will run (milliseconds). |`1000`|
|**`delay`**|Optionally delay animation (milliseconds). |`0`|
|**`direction`**|Direction of animation, especially useful for repeating animations. Valid values: `normal`, `reverse`, `alternate`, `alternate-reverse`. |`normal`|
|**`easing`**|Timing function for the animation. Valid values: custom function or `linear`, `ease`, `ease-in`, `ease-out`, `ease-in-out`, `ease-in-cubic`, `ease-out-cubic`, `ease-in-out-cubic`, `ease-in-circ`, `ease-out-circ`, `ease-in-out-circ`, `ease-in-expo`, `ease-out-expo`, `ease-in-out-expo`, `ease-in-quad`, `ease-out-quad`, `ease-in-out-quad`, `ease-in-quart`, `ease-out-quart`, `ease-in-out-quart`, `ease-in-quint`, `ease-out-quint`, `ease-in-out-quint`, `ease-in-sine`, `ease-out-sine`, `ease-in-out-sine`, `ease-in-back`, `ease-out-back`, `ease-in-out-back`. |`ease`|
|**`iterationCount`**|How many times to run the animation, use `infinite` for looped animations. |`1`|
|**`iterationDelay`**|For how long to pause between animation iterations (milliseconds). |`0`|
|**`transition`**|What `style` property to transition, for example `opacity`, `rotate` or `fontSize`. Use array for multiple properties.  |*None*|
|**`onAnimationBegin`**|A function that is called when the animation has been started. |*None*|
|**`onAnimationEnd`**|A function that is called when the animation has been completed successfully or cancelled. Function is called with an `endState` argument, refer to `endState.finished` to see if the animation completed or not. |*None*|
|**`onTransitionBegin`**|A function that is called when the transition of a style has been started. The function is called with a `property` argument to differentiate between styles. |*None*|
|**`onTransitionEnd`**|A function that is called when the transition of a style has been completed successfully or cancelled. The function is called with a `property` argument to differentiate between styles. |*None*|
|**`useNativeDriver`**|Whether to use native or JavaScript animation driver. Native driver can help with performance but cannot handle all types of styling.  |`false`|
|**`isInteraction`**|Whether or not this animation creates an "interaction handle" on the InteractionManager.  |`false` if `iterationCount` is less than or equal to one|

### Imperative Usage

#### Animations

All animations are exposed as functions on Animatable elements, they take an optional `duration` argument. They return a promise that is resolved when animation completes successfully or is cancelled. 

```js
import * as Animatable from 'react-native-animatable';

class ExampleView extends Component {
  handleViewRef = ref => this.view = ref;

  bounce = () => this.view.bounce(800).then(endState => console.log(endState.finished ? 'bounce finished' : 'bounce cancelled'));

  render() {
    return (
      <TouchableWithoutFeedback onPress={this.bounce}>
        <Animatable.View ref={this.handleViewRef}>
          <Text>Bounce me!</Text>
        </Animatable.View>
      </TouchableWithoutFeedback>
    );
  }
}
```

To stop any ongoing animations, just invoke `stopAnimation()` on that element. 

You can also animate imperatively by using the `animate()` function on the element for custom animations, for example:
```
this.view.animate({ 0: { opacity: 0 }, 1: { opacity: 1 } });
```

#### Generic transitions

##### `transition(fromValues, toValues[[, duration], easing])`

Will transition between given styles. If no `duration` or `easing` is passed a spring animation will be used. 

##### `transitionTo(toValues[[, duration], easing])`

This function will try to determine the current styles and pass it along to `transition()` as `fromValues`. 

```js
import * as Animatable from 'react-native-animatable';

class ExampleView extends Component {
  handleTextRef = ref => this.text = ref;

  render() {
    return (
      <TouchableWithoutFeedback onPress={() => this.text.transitionTo({ opacity: 0.2 })}>
        <Animatable.Text ref={this.handleTextRef}>Fade me!</Animatable.Text>
      </TouchableWithoutFeedback>
    );
  }
}
```

## Custom Animations

Animations can be referred to by a global name or a definition object. 

### Animation Definition Schema

An animation definition is a plain object that contains an optional `easing` property, an optional `style` property for static non-animated styles (useful for `perspective`, `backfaceVisibility`, `zIndex` etc) and a list of keyframes. The keyframes are refered to by a number between 0 to 1 or `from` and `to`. Inspect the source in the `definitions` folder to see more in depth examples. 

A simple fade in animation: 

```js
const fadeIn = {
  from: {
    opacity: 0,
  },
  to: {
    opacity: 1,
  },
};
```
```html
<Animatable.Text animation={fadeIn} >Fade me in</Animatable.Text>
```

Combining multiple styles to create a zoom out animation: 

```js
const zoomOut = {
  0: {
    opacity: 1,
    scale: 1,
  },
  0.5: {
    opacity: 1,
    scale: 0.3,
  },
  1: {
    opacity: 0,
    scale: 0,
  },
};
```
```html
<Animatable.Text animation={zoomOut} >Zoom me out</Animatable.Text>
```

To make your animations globally available by referring to them by a name, you can register them with `initializeRegistryWithDefinitions`. This function can also be used to replace built in animations in case you want to tweak some value. 

```js
Animatable.initializeRegistryWithDefinitions({
  myFancyAnimation: {
    from: { ... },
    to: { ... },
  }
});
```
