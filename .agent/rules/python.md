---
trigger: glob
globs: *.py
---

Python Development Guidelines
1. Style & Standards

    PEP 8 Compliance: Follow PEP 8 strictly for formatting.

    Naming Conventions:

        Variables and functions: snake_case

        Classes and Pydantic Models: PascalCase

        Constants: UPPER_CASE

    Readability: Prioritize clarity over cleverness or "one-liners."

    String Formatting: exclusively use f-strings for string interpolation.

2. Type Hinting

    Explicit Typing: Use explicit type hints for all function arguments, return values, and class attributes.

    Modern Syntax: Use standard types (e.g., list[str], dict[str, int]) or typing.Optional/Union where appropriate.

        Example: def process_data(items: list[str]) -> bool:

3. Data Validation (Pydantic)

    Usage: Use Pydantic (pydantic.BaseModel) for all data schemas, configuration management, and API request/response bodies instead of raw dictionaries.

    Field Definitions: Use Field() to provide metadata, default values, and validation constraints (e.g., gt, lt, regex).

    Settings Management: Use pydantic-settings (BaseSettings) for handling environment variables.

    Serialization: Prefer model_dump() and model_validate() (v2 syntax) over .dict() or .parse_obj().

        Example:
        Python

        from pydantic import BaseModel, Field

        class User(BaseModel):
            id: int = Field(..., gt=0)
            username: str = Field(..., min_length=3)

4. Testing (Pytest)

    Framework: Use pytest as the primary testing framework.

    File Structure: Prefix test files with test_ (e.g., test_auth.py) and test functions with test_.

    Fixtures: Use pytest.fixture for setup and teardown logic. Keep shared fixtures in conftest.py.

    Parametrization: Use @pytest.mark.parametrize to test multiple inputs/outputs with a single test function to avoid code duplication.

    Mocking: Use unittest.mock or pytest-mock to isolate unit tests from external dependencies (DBs, APIs).

5. Error Handling

    Specificity: Catch specific exceptions (e.g., ValueError, KeyError) rather than generic Exception.

    Control Flow: Use try/except/else/finally blocks where appropriate to ensure cleanup occurs.

    Custom Exceptions: Create custom exception classes inheriting from Exception for domain-specific errors.

6. Library Preferences

    Filesystem: Prefer pathlib.Path over os.path for file manipulation.

    HTTP Requests: Prefer httpx or requests (with timeouts explicitly set).

    Date/Time: Use datetime with timezone awareness (UTC default).

7. Logging

   Library: Use the standard `logging` module or `structlog` for structured JSON output.
   Configuration: Configure the root logger at the entry point of the application.