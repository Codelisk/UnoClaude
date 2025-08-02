# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This repository contains the Uno Platform documentation, including:
- Main platform documentation (unoplatform/doc/)
- Uno Extensions documentation (unoextensions/)
- Uno Toolkit UI documentation (unotoolkitui/)

## Build Commands

### Documentation Build
To build the documentation locally:

```bash
cd unoplatform/doc
npm install
npm run build   # Production build with minified assets
npm start       # Development server with live reload
npm run prod    # Production-like environment
npm run strict  # Build with strict error checking
npm run clean   # Clean generated files
```

### Prerequisites
- Node.js (version specified in .nvmrc)
- DocFX installed via brew (macOS) or choco (Windows)

## Documentation Architecture

### Structure
- **unoplatform/doc/articles/**: Main documentation articles
- **unoextensions/Learn/**: Extensions documentation organized by feature
- **unotoolkitui/doc/**: UI toolkit documentation

### Key Technologies
- **DocFX**: Documentation generation framework
- **Gulp**: Build automation
- **Sass**: CSS preprocessing
- **Browser-sync**: Live reload during development

### Generated Files
- `_site/`: DocFX output directory
- `styles/`: Generated CSS and JS (do not edit manually)

## Key Uno Platform Concepts

### MVUX (Model-View-Update-eXtended)
Uno's reactive programming pattern that combines:
- Immutable models with `IFeed<T>` for async data streams
- `IState<T>` for user input
- Auto-generated ViewModels for data binding
- `FeedView` control for handling different data states

### Platform Support
Uno allows single-codebase development for:
- Windows (WinUI 3)
- iOS/macOS
- Android
- WebAssembly
- Linux (Skia)

### Core Extensions
- **Navigation**: Region-based navigation system
- **Authentication**: MSAL, OIDC, Web authentication
- **HTTP**: Refit and Kiota integration
- **Configuration**: Settings management
- **Localization**: Multi-language support
- **Markup**: C# UI as alternative to XAML