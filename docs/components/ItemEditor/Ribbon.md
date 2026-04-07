# Ribbon Component

## 📋 Overview

The `Ribbon` component provides a consistent ribbon interface for Microsoft Fabric item editors with a clean, streamlined API. It automatically integrates with the ViewContext system and provides a mandatory Home tab with optional additional tabs.

## ✨ Features

✅ **Clean API** - Simple `homeToolbarActions` + optional `additionalToolbars` pattern  
✅ **Mandatory Home Tab** - Ensures consistent navigation across all items  
✅ **Standard Action Factories** - Built-in `createSaveAction()`, `createSettingsAction()` helpers  
✅ **ViewContext Integration** - Automatic back button handling for detail views  
✅ **Fabric Design System** - Uses official tokens and styling  
✅ **Accessibility Compliant** - ARIA labels, keyboard navigation, screen reader support  
✅ **TypeScript Support** - Full type definitions and IntelliSense

## 🚀 Quick Start

### Simple Pattern (Recommended)

```tsx
import {
  Ribbon,
  createSaveAction,
  createSettingsAction,
} from "../../components/ItemEditor";
import { ViewContext } from "../../components/ItemEditor";

export function MyItemRibbon({
  viewContext,
  saveItemCallback,
  openSettingsCallback,
  isSaveButtonEnabled,
}) {
  // Define mandatory Home tab actions
  const homeToolbarActions: RibbonAction[] = [
    createSaveAction(saveItemCallback, !isSaveButtonEnabled),
    createSettingsAction(openSettingsCallback),
  ];

  return (
    <Ribbon homeToolbarActions={homeToolbarActions} viewContext={viewContext} />
  );
}
```

### Complex Pattern with Additional Tabs

```tsx
export function ComplexItemRibbon({ viewContext, ...callbacks }) {
  const { t } = useTranslation();

  // Mandatory Home tab actions
  const homeToolbarActions: RibbonAction[] = [
    createSaveAction(callbacks.save, !props.canSave),
    createSettingsAction(callbacks.settings),
  ];

  // Optional additional tabs for complex functionality
  const additionalToolbars = [
    {
      key: "data",
      label: t("Data"),
      actions: [
        {
          key: "import",
          icon: CloudArrowDown24Regular,
          label: t("Import"),
          onClick: callbacks.import,
        },
      ],
    },
  ];

  return (
    <Ribbon
      homeToolbarActions={homeToolbarActions}
      additionalToolbars={additionalToolbars}
      viewContext={viewContext}
    />
  );
}
```

## 📖 Props API

### RibbonProps

| Property             | Type              | Required | Description                                |
| -------------------- | ----------------- | -------- | ------------------------------------------ |
| `homeToolbarActions` | `RibbonAction[]`  | ✅       | Actions for the mandatory Home tab         |
| `additionalToolbars` | `RibbonToolbar[]` | ❌       | Optional additional tabs for complex items |
| `viewContext`        | `ViewContext`     | ✅       | ViewContext for navigation and back button |

### RibbonAction Interface

| Property   | Type                          | Required | Description                      |
| ---------- | ----------------------------- | -------- | -------------------------------- |
| `key`      | `string`                      | ✅       | Unique identifier for the action |
| `icon`     | `FluentIconComponent`         | ✅       | Fluent UI icon component         |
| `label`    | `string`                      | ✅       | Action label and tooltip text    |
| `onClick`  | `() => void \| Promise<void>` | ✅       | Click handler                    |
| `disabled` | `boolean`                     | ❌       | Whether action is disabled       |
| `testId`   | `string`                      | ❌       | Test identifier                  |

### RibbonToolbar Interface

| Property  | Type             | Required | Description                   |
| --------- | ---------------- | -------- | ----------------------------- |
| `key`     | `string`         | ✅       | Unique identifier for the tab |
| `label`   | `string`         | ✅       | Tab display label             |
| `actions` | `RibbonAction[]` | ✅       | Actions for this tab          |

### ViewContext Interface

| Property         | Type                     | Description                           |
| ---------------- | ------------------------ | ------------------------------------- |
| `currentView`    | `string`                 | Name of currently active view         |
| `setCurrentView` | `(view: string) => void` | Navigate to different view            |
| `isDetailView`   | `boolean`                | True if current view is a detail view |
| `goBack`         | `() => void`             | Navigate to previous view             |
| `viewHistory`    | `string[]`               | Stack of previous views               |

## 🎯 Key Features

### Automatic Back Navigation

When `viewContext.isDetailView` is true, Ribbon automatically shows a back button:

```tsx
// Detail view - back button appears automatically
{
  name: 'details',
  component: <ItemDetailsView />,
  isDetailView: true  // ⭐ This triggers the back button
}

// Ribbon automatically handles the back navigation
<Ribbon viewContext={viewContext}>
  <MyContent />  {/* Back button appears automatically */}
</Ribbon>
```

### Integration with ItemEditor

Perfect integration with the ItemEditor view registration system:

```tsx
// In your item editor
<ItemEditor
  views={views}
  defaultView="main"
  ribbon={(viewContext) => <MyItemRibbon viewContext={viewContext} />}
/>;

// Your ribbon component
export function MyItemRibbon({ viewContext }) {
  return <Ribbon viewContext={viewContext}>{/* Ribbon content */}</Ribbon>;
}
```

## 🏗️ Architecture

### Component Hierarchy

```
Ribbon
├── Back Button (conditional)
│   └── ToolbarButton with "Back" tooltip
└── Children Content
    ├── RibbonToolbar (optional)
    ├── Tabs (optional)
    └── Custom Content
```

### CSS Classes

```scss
.base-ribbon {
  // Main ribbon container
  &__back-button {
    // Back button styling
  }
  &__content {
    // Content area
  }
}
```

## 💡 Usage Patterns

### Pattern 1: Simple Ribbon (Recommended)

Most items only need the Home tab with Save and Settings actions:

```tsx
export function SimpleRibbon({
  viewContext,
  saveCallback,
  settingsCallback,
  canSave,
}) {
  const { t } = useTranslation();

  // Create a translation helper function
  const translate = (key: string, fallback?: string) => t(key, fallback);

  const homeToolbarActions = [
    createSaveAction(saveCallback, !canSave, translate),
    createSettingsAction(settingsCallback, translate),
  ];

  return (
    <Ribbon homeToolbarActions={homeToolbarActions} viewContext={viewContext} />
  );
}
```

### Pattern 2: Complex Ribbon with Additional Tabs

For items with complex functionality that need multiple tabs:

```tsx
export function ComplexRibbon({ viewContext, ...callbacks }) {
  const { t } = useTranslation();

  // Create a translation helper function
  const translate = (key: string, fallback?: string) => t(key, fallback);

  const homeToolbarActions = [
    createSaveAction(callbacks.save, !callbacks.canSave, translate),
    createSettingsAction(callbacks.settings, translate),
  ];

  const additionalToolbars = [
    {
      key: "data",
      label: t("Data"),
      actions: [
        {
          key: "import",
          icon: CloudArrowDown24Regular,
          label: t("Import Data"),
          onClick: callbacks.import,
        },
        {
          key: "export",
          icon: Share24Regular,
          label: t("Export Data"),
          onClick: callbacks.export,
        },
      ],
    },
    {
      key: "view",
      label: t("View"),
      actions: [
        {
          key: "grid",
          icon: Grid24Regular,
          label: t("Grid View"),
          onClick: callbacks.toggleGrid,
        },
      ],
    },
  ];

  return (
    <Ribbon
      homeToolbarActions={homeToolbarActions}
      additionalToolbars={additionalToolbars}
      viewContext={viewContext}
    />
  );
}
```

### Pattern 3: Custom Actions in Home Tab

Adding custom actions to the Home tab alongside standard ones:

```tsx
export function CustomActionsRibbon({ viewContext, ...callbacks }) {
  const { t } = useTranslation();

  // Create a translation helper function
  const translate = (key: string, fallback?: string) => t(key, fallback);

  const homeToolbarActions = [
    // Standard actions first
    createSaveAction(callbacks.save, !callbacks.canSave, translate),
    createSettingsAction(callbacks.settings, translate),

    // Custom actions
    {
      key: "refresh",
      icon: ArrowClockwise24Regular,
      label: t("Refresh"),
      onClick: callbacks.refresh,
    },
    {
      key: "help",
      icon: QuestionCircle24Regular,
      label: t("Help"),
      onClick: callbacks.showHelp,
    },
  ];

  return (
    <Ribbon homeToolbarActions={homeToolbarActions} viewContext={viewContext} />
  );
}
```

## ♿ Accessibility

- **ARIA Labels**: Back button has proper aria-label
- **Keyboard Navigation**: Full keyboard support with Tab/Enter/Space
- **Screen Reader**: Announces navigation changes
- **Focus Management**: Proper focus handling for back button

## 🎨 Styling

### Design Tokens

Uses Fabric design tokens for consistency:

```scss
// Background
background: var(--colorNeutralBackground1);

// Border
border-bottom: 1px solid var(--colorNeutralStroke2);

// Padding
padding: var(--spacingHorizontalM) var(--spacingHorizontalL);
```

### Custom Styling

```tsx
<Ribbon viewContext={viewContext} className="my-custom-ribbon">
  <MyContent />
</Ribbon>
```

## 🔧 Best Practices

### ✅ Do's

✅ **Always define homeToolbarActions** - Every ribbon needs a mandatory Home tab  
✅ **Use standard action factories** - `createSaveAction()`, `createSettingsAction()` for consistency  
✅ **Start with simple pattern** - Only add `additionalToolbars` when truly needed  
✅ **Import proper Fluent icons** - Use `import { Icon24Regular } from '@fluentui/react-icons'`  
✅ **Test keyboard navigation** for all interactive elements  
✅ **Use descriptive action keys** - Unique identifiers help with testing and debugging

### ❌ Don'ts

❌ **Don't skip homeToolbarActions** - This parameter is mandatory and ensures consistent UX  
❌ **Don't create unnecessary tabs** - Keep ribbons simple unless complexity is justified  
❌ **Don't manage back button manually** - ViewContext handles it automatically  
❌ **Don't use old RibbonToolbar** - The new API eliminates the need for child components  
❌ **Don't ignore accessibility** - Always include proper labels and test with screen readers

## 🔀 Migration from Old API

### Before (Complex)

```tsx
// Old complex pattern - DON'T USE
const tabs = createRibbonTabs(/* complex config */);
return (
  <Ribbon tabs={tabs} viewContext={viewContext}>
    <RibbonToolbar actions={actions} />
  </Ribbon>
);
```

### After (Clean)

```tsx
// New clean pattern - RECOMMENDED
const { t } = useTranslation();
const translate = (key: string, fallback?: string) => t(key, fallback);

const homeToolbarActions = [
  createSaveAction(save, !canSave, translate),
  createSettingsAction(settings, translate),
];

return (
  <Ribbon homeToolbarActions={homeToolbarActions} viewContext={viewContext} />
);
```

## 🔗 Related Components

- **[ItemEditor](./README.md)** - Main container with view registration
- **[RibbonToolbar](./RibbonToolbar.md)** - Standardized toolbar actions
- **[ItemEditorView](./ItemEditorView.md)** - Default view layout
- **[ItemEditorDetailView](./ItemEditorDetailView.md)** - Detail view layout

## 📝 Examples

For complete examples, see:

- [Sample Ribbon Implementation](../../Workload/app/items/HelloWorldItem/HelloWorldItemRibbon.tsx) - HelloWorld reference
- [ItemEditor README](./README.md) - Integration patterns
