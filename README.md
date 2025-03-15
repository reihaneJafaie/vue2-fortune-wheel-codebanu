# Vue 2 Fortune Wheel Package Documentation

## Overview
The **Vue 2 Fortune Wheel** package is a customizable spinning wheel component for Vue.js applications. It allows users to define prizes, configure visual styles, and trigger events based on the spin results.

## Features
- Fully customizable prizes
- Configurable colors, fonts, and shadows
- Supports probability-based prize selection
- Plays sound effects on spin
- Displays a modal with results
- Confetti animation for winners

## Installation
You can install the package using npm:
```sh
npm install vue2-fortune-wheel
```
Or using yarn:
```sh
yarn add vue2-fortune-wheel
```

## Usage
Import the component into your Vue project:
```vue
<template>
  <div>
    <FortuneWheel
      ref="child"
      @prizesRandomIndex="prizeIndex"
      @onCanvas="onCanvasRotateStart"
      @onRotateEnd="onRotateEnd"
      :prizes="prizes"
      :prizesId="0"
      :soundStatus="true"
      :imgBorder="true"
      :styleWheel="styleWheel"
      :happyRain="true"
    />
    <button @click="onCanvasRotateStart">Spin</button>
  </div>
</template>

<script>
import FortuneWheel from "vue2-fortune-wheel";

export default {
  components: { FortuneWheel },
  data() {
    return {
      prizes: [
        { id: 0, name: "Nothing", probability: 30 },
        { id: 1, name: "100 Points", probability: 20 },
        { id: 2, name: "10 Voucher", probability: 15 },
        { id: 3, name: "$3 Wallet", probability: 10 },
        { id: 4, name: "30% Discount", probability: 10 },
        { id: 5, name: "$3 Discount", probability: 10 }
      ],
      styleWheel: { colorText: "black", textAlign: "center" }
    };
  },
  methods: {
    onCanvasRotateStart() {
      this.$refs.child.spinWheel();
    },
    onRotateEnd() {
      console.log("Spin ended");
    },
    prizeIndex(index) {
      console.log("Winning Prize: ", index);
    }
  }
};
</script>
```

## Props
| Prop Name     | Type      | Default | Description |
|--------------|----------|---------|-------------|
| `prizes`     | `Array`  | `[]`    | List of prizes with names and probabilities |
| `prizesId`   | `Number` | `null`  | Specific prize ID to be selected |
| `textButton` | `String` | `'Spin'`| Button label |
| `CenterBtn`  | `Boolean`| `true`  | Show center spin button |
| `soundStatus`| `Boolean`| `true`  | Enable/disable sound |
| `imgBorder`  | `Boolean`| `true`  | Show animated border |
| `happyRain`  | `Boolean`| `true`  | Enable confetti effect |
| `styleWheel` | `Object` | `{}`    | Custom styles for the wheel |

## Events
| Event Name         | Description |
|--------------------|-------------|
| `onCanvas`        | Triggers when the wheel starts spinning |
| `onRotateEnd`     | Fires when spinning stops |
| `prizesRandomIndex` | Emits the selected prize index |

## Styling
You can customize the wheel with the `styleWheel` prop:
```js
styleWheel: {
  colorText: "black",
  font: "bold 16px Arial",
  textAlign: "center",
  shadow: {
    shadowColor: "rgba(0, 0, 0, 0.5)",
    shadowBlur: 14,
    shadowOffsetX: 0,
    shadowOffsetY: 0
  }
}
```

## License
This package is licensed under the **MIT License**.

## Contributions
Feel free to contribute to this project! Submit pull requests or report issues on GitHub.

---
### Need Help?
If you have any questions or need support, please open an issue on GitHub or contact the maintainer.

Enjoy spinning! 🎡✨

