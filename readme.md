# camera-controls
A camera control for three.js, similar to THREE.OrbitControls yet supports smooth transitions and es6 import.

## Why this fork
I forked this project to add `options` that let you customize which type of `events` you want to bind. 
I needed only the `wheel` `event` and this event allows you to isolate and have only the events you need. 
More in the `constructor` section 

[![Latest NPM release](https://img.shields.io/npm/v/camera-controls.svg)](https://www.npmjs.com/package/camera-controls)

## Demos

| camera move    | user input |
| ---            | ---        |
| Orbit rotation | left mouse drag / touch: one-finger move |
| Dolly (Zoom)   | middle mouse drag, or mousewheel / touch: two-finger pinch-in or out |
| Truck (Pan)    | right mouse drag / touch: two-finger move or three-finger move |

- [basic](https://yomotsu.github.io/camera-controls/examples/basic.html)
- [orthographic](https://yomotsu.github.io/camera-controls/examples/orthographic.html)
- [fit-and-padding](https://yomotsu.github.io/camera-controls/examples/fit-and-padding.html)

## Usage

```javascript
import * as THREE from 'three';
import CameraControls from 'camera-controls';

CameraControls.install( { THREE: THREE } );

// snip ( init three scene... )
const clock = new THREE.Clock();
const camera = new THREE.PerspectiveCamera( 60, width / height, 0.01, 100 );
const cameraControls = new CameraControls( camera, renderer.domElement, {/*
  keepMousedownListener: boolean,
  keepTouchstartListener: boolean,
  keepWheelListener: boolean,
  keepContextmenuListener: boolean,
*/});

( function anim () {

	// snip
	const delta = clock.getDelta();
	const hasControlsUpdated = cameraControls.update( delta );

	requestAnimationFrame( anim );

	// you can skip this condition to render though
	if ( hasControlsUpdated ) {

		renderer.render( scene, camera );

	}
} )();
```

## Constructor

`CameraControls( camera, domElement, options )`

- `camera` is a three.js perspective camera to be controlled.
- `domElement` is a HTML element for draggable area.
- `options` in Object.
  - `ignoreDOMEventListeners`: Default is `false`. if `true`, Mouse and touch event listeners will be ignored, and you can attach your handlers instead.
  - `keepMousedownListener`: Default is `true`, if `false`, `Mousedown` `event` listener won't be added to the element
  - `keepTouchstartListener`: Default is `true`, if `false`, `Touchstart` `event` listener won't be added to the element
  - `keepWheelListener`: Default is `true`, if `false`, `Wheel` `event` listener won't be added to the element
  - `keepContextmenuListener`: Default is `true`, if `false`, `Contextmenu` `event` listener won't be added to the element



## Terms

CameraControls uses Spherical Coordinates for orbit rotations. `theta` for azimuthal and `phi` for polar angle.

If your camera is Y-up, `theta` will be the angle for y-axis rotation and `phi` will be the angle for vertiacal position. (So far, CameraControls supports only Y-up though)

![](https://yomotsu.github.io/camera-controls/examples/fig1.png)

## Properties

- `.enabled`: Default is `true`. Whether or not the controls are enabled.
- `.minDistance`: Default is `0`. Minimum distance for dolly.
- `.maxDistance`: Default is `Infinity`. Maximum distance for dolly.
- `.minPolarAngle`: Default is `0`, in radians.
- `.maxPolarAngle`: Default is `Math.PI`, in radians.
- `.minAzimuthAngle`: Default is `-Infinity`, in radians.
- `.maxAzimuthAngle`: Default is `Infinity`, in radians.
- `.dampingFactor`: Default is `0.05`.
- `.draggingDampingFactor`: Default is `0.25`.
- `.phiSpeed`: Default is `1.0`. speed of phi rotation.
- `.thetaSpeed`: Default is `1.0`. speed of theta rotation.
- `.dollySpeed`: Default is `1.0`. speed of mouse-wheel dollying.
- `.truckSpeed`: Default is `2.0`. speed of drag for truck and pedestal.
- `.verticalDragToForward`: Default is `false`. The same as `.screenSpacePanning` in three.js's OrbitControls.
- `.dollyToCursor`: Default is `false`. `true` to enable Dolly-in to the mouse cursor coords.

## Events

CameraControls instance emits the following events.
To subscribe, use `cameraControl.addEventListener( 'eventname', function )`.

| event name     | timing |
| ---            | ---    |
| `'controlstart'` | When the user starts to control the camera via mouse / touches. |
| `'control'`      | When the user controls the camera (dragging). |
| `'controlend'`   | When the user ends to control the camera. |
| `'update'`       | When camera position is updated. |

## Methods

#### `rotate( azimuthAngle, polarAngle, enableTransition )`

Rotate azimuthal angle(theta) and polar angle(phi). `azimuthAngle` and `polarAngle` are in radian. `enableTransition` is in a boolean.

#### `rotateTo( azimuthAngle, polarAngle, enableTransition )`

Rotate azimuthal angle(theta) and polar angle(phi) to a given point.

#### `dolly( distance, enableTransition )`

Dolly in/out camera position. `distance` is in a number. `enableTransition` is in a boolean

#### `dollyTo( distance, enableTransition )`

Dolly in/out camera position to given distance.

#### `truck( x, y, enableTransition )`

Truck and pedestal camera using current azimuthal angle.

#### `moveTo( x, y, z, enableTransition )`

Move `target` position to given point.

#### `forward( distance, enableTransition )`

Move forward / backward.

#### `fitTo( meshOrBox3, enableTransition, { paddingTop?: number = 0, paddingLeft?: number = 0, paddingBottom?: number = 0, paddingRight?: number = 0 } )`

Fit the viewport to the object bounding box or the bounding box itself. paddings are in unit.

#### `setLookAt( positionX, positionY, positionZ, targetX, targetY, targetZ, enableTransition )`

It moves the camera into `position`, and also make it look at `target`.

#### `lerpLookAt( positionAX, positionAY, positionAZ, targetAX, targetAY, targetAZ, positionBX, positionBY, positionBZ, targetBX, targetBY, targetBZ, x, enableTransition )`

Similar to `setLookAt`, but it interpolates between two states.

#### `setPosition( positionX, positionY, positionZ, enableTransition )`

`setLookAt` without target, keep gazing at the current target.

#### `setTarget( targetX, targetY, targetZ, enableTransition )`

`setLookAt` without position, Stay still at the position.

#### `getPosition( out )`

Return its current position. `out` is in `THREE.Vector3`.

#### `getTarget( out )`

Return its current gazing target. `out` is in `THREE.Vector3`.

#### `saveState()`

Set current camera position as the default position

#### `reset( enableTransition )`

Reset all rotation and position to default.

#### `update( delta )`

Update camera position and directions. This should be called in your tick loop and returns `true` if re-rendering is needed.
`delta` is delta time between previous update call.

#### `toJSON()`

Get all state in JSON string

#### `fromJSON( json, enableTransition )`

Reproduce the control state with JSON. `enableTransition` is where anim or not in a boolean.

#### `dispose()`

Dispose the cameraControls instance itself, remove all eventListeners.
