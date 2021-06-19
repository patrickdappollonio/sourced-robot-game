# Sourced Robot Game

## Development

Follow general conventions of other Vue.js applications and install the dependencies with `npm install`.

Use:

* `npm run serve` to serve a development version
* `npm run build` to build an output for release
* `npm run lint` to lint the code

## TODO

* Improve multiple components
* Use Vuex for state management rather than a message bus using a Vue instance
* Potentially use two different screens to present the game over section apart from the main game: currently, it's using a visibility CSS option to hide and show required items, since otherwise they will lose their state and the ability to trigger events (see previous point) if they're actually removed completely from the screen (timer, for example)
* For predefined levels, it could be possible to render them as a Javascript matrix of `[][]Number` and previously mark the places where the flags/stars will be, or even allow for more than one flag to be in the board in future levels
* Code organization can definitely be better, especially in `RobotGame.vue`
* Notes from the requirements were mostly ignored to match a ~3h window for development, but the following can be improved:
  * Grid is done using CSS and floating divs, it could use flexbox instead
  * In the usability side, `<button>` provide already most of the usability out of the box, but animations and other effects can be used to show better usability
  * No `aria` information has been provided for the SVG images and they're embedded as code to facilitate initial portability, but they should go on the assets folder later, so they can be delivered in a CDN -- although this entire site can be delivered that way as well
  * The max size for the grid and initial placement are hardcoded, not calculated: this could be improved further to allow bigger grids and obstacles
