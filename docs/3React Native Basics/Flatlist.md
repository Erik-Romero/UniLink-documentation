---
sidebar_position: 2
---

# Flatlist

The `FlatList` component is a core part of React Native that is used for efficiently displaying a scrolling list of changing, but similarly structured, data. `FlatList` is well-optimized and simple to use, making it one of the most commonly used components for rendering lists in React Native applications.

## Overview

`FlatList` works well for long lists of data, where the number of items might change over time. It only renders components for items currently on-screen, which can be a significant performance boost for lists with a large number of items. This is known as "windowing".

## Key Features

- **Performance Optimization**: Only renders items that are currently visible to the user, reducing the usage of device resources.
- **Easy to Use**: Provides a straightforward way to render lists using basic functions, without the need to manage the rendering of individual list items.
- **Customization**: Offers multiple props to customize its behavior and appearance, such as `ItemSeparatorComponent`, `ListHeaderComponent`, `ListFooterComponent`, and more.
- **Horizontal mode**: Can be configured to scroll horizontally with the `horizontal` prop.
- **Refresh Control**: Supports pull-to-refresh functionality with the `refreshControl` prop.

## Props

`FlatList` provides several props to configure its behavior:

- `data`: The array of data to be rendered as list items.
- `renderItem`: Takes an item from `data` and renders it into the list.
- `keyExtractor`: Used to extract a unique key for a given item.
- `onEndReached`: A callback function that is called once the end of the list has been reached, useful for implementing infinite scroll.
- `ItemSeparatorComponent`: Renders a component between list items (e.g., a divider).
- `ListHeaderComponent` and `ListFooterComponent`: Render components at the beginning and end of the list.
- `initialNumToRender`: Determines how many items to render in the initial batch.
- `onRefresh` and `refreshing`: Control the refresh behavior for the list.
- `horizontal`: If set to true, the list will scroll horizontally rather than vertically.

## Best Practices

When using `FlatList`, keep these best practices in mind:

1. **Optimize Item Components**: Ensure the components you render for each item are optimized to avoid performance issues.
2. **Use the `keyExtractor`**: Always provide a unique key to each item using the `keyExtractor` prop to help React identify which items have changed.
3. **Avoid Anonymous Functions**: Inline functions can lead to performance issues; instead, define them outside the render method.
4. **Leverage the `extraData` Prop**: If your list needs to be re-rendered to reflect changed data outside of the `data` prop, use `extraData` to trigger a re-render.

## Conclusion

`FlatList` is a powerful component that is integral to the development of React Native applications that handle lists. By following best practices and understanding its props and capabilities, developers can create smooth and performant list-based interfaces.

:::note

For more information head over to React native's website and documentation.

[Click Here](https://reactnative.dev/docs/getting-started)

:::