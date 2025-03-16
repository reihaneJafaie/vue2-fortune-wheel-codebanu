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
- Customizable modal dialog background color and content

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

    <ModalDialog
      @visible="visible"
      :statusVisible="showModal"
      :title="modalData.title"
      :message="modalData.message"
      :discountCode="modalData.discountCode"
      :backgroundColor="modalBackground"
    />
  </div>
</template>

<script>
import FortuneWheel from "vue2-fortune-wheel";
import ModalDialog from "vue2-fortune-wheel/components/modalDialog.vue";

export default {
  components: { FortuneWheel, ModalDialog },
  data() {
    return {
      prizes: [
        { id: 0, name: "Nothing", probability: 30, bgColor: "#FF0000" },
        { id: 1, name: "100 Points", probability: 20, bgColor: "#00FF00" },
        { id: 2, name: "10 Voucher", probability: 15, bgColor: "#0000FF" },
        { id: 3, name: "$3 Wallet", probability: 10, bgColor: "#FFFF00" },
        { id: 4, name: "30% Discount", probability: 10, bgColor: "#FF00FF" },
        { id: 5, name: "$3 Discount", probability: 10, bgColor: "#00FFFF" }
      ],
      styleWheel: { colorText: "black", textAlign: "center" },
      modalBackground: "#ffffff"
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
    },
    visible(toggle) {
      this.showModal = toggle;
    }
  }
};
</script>
```

## Props

| Prop Name         | Type      | Default   | Description                                 |
| ----------------- | --------- | --------- | ------------------------------------------- |
| `prizes`          | `Array`   | `[]`      | List of prizes with names, probabilities, and background colors |
| `prizesId`        | `Number`  | `null`    | Specific prize ID to be selected            |
| `textButton`      | `String`  | `'Spin'`  | Button label                                |
| `CenterBtn`       | `Boolean` | `true`    | Show center spin button                     |
| `soundStatus`     | `Boolean` | `true`    | Enable/disable sound                        |
| `imgBorder`       | `Boolean` | `true`    | Show animated border                        |
| `happyRain`       | `Boolean` | `true`    | Enable confetti effect                      |
| `styleWheel`      | `Object`  | `{}`      | Custom styles for the wheel                 |
| `backgroundColor` | `String`  | `#ffffff` | Background color of the modal dialog        |

## Modal Dialog Customization

The **ModalDialog** component allows further customization. You can modify:

- The **background color** using the `backgroundColor` prop.
- The **modal title and message** dynamically.
- The **discount code** display (if applicable).

Example customization:

```vue
<ModalDialog
  @visible="visible"
  :statusVisible="showModal"
  :title="'Congratulations!'"
  :message="'You won a prize!'"
  :discountCode="'DISCOUNT2025'"
  :backgroundColor="'#f0f0f0'"
/>
```

## Events

| Event Name          | Description                             |
| ------------------- | --------------------------------------- |
| `onCanvas`          | Triggers when the wheel starts spinning |
| `onRotateEnd`       | Fires when spinning stops               |
| `prizesRandomIndex` | Emits the selected prize index          |
| `visible`           | Controls modal visibility               |

## Styling

You can customize the wheel with the `styleWheel` prop:

```js
styleWheel: {
  colorText: "black",
  twoLineText: {
    status:false,
    outlineColor: "#000",
    inlineColor: "#fff",
    lineWidth: 3,
    },
    font: "bold 16px Arial",
    textAlign: "center",
    textBaseline: "middle",
    textOrientation: "vertical",
    // textOrientation : 'horizontal',
    shadow: {
      shadowColor: "rgba(0, 0, 0, 0.5)",
      shadowBlur: 14, // shadow blur level
      shadowOffsetX: 0, // shadow offset in X axis
      shadowOffsetY: 0, // shadow offset in Y axis
    },
    // stroke:{
    //   lineWidth : 10,
    //   color : '#000'
    // }
},
```

## Screenshots

### Spinning Wheel
![Spinning Wheel](./src/assets/images/wheel/wheel1.PNG)

### Winning Modal Dialog
![Winning Modal](./src/assets/images/wheel/Capture2.PNG)

## License

This package is licensed under the **MIT License**.

## Contributions

Feel free to contribute to this project! Submit pull requests or report issues on GitHub.

---

### Need Help?

If you have any questions or need support, please open an issue on GitHub or contact the maintainer.

Enjoy spinning! 🎡✨