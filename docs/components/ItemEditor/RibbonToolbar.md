# RibbonToolbar Component

## 📋 Overview

The `RibbonToolbar` component provides a standardized toolbar interface for ribbon actions in Microsoft Fabric item editors. It automatically handles action layout, theming, accessibility, and integrates seamlessly with the Ribbon component.

## ✨ Features

✅ **Standardized Actions** - Consistent button styling and behavior  
✅ **Fabric Design System** - Uses official tokens and Fluent UI components  
✅ **Accessibility Compliant** - ARIA labels, keyboard navigation, screen reader support  
✅ **Flexible Actions** - Supports primary, secondary, and grouped actions  
✅ **Icon Integration** - Fluent UI icon support with tooltips  
✅ **Responsive Design** - Adapts to different screen sizes  
✅ **TypeScript Support** - Full type definitions and IntelliSense

## 🚀 Quick Start

### Basic Usage

```tsx
import { RibbonToolbar, RibbonAction } from "../../components/ItemEditor";

export function MyToolbar() {
  const actions: RibbonAction[] = [
    {
      key: "save",
      label: "Save",
      iconName: "Save",
      onClick: handleSave,
      appearance: "primary",
    },
    {
      key: "refresh",
      label: "Refresh",
      iconName: "Refresh",
      onClick: handleRefresh,
    },
  ];

  return <RibbonToolbar actions={actions} />;
}
```

### In Ribbon

```tsx
import { Ribbon, RibbonToolbar } from "../../components/ItemEditor";

export function MyItemRibbon({ viewContext }) {
  const actions: RibbonAction[] = [
    {
      key: "save",
      label: "Save Item",
      iconName: "Save",
      onClick: handleSave,
      appearance: "primary",
      disabled: !isDirty,
    },
  ];

  return (
    <Ribbon viewContext={viewContext}>
      <RibbonToolbar actions={actions} />
    </Ribbon>
  );
}
```

## 📖 Props API

### RibbonToolbarProps

| Property    | Type             | Required | Description              |
| ----------- | ---------------- | -------- | ------------------------ |
| `actions`   | `RibbonAction[]` | ✅       | Array of toolbar actions |
| `className` | `string`         | ❌       | Additional CSS classes   |

### RibbonAction Interface

| Property     | Type                                   | Required | Description                              |
| ------------ | -------------------------------------- | -------- | ---------------------------------------- |
| `key`        | `string`                               | ✅       | Unique identifier for the action         |
| `label`      | `string`                               | ✅       | Button text and tooltip                  |
| `iconName`   | `string`                               | ❌       | Fluent UI icon name                      |
| `onClick`    | `() => void`                           | ✅       | Click handler function                   |
| `appearance` | `'primary' \| 'secondary' \| 'subtle'` | ❌       | Button appearance (default: 'secondary') |
| `disabled`   | `boolean`                              | ❌       | Whether the action is disabled           |
| `hidden`     | `boolean`                              | ❌       | Whether to hide the action               |
| `ariaLabel`  | `string`                               | ❌       | Custom aria-label (defaults to label)    |

## 🎯 Action Types

### Primary Actions

Used for the most important action (usually Save):

```tsx
{
  key: 'save',
  label: 'Save',
  iconName: 'Save',
  onClick: handleSave,
  appearance: 'primary'  // Blue background
}
```

### Secondary Actions

Standard actions with normal styling:

```tsx
{
  key: 'refresh',
  label: 'Refresh',
  iconName: 'Refresh',
  onClick: handleRefresh,
  appearance: 'secondary'  // Default appearance
}
```

### Subtle Actions

Less prominent actions:

```tsx
{
  key: 'settings',
  label: 'Settings',
  iconName: 'Settings',
  onClick: handleSettings,
  appearance: 'subtle'  // Minimal styling
}
```

## 🏗️ Architecture

### Component Structure

```
RibbonToolbar
├── Toolbar (Fluent UI)
│   └── ToolbarGroup
│       ├── Tooltip + ToolbarButton (for each action)
│       └── Icon (optional)
```

### CSS Classes

```scss
.base-ribbon-toolbar {
  // Main toolbar container

  .fui-Toolbar {
    // Fluent UI toolbar styling
  }

  .fui-ToolbarButton {
    // Individual button styling
  }
}
```

## 💡 Usage Patterns

### Pattern 1: Save/Cancel Actions

```tsx
const actions: RibbonAction[] = [
  {
    key: "save",
    label: "Save",
    iconName: "Save",
    onClick: handleSave,
    appearance: "primary",
    disabled: !isDirty,
  },
  {
    key: "cancel",
    label: "Cancel",
    iconName: "Cancel",
    onClick: handleCancel,
    appearance: "subtle",
  },
];
```

### Pattern 2: Conditional Actions

```tsx
const actions: RibbonAction[] = [
  {
    key: "save",
    label: "Save",
    iconName: "Save",
    onClick: handleSave,
    appearance: "primary",
  },
  {
    key: "publish",
    label: "Publish",
    iconName: "Share",
    onClick: handlePublish,
    hidden: !canPublish, // Hide when not available
  },
];
```

### Pattern 3: Loading States

```tsx
const actions: RibbonAction[] = [
  {
    key: "save",
    label: isLoading ? "Saving..." : "Save",
    iconName: "Save",
    onClick: handleSave,
    disabled: isLoading,
  },
];
```

### Pattern 4: View-Specific Actions

```tsx
export function MyItemRibbon({ viewContext }) {
  const getActions = (): RibbonAction[] => {
    const baseActions = [
      { key: "save", label: "Save", iconName: "Save", onClick: handleSave },
    ];

    if (viewContext.currentView === "details") {
      return [
        ...baseActions,
        { key: "edit", label: "Edit", iconName: "Edit", onClick: handleEdit },
      ];
    }

    return baseActions;
  };

  return (
    <Ribbon viewContext={viewContext}>
      <RibbonToolbar actions={getActions()} />
    </Ribbon>
  );
}
```

## 🎨 Icons

### Common Fluent UI Icons

```tsx
// Standard actions
"Save"; // Save action
"Refresh"; // Refresh/reload
"Settings"; // Settings/preferences
"Add"; // Create new
"Delete"; // Delete/remove
"Edit"; // Edit mode
"Share"; // Publish/share
"Download"; // Export/download
"Upload"; // Import/upload
"Search"; // Search/filter

// Navigation
"ChevronLeft"; // Back navigation
"ChevronRight"; // Forward navigation
"Home"; // Home/dashboard

// Status
"CheckMark"; // Success/complete
"ErrorBadge"; // Error/warning
"Info"; // Information
```

### Custom Icons

```tsx
import { bundleIcon, Filled, Regular } from "@fluentui/react-icons";

const MyCustomIcon = bundleIcon(MyIconFilled, MyIconRegular);

const action: RibbonAction = {
  key: "custom",
  label: "Custom Action",
  iconName: "MyCustomIcon", // Use your bundled icon
  onClick: handleCustom,
};
```

## ♿ Accessibility

### ARIA Support

- **Toolbar Role**: Proper ARIA toolbar role
- **Button Labels**: Each action has accessible name
- **Tooltips**: Provide additional context
- **Disabled State**: Properly announced to screen readers

### Keyboard Navigation

- **Tab**: Navigate between actions
- **Enter/Space**: Activate actions
- **Arrow Keys**: Navigate within toolbar (Fluent UI default)

### Example with Custom ARIA

```tsx
{
  key: 'save',
  label: 'Save Document',
  iconName: 'Save',
  onClick: handleSave,
  ariaLabel: 'Save the current document. Shortcut: Ctrl+S'
}
```

## 🔧 Best Practices

### ✅ Do's

✅ **Use descriptive labels** - Clear, concise action names  
✅ **Provide tooltips** - Labels serve as tooltips automatically  
✅ **Use standard icons** - Leverage Fluent UI icon library  
✅ **Handle disabled states** - Show when actions aren't available  
✅ **Keep actions minimal** - 3-5 primary actions maximum  
✅ **Test keyboard navigation** - Ensure full accessibility

### ❌ Don'ts

❌ **Don't use vague labels** - Avoid "OK", "Submit", etc.  
❌ **Don't overcrowd toolbar** - Too many actions reduce usability  
❌ **Don't forget loading states** - Show feedback during operations  
❌ **Don't ignore accessibility** - Always test with screen readers  
❌ **Don't use custom styling** - Stick to appearance props

## 🎯 Integration Examples

### With ItemEditor

```tsx
// Complete integration example
export function MyItemEditor(props: PageProps) {
  const views: RegisteredView[] = [
    {
      name: "main",
      component: <MyMainView />,
    },
  ];

  return (
    <ItemEditor
      views={views}
      defaultView="main"
      ribbon={(viewContext) => <MyItemRibbon viewContext={viewContext} />}
    />
  );
}

export function MyItemRibbon({ viewContext }) {
  const actions: RibbonAction[] = [
    {
      key: "save",
      label: "Save Item",
      iconName: "Save",
      onClick: () => saveItem(),
      appearance: "primary",
    },
  ];

  return (
    <Ribbon viewContext={viewContext}>
      <RibbonToolbar actions={actions} />
    </Ribbon>
  );
}
```

## 🔗 Related Components

- **[Ribbon](./Ribbon.md)** - Ribbon container component
- **[ItemEditor](./README.md)** - Main editor container
- **[ItemEditorView](./ItemEditorView.md)** - Default view layout
- **[ItemEditorDetailView](./ItemEditorDetailView.md)** - Detail view layout

## 📝 Examples

For complete examples, see:

- [HelloWorldItemRibbon.tsx](../../Workload/app/items/HelloWorldItem/HelloWorldItemRibbon.tsx) - Reference implementation
- [StandardRibbonActions.ts](../../Workload/app/components/ItemEditor/StandardRibbonActions.ts) - Standard action patterns
