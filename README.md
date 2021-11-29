# bottlecap.js - 2D Game Framework

![bottlecap.js](https://github.com/harshsinghdev/bottlecap/raw/main/banner.png)

### Table of Contents  
- [About](#about)  
- [Docs](#Docs)  
- [Components](#components)
- [Example](#example)
- [Installation](#installation)

### About

bottlecap is a minimalist 2d game framework written in ES6. it is a collection of tiny components (called **bottlecaps**) you can combine to create a 2d game.

### Docs

You can read the Docs at bottlecap.js's [wiki](https://github.com/harshsinghdev/bottlecap/wiki).

### Components

* canvas.js - create/initialize canvas, clearCanvas, etc.
* camera.js - a simple 2d center-fixed camera
* keyboard.js - simple keyboard input
* sprite.js - basic sprite animation
* sound.js - play sound, set volume, stop sound
* loader.js - load images, sounds, json files asynchronously
* game.js - main game class to give your game a structure, includes the game loop
* collision.js - simple aabb, circle-in-circle, point-in-circle, point-in-rect, circle-in-rect collision detection and resolve aabb collision of two hitboxes
* device.js - get device info
* dom.js - a tiny but powerful library for manipulating the dom
* math.js - basic math functions for game development, Vec2 class
* utils.js - basic utility functions
* emitter.js - simple event system

### Example

**MyGame.js**

```javascript
import Game from './bottlecap/game.js';
import Camera from './bottlecap/camera.js';
import { createCanvas } from './bottlecap/canvas.js';
import { getDirection } from './bottlecap/keyboard.js';
import { Vec2, TWO_PI } from './bottlecap/math.js';
import { circleInRect } from './bottlecap/collision.js';
import { randomInt } from './bottlecap/utils.js';

export default class MyGame extends Game {

  init() {
  
    // create a canvas with width and height equal to window's width and height and set its background color to black
  
    const { ctx, cnv, clearCanvas } = createCanvas(window.innerWidth, window.innerHeight, 'black');
    
    // assign the destructured variables returned by createCanvas function to `this`
    // you can just do Object.assign(this, createCanvas(window.innerWidth, window.innerHeight, 'black')); if you like
    
    Object.assign(this, { ctx, cnv, clearCanvas });
    
    // append the canvas element to the document's body
  
    document.body.appendChild(cnv);
    
    // create a camera
    
    this.camera = new Camera(ctx);
    
    // -- initial game state
    
    this.score = 0;
    
    this.player = {
      x: 0,
      y: 0,
      w: 50,
      h: 50,
      speed: 100
    };
    
    this.coins = [];
    
    for(let i = 0; i < 20; i++) {
      this.coins.push({
        pos: Vec2.create(
          randomInt(100, this.cnv.width - 100),
          randomInt(100, this.cnv.height - 100)
        ),
        radius: 10,
        visible: true
      });
    }
    
    console.log('Game Initialised');
  
  }
  
  update(dt) {
    
    const direction = getDirection(); // { x, y }
    
    this.player.x += direction.x * this.player.speed * dt; // move player left or right depending on direction.x's value [1, -1]
    this.player.y += direction.y * this.player.speed * dt; // move player up or down depending on direction.y's value [1, -1]
    
    for(let i = 0; i < this.coins.length; i++) {
      const coin = this.coins[i];
      // if the coin is visible &&
      // the coin (circle) is colliding with the player (rect)
      if(coin.visible && circleInRect(coin.pos.x, coin.pos.y, coin.radius, this.player.x, this.player.y, this.player.w, this.player.h)) {
        coin.visible = false; // set the visiblity of the coin to false
        this.score += 10; // add 10 to player's total score
      }
    }
    
    this.camera.lookAt(this.player.x, this.player.y); // update camera's target location
    
    this.camera.update(dt); // update the camera
    
  }
  
  render() {
  
    this.clearCanvas();
    
    this.camera.attach(); // -- camera attached
    
    // Render coins
    
    this.ctx.fillStyle = 'yellow';
    
    for(let i = 0; i < this.coins.length; i++) {
      const coin = this.coins[i];
      // render coin only if it is visible
      if(coin.visible) {
        this.ctx.beginPath();
        this.ctx.arc(coin.pos.x, coin.pos.y, coin.radius, 0, TWO_PI, false);
        this.ctx.closePath();
        this.ctx.fill();
      }
    }

    this.ctx.fillStyle = '#fff';
    
    this.ctx.fillRect(this.player.x, this.player.y, this.player.w, this.player.h); // render the player

    this.camera.detach(); // -- camera detached
    
    this.ctx.fillStyle = "#fff";
    
    this.ctx.fillText(`Score: ${this.score}`, 20, 20); // display the score
  
  }

}
```

**main.js**

```javascript

import { ready } from './bottlecap/dom.js';
import MyGame from './MyGame.js';

const initGame = () => {
  const game = new MyGame(); // create an instance of the game

  game.run(); // runs the game
};

// call initGame when DOM state is ready

ready(initGame);
```

**index.html**

```html
<!Doctype html>
<html>
<head>
  <title>My Game</title>
</head>
<body>

  <script src="./main.js" type="module"></script>

</body>
</html>
```

### Installation

We have decided to use Native Javascript Module Import/Export to avoid any build steps. So you can simply clone this repo or Download the latest release from [Releases](https://github.com/harshsinghdev/bottlecap/releases).

### Games made using bottlecap
* [Hydrogen](https://hypervoid.itch.io/hydrogen)
* [Play Or Die](https://hypervoid.itch.io/play-or-die)
* [Sneaky Tails](https://hypervoid.itch.io/sneaky-tails)
* [SlideToShoot](https://hypervoid.itch.io/slide-to-shoot)
