# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

SwiftDown is a SwiftUI markdown editor component that provides live preview directly in the editor without web-based preview. It's built on top of [cmark](https://github.com/commonmark/cmark) via the [Down](https://github.com/johnxnguyen/Down) library for fast markdown parsing and supports both macOS and iOS platforms.

## Development Commands

### Building
```bash
swift build
```

### Testing
```bash
swift test
```

### Running Tests for Specific Target
```bash
swift test --filter SwiftDownTests
```

### Demo Application
The demo app is located in `SwiftDownDemo/` and can be opened via:
- Xcode: Open `.swiftpm/xcode/package.xcworkspace`
- Or use Xcode to open the Package.swift directly

## Architecture Overview

### Core Components

**SwiftDownEditor** (`SwiftDownEditor.swift`): The main SwiftUI component that wraps the underlying platform-specific text views. It uses `UIViewRepresentable` on iOS and `NSViewRepresentable` on macOS with separate implementations for each platform.

**SwiftDown** (`SwiftDown.swift`): Platform-specific text view implementations:
- iOS: Extends `UITextView` with markdown highlighting capabilities
- macOS: Custom `NSView` containing an `NSScrollView` with `NSTextView`

**MarkdownEngine** (`MarkdownEngine.swift`): Handles markdown parsing using the Down library (cmark wrapper). Converts markdown text into `MarkdownNode` objects with position information for styling.

**Theme System**:
- `Theme.swift`: Theme configuration and application system
- `Style.swift`: Style definitions for different markdown elements
- JSON theme files in `Sources/SwiftDown/Resources/Themes/`

**Storage** (`Storage.swift`): Custom `NSTextStorage` subclass that handles real-time markdown processing and styling application.

**SwiftDownHighlighter** (`SwiftDownHighlighter.swift`): Applies markdown styles to text ranges based on parsed markdown nodes.

### Key Architectural Patterns

1. **Platform Abstraction**: Uses conditional compilation (`#if os(iOS)` / `#else`) to provide platform-specific implementations while maintaining a unified SwiftUI interface.

2. **Real-time Processing**: Text changes trigger debounced markdown parsing and style application through the Storage system.

3. **Theme-driven Styling**: JSON-based theme system allows customization of colors, fonts, and sizes for different markdown elements.

4. **Coordinator Pattern**: SwiftUI coordinators handle delegate methods and manage the bridge between SwiftUI binding and UIKit/AppKit text views.

## Testing

Uses XCTest with Nimble for BDD-style assertions. Test files are in `Tests/SwiftDownTests/`:
- `ThemeTests.swift`: Theme parsing and configuration
- `MardownEngineTests.swift`: Markdown parsing functionality  
- `ThemeConfigPropertyTests.swift`: Theme property configuration
- `UtilsTests.swift`: Utility functions

## Key Dependencies

- **Down**: Markdown parsing (CommonMark/cmark wrapper)
- **Nimble**: Testing framework for expressive assertions

## Theme Development

Themes are JSON files defining:
- Editor colors (background, tint, cursor)  
- Markdown element styles (headings, body, bold, italic, links, code, etc.)
- Font specifications and sizes

Built-in themes: `default-dark.json`, `default-light.json`

## Platform Considerations

- **iOS**: Includes keyboard toolbar functionality for markdown shortcuts
- **macOS**: Custom scroll view with transparent scrollers for better visual integration
- **Text Storage**: Both platforms use custom NSTextStorage for real-time markdown processing
- **Debouncing**: 0.3s debounce on text changes to optimize performance