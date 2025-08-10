# API Reference

This section provides comprehensive API documentation for Project Aster.

## Core Package

### aster

```{automodule} aster
:members:
:undoc-members:
:show-inheritance:
```

## Adding New Modules

To add documentation for new modules, follow these steps:

1. **Create your module** in `src/aster/`
2. **Add docstrings** with Google or NumPy style
3. **Include type hints** for all functions and methods
4. **Update this file** to include your new module

### Example: Adding a Core Module

If you create `src/aster/core.py`, add this section:

```markdown
### aster.core

```{automodule} aster.core
:members:
:undoc-members:
:show-inheritance:
```
```

### Example: Adding a Utils Module

If you create `src/aster/utils.py`, add this section:

```markdown
### aster.utils

```{automodule} aster.utils
:members:
:undoc-members:
:show-inheritance:
```
```

## Examples

For usage examples, see the project repository and test files.

## Contributing

To add new API documentation, follow these guidelines:

1. Use docstrings with Google or NumPy style
2. Include type hints for all functions and methods
3. Add examples in docstrings where appropriate
4. Update this file to include new modules

For more information, see the [Contributing Guide](contributing.md).
