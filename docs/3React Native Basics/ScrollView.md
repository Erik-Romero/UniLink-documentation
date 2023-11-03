---
sidebar_position: 3
---

# Scroll View

`ScrollView` is a fundamental component in React Native that provides a scrollable container, which can host multiple components and views. It is useful when you have content that does not fit on the screen, but it's not optimized for large lists or items of varying sizes.

## Overview

Unlike `FlatList` or `SectionList`, `ScrollView` renders all of its child components at once, but it allows them to be scrollable. This means it's not memory efficient for large lists because it does not recycle list items like `FlatList`.

## Features of ScrollView

- Allows users to scroll through content that exceeds the device's screen dimensions.
- Can be both vertically and horizontally scrollable.
- Supports nested scrolling views.
- Can contain multiple components and views.
- Offers zooming capabilities via the `maximumZoomScale` and `minimumZoomScale` props on iOS.

## When to Use ScrollView

- Use `ScrollView` for a small number of items that can fit into memory comfortably.
- Ideal for forms, text documents, or a set of controls that exceed the screen's real estate.

## Important Props

- `horizontal`: When true, the scroll view's children are arranged horizontally instead of vertically.
- `pagingEnabled`: When true, the scroll view stops on multiples of the scroll view's size when scrolling. This can be used for horizontal pagination.
- `showsHorizontalScrollIndicator` and `showsVerticalScrollIndicator`: Controls the visibility of scroll indicators.
- `onScroll`: This prop accepts a function that is called when the user scrolls the view.
- `refreshControl`: A component used to provide pull-to-refresh functionality for the `ScrollView`.

## Best Practices

1. **Avoid Performance Issues**: Since all children are rendered at once, be cautious when placing a large number of elements within a `ScrollView`, as this can impact performance.
2. **Consider Alternatives for Large Lists**: For long lists of data where not all items are visible at once, consider using `FlatList` or `VirtualizedList` instead.
3. **Use Scroll Indicators**: Scroll indicators give users a visual cue about the amount of content and their current position within the scroll view.
4. **Test Scroll Performance**: Always test the performance on the actual device, since emulators or simulators may not give an accurate representation of performance issues.

## Conclusion

`ScrollView` is an essential component for scrolling content, suited for scenarios where the number of items is limited and known. It's simple to use and provides basic scrolling capabilities, but for performance-critical applications or large lists, consider using more suitable components like `FlatList` or `VirtualizedList`.


