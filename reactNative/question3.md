# React Native Interview Question 3: React Native Performance: Unnecessary Re-renders & Optimizing FlatList

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



## 10 Points to Optimize a React Native App

1\. Optimize re-renders — Use `React.memo`, `useCallback`, and `useMemo` where profiling shows unnecessary work.

2\. Optimize FlatList — Use virtualization, stable keys, memoized row components, `getItemLayout`, and tuned batch settings.

3\. Optimize images — Resize images, use caching, prefer appropriate formats, and avoid loading full-resolution assets unnecessarily.

4\. Reduce bundle size — Remove unused dependencies, assets, and code; enable minification and use production builds.

5\. Improve API performance — Avoid duplicate requests, implement caching with TanStack Query or RTK Query, and cancel obsolete requests where appropriate.

6\. Optimize JavaScript execution — Avoid expensive calculations during rendering, debounce search inputs, and move suitable heavy work off the JS thread.

7\. Improve startup time — Defer non-critical initialization, lazy-load where supported, and avoid unnecessary work during app launch.

8\. Optimize animations — Use React Native's native animation capabilities or Reanimated's UI-thread worklets for suitable animations.

9\. Reduce memory usage — Clean up subscriptions, timers, event listeners, and sockets; avoid retaining large objects unnecessarily.

10\. Profile real devices — Use React Native DevTools, Android Studio Profiler, Xcode Instruments, and crash/performance monitoring tools. Validate changes in release builds.
