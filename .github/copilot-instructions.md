# Copilot Instructions for Cypress Test Suite

## Project Overview
This is a Cypress end-to-end testing suite organized into examples demonstrating testing patterns and best practices. The structure includes organized test categories (getting-started, advanced-examples, practice) with fixture data and custom command support.

## Architecture & Key Patterns

### Test Organization
- **Location**: `cypress/e2e/` - All spec files follow naming convention `*.cy.js`
- **Categories**: 
  - `1-getting-started/` - Basic test patterns (e.g., `todo.cy.js` tests todo list app)
  - `2-advanced-examples/` - In-depth testing patterns (actions, assertions, DOM querying, network requests, etc.)
  - `practice/` - Custom test files for experimentation
- **Pattern**: Each test file uses `describe()` blocks with `beforeEach()` for common setup (usually `cy.visit()` to target URL)

### Configuration
- [cypress.config.js](cypress.config.js) defines:
  - Browser launch behavior: Chromium/non-Electron browsers launch in incognito mode
  - Spec pattern: `cypress/e2e/**/*.cy.js` (all test files in e2e directory)

### Support Infrastructure
- [cypress/support/e2e.js](cypress/support/e2e.js) - Global setup (imports commands)
- [cypress/support/commands.js](cypress/support/commands.js) - Custom command definitions (template provided with commented examples for login, drag, dismiss patterns)
- [cypress/fixtures/example.json](cypress/fixtures/example.json) - Test data fixtures

## Developer Workflows

### Running Tests
- No npm scripts configured in root; use Cypress CLI directly:
  ```bash
  npx cypress open    # Interactive test runner
  npx cypress run     # Headless execution
  ```

### Test Writing Patterns
1. **Element Selection**: Prefer `[data-test]` attributes (documented pattern in tests)
2. **Assertions**: Chain `.should()` for verification (e.g., `cy.get('.selector').should('have.length', 2)`)
3. **Type Commands**: Use `cy.type()` with special sequences like `{enter}`, `{del}`, `{selectall}` and modifiers `{alt}`, `{ctrl}`, `{shift}`
4. **Force Option**: Use `.type(..., { force: true })` when ignoring visibility checks needed
5. **Chaining**: Leverage Cypress command chaining extensively (e.g., `.get().type().should()`)

## Project-Specific Conventions

- **Test URLs**: Tests target external example site `https://example.cypress.io/` (generic testing playground)
- **Reference Type Hints**: Tests include `/// <reference types="cypress" />` for IDE autocomplete
- **Descriptive Comments**: Tests include Cypress documentation links and explanation comments
- **No Custom Commands Yet**: commands.js is a template; customize as needed for login, navigation helpers, etc.

## Key Files to Reference
- [cypress/e2e/1-getting-started/todo.cy.js](cypress/e2e/1-getting-started/todo.cy.js) - Basic pattern reference
- [cypress/e2e/2-advanced-examples/actions.cy.js](cypress/e2e/2-advanced-examples/actions.cy.js) - DOM interaction examples
- [cypress.config.js](cypress.config.js) - Configuration & browser behavior

## When Modifying Tests
- Keep test descriptions and comments aligned with actual test logic
- Use consistent beforeEach() setup pattern across similar tests
- Add fixtures to `cypress/fixtures/` for data-driven tests
- Document complex selectors or unusual Cypress configurations inline
