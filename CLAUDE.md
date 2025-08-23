# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

AssetRipper is a tool for extracting assets from Unity serialized files and asset bundles, converting them into the native Unity engine format. It supports Unity versions from 3.5.0 to 6000.2.X.

## Repository Structure

The codebase is organized into multiple projects:
- Core functionality: `AssetRipper.Import`, `AssetRipper.IO.Files`, `AssetRipper.Assets`
- Processing: `AssetRipper.Processing` with modules for different asset types
- Export: `AssetRipper.Export` and `AssetRipper.Export.UnityProjects`
- GUI: `AssetRipper.GUI.Web`, `AssetRipper.GUI.Free`
- Utilities: `AssetRipper.Yaml`, `AssetRipper.Numerics`

## Development Environment

- Target Framework: .NET 9.0
- Language Version: C# preview features enabled
- AOT Compatible: Projects are configured for Native AOT compilation

## Common Commands

### Building
```bash
dotnet build
```

### Running Tests
```bash
dotnet test
```

### Publishing (AOT)
```bash
dotnet publish -c Release -r <runtime>
```
Where `<runtime>` can be `win-x64`, `linux-x64`, `osx-x64`, etc.

### Running the GUI Application
```bash
dotnet run --project Source/AssetRipper.GUI.Free/
```

## Architecture Overview

The application follows a modular architecture:
1. **Import Layer**: Handles reading Unity files (serialized files, asset bundles, resource files)
2. **Asset Layer**: Represents Unity objects in memory with a common interface
3. **Processing Layer**: Transforms and processes assets after import
4. **Export Layer**: Converts processed assets to output formats (Unity project structure, raw files)
5. **GUI Layer**: Provides a web-based interface for interacting with the application

Key design patterns include:
- Separation of concerns with distinct layers
- Interface-based design for extensibility
- Source generators for performance-critical code paths