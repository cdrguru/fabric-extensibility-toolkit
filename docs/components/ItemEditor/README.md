# ItemEditor Control - Detailed Documentation

The `ItemEditor` is a foundational control for building item editors in the Microsoft Fabric Extensibility Toolkit. It provides a view registration system with automatic navigation, fixed ribbon layout, and consistent UX patterns.

## 📚 Documentation Index

### Core Components

- **[ItemEditor](./README.md)** (this file) - Main container with view registration system
- **[Ribbon](./Ribbon.md)** - Ribbon container with automatic back navigation
- **[RibbonToolbar](./RibbonToolbar.md)** - Standardized toolbar actions

### View Components

- **[ItemEditorDefaultView](./ItemEditorDefaultView.md)** - Multi-panel layout with resizable splitters (left + center)
- **[ItemEditorView](./ItemEditorView.md)** - Simple single-panel layout
- **[ItemEditorEmptyView](./ItemEditorEmptyView.md)** - Empty state onboarding
- **[ItemEditorDetailView](./ItemEditorDetailView.md)** - Detail/drill-down views

### Architecture & Implementation

- **[Architecture](./Architecture.md)** - System design and patterns
- **[Implementation](./Implementation.md)** - Implementation guidelines
- **[QuickReference](./QuickReference.md)** - Quick reference guide

## 🚀 Overview

The `ItemEditor` component provides a complete view management system with automatic navigation and consistent layout:

```
┌─────────────────────────────────────┐
│  Ribbon (ViewContext-aware)         │
│  ├─ Back Button (detail views)      │
│  └─ Tabs + Actions (normal views)   │
├─────────────────────────────────────┤
│  Optional Notification Area         │
├─────────────────────────────────────┤
│                                     │
│  Dynamic View Content               │
│  ├─ Empty View                      │
│  ├─ Getting Started View            │
│  ├─ Detail Views (L2)               │
│  └─ Custom Views                    │
│                                     │
│  (scrolls independently)            │
│                                     │
└─────────────────────────────────────┘
```

### Key Benefits

✅ **View Registration** - Centralized view management with automatic switching  
✅ **ViewContext** - Automatic navigation context for ribbons  
✅ **Detail View Support** - Automatic back navigation for L2 pages  
✅ **ViewSetter Support** - Programmatic view control for data-dependent switching  
✅ **Fixed Navigation** - Ribbon stays visible during scroll  
✅ **Full Height** - Properly fills the iframe container  
✅ **Independent Scrolling** - Content scrolls without affecting ribbon  
✅ **Fabric Compliant** - Follows Microsoft Fabric design guidelines

## 🎯 ViewSetter Pattern (Recommended)

The `viewSetter` prop enables programmatic view switching based on item loading state. This pattern is **MANDATORY** for items that need to automatically switch between EMPTY and DEFAULT views based on item content.

### When to Use ViewSetter

**✅ Use viewSetter when**:

- Item starts with empty view but should switch to default view if it has content
- View selection depends on loaded item data
- You need programmatic control over view switching after async operations
- Item has different initial views based on data state

**❌ Don't use viewSetter when**:

- Views are switched only by user interaction
- Initial view can be determined statically
- No data-dependent view logic needed

### Implementation Pattern

```typescript
export function MyItemEditor(props: PageProps) {
  const [isLoading, setIsLoading] = useState(true);
  const [item, setItem] = useState<ItemWithDefinition<MyItemDefinition>>();
  const [viewSetter, setViewSetter] = useState<((view: string) => void) | null>(null);

  // Load item data
  useEffect(() => {
    async function loadItem() {
      setIsLoading(true);
      const loadedItem = await getWorkloadItem<MyItemDefinition>(workloadClient, itemObjectId);
      setItem(loadedItem);
      setIsLoading(false);
    }
    loadItem();
  }, [itemObjectId]);

  // 🚨 CRITICAL: Effect to set correct view after loading completes
  useEffect(() => {
    if (!isLoading && item && viewSetter) {
      // Business logic to determine correct view
      const correctView = !item?.definition?.message ? EDITOR_VIEW_TYPES.EMPTY : EDITOR_VIEW_TYPES.DEFAULT;
      viewSetter(correctView);
    }
  }, [isLoading, item, viewSetter]);

  return (
    <ItemEditor
      isLoading={isLoading}
      loadingMessage={t("MyItemEditor_Loading", "Loading item...")}
      views={views}
      initialView={EDITOR_VIEW_TYPES.EMPTY}  // Start with empty, effect will switch if needed
      viewSetter={(setCurrentView) => {
        // Store the setCurrentView function for later use
        if (!viewSetter) {
          setViewSetter(() => setCurrentView);
        }
      }}
      ribbon={(context) => <MyRibbon viewContext={context} />}
    />
  );
}
```

### ViewSetter Workflow

1. **ItemEditor mounts**: Calls `viewSetter` callback with its internal `setCurrentView` function
2. **Parent stores function**: Callback stores the function in component state
3. **Item loads**: Async data loading completes, `isLoading` becomes false
4. **Effect triggers**: useEffect detects loading completion and calls stored viewSetter
5. **View switches**: ItemEditor internally switches to the programmatically determined view

### ViewSetter Props

| Prop         | Type                                               | Description                                                 |
| ------------ | -------------------------------------------------- | ----------------------------------------------------------- |
| `viewSetter` | `(setCurrentView: (view: string) => void) => void` | Callback that receives ItemEditor's view switching function |

### Critical Implementation Rules

**✅ Required Implementation**:

```typescript
// 1. State to store the view setter function
const [viewSetter, setViewSetter] = useState<((view: string) => void) | null>(null);

// 2. Effect to use the stored function after loading
useEffect(() => {
  if (!isLoading && item && viewSetter) {
    const correctView = determineCorrectView(item);
    viewSetter(correctView);
  }
}, [isLoading, item, viewSetter]);

// 3. viewSetter prop implementation
viewSetter={(setCurrentView) => {
  if (!viewSetter) {
    setViewSetter(() => setCurrentView);
  }
}}
```

**❌ Common Mistakes**:

```typescript
// ❌ WRONG: Calling viewSetter immediately in the callback
viewSetter={(setCurrentView) => {
  setCurrentView(EDITOR_VIEW_TYPES.DEFAULT);  // Too early! Item not loaded yet
}}

// ❌ WRONG: Missing null check
viewSetter(correctView);  // Error if viewSetter is null

// ❌ WRONG: Missing loading check
useEffect(() => {
  if (item) {  // Missing !isLoading check
    viewSetter(correctView);
  }
}, [item, viewSetter]);
```

### Example: Empty vs Default View Logic

```typescript
// Typical business logic for view switching
useEffect(() => {
  if (!isLoading && item && viewSetter) {
    // Determine view based on item content
    const hasContent =
      item?.definition?.message && item.definition.message.trim() !== "";
    const correctView = hasContent
      ? EDITOR_VIEW_TYPES.DEFAULT
      : EDITOR_VIEW_TYPES.EMPTY;

    console.log(
      "Auto-switching to view:",
      correctView,
      "because hasContent:",
      hasContent,
    );
    viewSetter(correctView);
  }
}, [isLoading, item, viewSetter]);
```

For complete documentation and examples, see the individual component files listed above.
