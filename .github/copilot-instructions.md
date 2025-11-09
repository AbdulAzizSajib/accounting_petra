# AI Agent Instructions for ERPSA Project

## Project Overview

ERPSA is a Vue 3-based Enterprise Resource Planning System with modules for Finance, Attendance, Payroll, and User Management.

## Key Architecture Patterns

### Component Organization

- All views are in `src/views/` organized by module (finance, attendence, payroll, user-management)
- Reusable components in `src/components/`
- Main layouts in `src/components/layouts/`
- Example: See `src/views/finance/files/chart-of-accounts.vue` for component structure

### State Management

- Uses Vue 3's Composition API with store files in `src/stores/`
- Login state managed in `src/stores/login.js`
- User management in `src/stores/users.js`

### Routing & Navigation

- Route configuration in `src/router/index.js`
- Protected routes require authentication
- Modules have their own dedicated layouts (AttendenceLayout, PayrollLayout, etc.)

## Development Workflows

### Setting Up Local Dev Environment

```bash
npm install
npm run dev
```

### Project Structure Conventions

- Each module (finance, attendance, etc.) follows similar structure:
  - Base views in module root (e.g., `finance/finance-dashboard.vue`)
  - Submodules in dedicated folders (e.g., `finance/files/`, `finance/reports/`)
  - Complex features split into multiple components

### UI Component Patterns

- Uses Ant Design Vue for UI components
- Custom components wrap Ant Design with project-specific styling
- TailwindCSS for custom styling
- Example: See `src/components/popconfirm.vue` for component customization

### Data Flow Patterns

- API calls centralized in composables/services
- Form handling uses Ant Design Form components
- Data transformations handled in component setup functions

## Common Patterns & Practices

### File Naming

- Vue components use PascalCase (e.g., `PayrollLayout.vue`)
- Utilities and composables use camelCase
- Keep names descriptive and module-specific

### Component Structure

- Use Composition API with `<script setup>`
- Props defined with proper types
- Components organized by module functionality

### Error Handling

- Toast notifications via vue3-toastify for user feedback
- Consistent error message format

## Tips for AI Agents

1. Check module structure when adding features
2. Follow existing naming conventions
3. Use Ant Design Vue components for consistency
4. Leverage existing layouts for new views
5. Maintain module separation in routing and components
