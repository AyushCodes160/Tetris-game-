# Tetris Web to React Native: Full Documentation

This document explains what you need to know in React Native to convert the current web Tetris project (HTML/CSS/JS) into a mobile app. It is focused on the concepts, APIs, and practical mapping from the existing files.

## 1) What Changes From Web to React Native

### No HTML/CSS/DOM
- React Native does not use HTML or the browser DOM.
- UI is built with React Native components (for example: `View`, `Text`, `Pressable`).
- Styling uses JavaScript objects, not CSS files.

### No Canvas Element
- On the web, you likely draw the board with `<canvas>` or DOM elements.
- In React Native, you can:
  - Render a grid using nested `View` components.
  - Or use a drawing library (for example: `react-native-svg`).

### Different Input Model
- Keyboard input is not a primary input on mobile.
- You will use touch gestures and buttons (tap, swipe).

### Different Project Structure
- React Native apps are built with Metro bundler, not HTML files.
- You will have files like `App.js`, `index.js`, and a `package.json`.

## 2) Core React Native Knowledge You Need

### 2.1 React Component Model
- React Native uses the same React component model as React for web.
- You will build the app in components with props and state.

Key concepts:
- Functional components
- `useState` for state
- `useEffect` for timers and lifecycle

### 2.2 Layout With Flexbox
- React Native layout is Flexbox by default.
- There is no CSS grid.
- Common patterns:
  - Column layout: `flexDirection: 'column'`
  - Row layout: `flexDirection: 'row'`
  - Centering: `alignItems`, `justifyContent`

### 2.3 Styling With StyleSheet
- Styles are plain objects.
- Example:

```js
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#111',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```

### 2.4 Rendering Lists and Grids
- You can map arrays to `View` elements.
- For performance, use `FlatList` or `SectionList` for long lists.
- For a Tetris grid, a nested map is common:

```js
{board.map((row, r) => (
  <View key={`r-${r}`} style={styles.row}>
    {row.map((cell, c) => (
      <View key={`c-${c}`} style={[styles.cell, { backgroundColor: cell }]} />
    ))}
  </View>
))}
```

### 2.5 Touch Input
- `Pressable` for buttons
- `PanResponder` or `react-native-gesture-handler` for swipes
- Example interactions for Tetris:
  - Swipe left/right: move
  - Swipe down: soft drop
  - Tap: rotate

### 2.6 Timers and Game Loop
- Use `setInterval` with `useEffect` to create a drop loop.
- Clear the interval on unmount to avoid leaks.

### 2.7 Local Assets
- Images and sounds are bundled differently.
- Use `require('./assets/sound.wav')` or `expo-av` for audio.

## 3) Mapping Your Current Files

### 3.1 index.html
- This becomes `App.js` UI structure.
- The `div` containers map to `View` components.

### 3.2 styles.css
- Move styles into a `StyleSheet` object in `App.js` or `styles.js`.
- Convert CSS rules to JS:

CSS:
```
.container { display: flex; }
```

React Native:
```js
container: { flexDirection: 'row' }
```

### 3.3 script.js
- Your game logic can largely stay in JS.
- You will wrap logic with React state and effects.
- Example: board state in `useState`, update via `setBoard`.

## 4) Recommended Stack (Two Options)

### Option A: Expo (Easiest)
- Best for quick start and easier device testing.
- Good for most games.

### Option B: React Native CLI
- More control but heavier setup.
- Use if you need native modules not supported in Expo.

## 5) Core React Native Packages You May Need

- `react-native-gesture-handler` for swipe input
- `react-native-reanimated` for smooth animations
- `react-native-svg` if you want canvas-like drawing
- `expo-av` for sound (if using Expo)

## 6) Example Component Skeleton

```js
import React, { useEffect, useState } from 'react';
import { View, Text, Pressable, StyleSheet } from 'react-native';

export default function App() {
  const [board, setBoard] = useState(createBoard());

  useEffect(() => {
    const interval = setInterval(() => {
      setBoard((prev) => tick(prev));
    }, 500);
    return () => clearInterval(interval);
  }, []);

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Tetris</Text>
      <View style={styles.board}>
        {board.map((row, r) => (
          <View key={`r-${r}`} style={styles.row}>
            {row.map((cell, c) => (
              <View key={`c-${c}`} style={[styles.cell, { backgroundColor: cell }]} />
            ))}
          </View>
        ))}
      </View>
      <View style={styles.controls}>
        <Pressable onPress={rotate} style={styles.button}>
          <Text>Rotate</Text>
        </Pressable>
        <Pressable onPress={moveLeft} style={styles.button}>
          <Text>Left</Text>
        </Pressable>
        <Pressable onPress={moveRight} style={styles.button}>
          <Text>Right</Text>
        </Pressable>
        <Pressable onPress={softDrop} style={styles.button}>
          <Text>Down</Text>
        </Pressable>
      </View>
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: '#111', alignItems: 'center', justifyContent: 'center' },
  title: { color: '#fff', fontSize: 24, marginBottom: 12 },
  board: { borderWidth: 2, borderColor: '#444', padding: 4 },
  row: { flexDirection: 'row' },
  cell: { width: 18, height: 18, margin: 1, backgroundColor: '#222' },
  controls: { flexDirection: 'row', marginTop: 16 },
  button: { backgroundColor: '#eee', padding: 8, marginHorizontal: 4, borderRadius: 4 },
});
```

## 7) Migration Plan

1) Create a new React Native app (Expo recommended).
2) Move game logic into a separate JS module (for example: `game.js`).
3) Render the board as a grid of `View` components.
4) Replace keyboard controls with touch controls.
5) Add sound and animations (optional).
6) Test on both iOS and Android.

## 8) Key React Native Differences Checklist

- No `document`, `window`, or DOM APIs.
- No CSS files; use `StyleSheet`.
- Touch and gestures instead of keyboard.
- Different build and packaging system.

## 9) Next Steps

If you want, I can:
- Create the initial Expo project structure.
- Port your current `script.js` logic into React Native state.
- Convert your `styles.css` into a React Native `StyleSheet`.
