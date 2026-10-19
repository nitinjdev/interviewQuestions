# React Native Performance: Unnecessary Re-renders & Optimizing FlatList

> youtube video : https://www.youtube.com/watch?v=AZ9i3eoyrnE 

I first use React Native DevTools Profiler to identify which components render frequently and which commits are expensive. Then I inspect unstable props, parent state updates, context changes, and expensive calculations. I use React.memo, useCallback, and useMemo selectively based on profiling results.

For a FlatList with 1,000 items, I rely on virtualization, memoize row components, use stable keys and callbacks, implement getItemLayout for fixed-height rows, and tune batch rendering and the window size. I also optimize images and avoid expensive row calculations. Finally, I validate scrolling FPS, memory, and responsiveness in a release build on real devices.


1. explain this in three steps: identify the bottleneck, find the cause, and apply the smallest effective optimization.

# Step 1 :  Use the right debugging tools

| Tool	| What I use it for |
|---|---|
| React DevTools Profiler | 	Identify components that render frequently and measure render duration |
|React Native DevTools |	Inspect React components, performance traces, and JavaScript execution |
| console.count()	 | Quickly count how often a component renders during development|
| React <Profiler> |	Programmatically measure commits and rendering duration|
| Flipper / performance tools	 | Inspect native and JS performance where supported by the project setup |