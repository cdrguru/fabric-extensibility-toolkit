# ItemEditorDetailView Component

## Overview

The `ItemEditorDetailView` component provides a standardized layout for detail/drill-down views in Microsoft Fabric item editors. It offers consistent styling, proper spacing, and integrates seamlessly with the automatic back navigation system.

## ✨ Features

✅ **Automatic Back Navigation** - Works with `isDetailView: true` in view registration  
✅ **Fabric Design System** - Uses official design tokens and spacing  
✅ **Responsive Layout** - Adapts to different screen sizes  
✅ **Consistent Styling** - Standardized for detail view patterns  
✅ **Action Support** - Built-in support for detail-specific actions  
✅ **Accessibility Compliant** - Semantic HTML and ARIA support  
✅ **TypeScript Support** - Full type definitions and IntelliSense

## 🚀 Quick Start

### Basic Usage

```tsx
import { ItemEditorDetailView } from "../../components/ItemEditor";

export function MyItemDetailView({ item }: { item: MyItem }) {
  return (
    <ItemEditorDetailView
      center={{
        content: (
          <div>
            <h3>Item Details</h3>
            <p>ID: {item.id}</p>
            <p>Your detailed view content goes here.</p>
          </div>
        ),
      }}
    />
  );
}
```

### With Actions

```tsx
import { ItemEditorDetailView, DetailViewAction } from "../../components/ItemEditor";

export function MyItemDetailView({ item }: { item: MyItem }) {
export function MyItemDetailView({ item }: { item: MyItem }) {
  const toolbarActions: DetailViewAction[] = [
    {
      key: 'edit',
      label: 'Edit Item',
      icon: Edit24Regular,
      onClick: () => editItem(item.id),
      appearance: 'primary'
    },
    {
      key: 'delete',
      label: 'Delete Item',
      icon: Delete24Regular,
      onClick: () => deleteItem(item.id),
      appearance: 'subtle'
    }
  ];

  return (
    <ItemEditorDetailView
      left={{
        content: <PropertiesPanel item={item} />,
        title: "Properties"
      }}
      center={{
        content: (
          <div>
            <h4>Description</h4>
            <p>{item.description}</p>

            <h4>Properties</h4>
            <dl>
              <dt>Type:</dt>
              <dd>{item.type}</dd>
              <dt>Status:</dt>
              <dd>{item.status}</dd>
            </dl>
          </div>
        )
      }}
      toolbarActions={toolbarActions}
    />
  );
}
```

### In View Registration

```tsx
// Automatically gets back navigation with isDetailView: true
const views: RegisteredView[] = [
  {
    name: "main",
    component: <MyMainView />,
  },
  {
    name: "item-detail",
    component: (
      <ItemEditorDetailView
        left={{
          content: <PropertiesPanel />,
          title: "Properties",
        }}
        center={{
          content: <MyDetailContent />,
        }}
        toolbarActions={detailActions}
      />
    ),
    isDetailView: true, // ⭐ Enables automatic back navigation
  },
];
```

## 📖 Props API

### ItemEditorDetailViewProps

| Property         | Type                 | Required | Description                                                                       |
| ---------------- | -------------------- | -------- | --------------------------------------------------------------------------------- |
| `left`           | `LeftPanelConfig`    | ❌       | Optional left panel configuration (inherited from ItemEditorDefaultView)          |
| `center`         | `CentralPanelConfig` | ✅       | Required center content area (inherited from ItemEditorDefaultView)               |
| `toolbarActions` | `DetailViewAction[]` | ❌       | Array of detail-specific actions for the ribbon                                   |
| `onBack`         | `() => void`         | ❌       | Callback for back navigation (if not provided, uses automatic ViewContext.goBack) |
| `backLabel`      | `string`             | ❌       | Custom label for the back button                                                  |
| `backTooltip`    | `string`             | ❌       | Custom tooltip for the back button                                                |
| `className`      | `string`             | ❌       | Additional CSS classes                                                            |

### DetailViewAction Interface

| Property     | Type                                   | Required | Description                                                   |
| ------------ | -------------------------------------- | -------- | ------------------------------------------------------------- |
| `key`        | `string`                               | ✅       | Unique identifier for the action                              |
| `label`      | `string`                               | ❌       | Button text (can be omitted for translation at display level) |
| `icon`       | `React.ComponentType`                  | ✅       | Fluent UI icon component                                      |
| `onClick`    | `() => void`                           | ✅       | Click handler function                                        |
| `appearance` | `'primary' \| 'secondary' \| 'subtle'` | ❌       | Button appearance                                             |
| `disabled`   | `boolean`                              | ❌       | Whether the action is disabled                                |
| `tooltip`    | `string`                               | ❌       | Tooltip text (falls back to label if not provided)            |

## 🎯 Key Features

### Automatic Back Navigation

When registered with `isDetailView: true`, the Ribbon automatically shows a back button:

```tsx
// View registration
{
  name: 'item-detail',
  component: <ItemEditorDetailView title="Details">...</ItemEditorDetailView>,
  isDetailView: true  // ⭐ Triggers automatic back button in ribbon
}

// No manual back button needed - handled automatically by Ribbon
```

### Header Layout

Consistent header with title, subtitle, and actions:

```tsx
// Header structure
<div className="detail-view-header">
  <div className="detail-view-title-section">
    <Text variant="title1">{title}</Text>
    {subtitle && <Text variant="caption1">{subtitle}</Text>}
  </div>
  {actions && (
    <div className="detail-view-actions">
      {actions.map((action) => (
        <Tooltip key={action.key} content={action.label}>
          <Button {...actionProps} />
        </Tooltip>
      ))}
    </div>
  )}
</div>
```

### Responsive Design

Adapts to different screen sizes:

```scss
// Desktop: Side-by-side title and actions
.detail-view-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}

// Mobile: Stacked layout
@media (max-width: 768px) {
  .detail-view-header {
    flex-direction: column;
    gap: var(--spacingVerticalM);
  }
}
```

## 🏗️ Architecture

### Component Structure

```
ItemEditorDetailView
├── Header
│   ├── Title Section
│   │   ├── Title (Text variant="title1")
│   │   └── Subtitle (Text variant="caption1", optional)
│   └── Actions (optional)
│       └── Button + Tooltip (for each action)
├── Divider
└── Content
    └── Children (scrollable)
```

### CSS Classes

```scss
.base-item-editor-detail-view {
  // Main container

  &__header {
    // Header section
  }

  &__title-section {
    // Title and subtitle container
  }

  &__actions {
    // Action buttons container
  }

  &__content {
    // Content area (scrollable)
  }
}
```

## 💡 Usage Patterns

### Pattern 1: Simple Detail View

```tsx
export function UserDetailView({ userId }: { userId: string }) {
  const [user, setUser] = useState<User>();

  useEffect(() => {
    loadUser(userId).then(setUser);
  }, [userId]);

  if (!user) return <LoadingSpinner />;

  return (
    <ItemEditorDetailView
      center={{
        content: (
          <div className="user-details">
            <h2>{user.name}</h2>
            <p className="user-subtitle">
              ID: {user.id} • Last login: {user.lastLogin}
            </p>

            <section>
              <h4>Contact Information</h4>
              <p>Email: {user.email}</p>
              <p>Phone: {user.phone}</p>
            </section>

            <section>
              <h4>Permissions</h4>
              <ul>
                {user.permissions.map((p) => (
                  <li key={p}>{p}</li>
                ))}
              </ul>
            </section>
          </div>
        ),
      }}
    />
  );
}
```

### Pattern 2: Detail with Actions

```tsx
export function ProjectDetailView({ project }: { project: Project }) {
  const toolbarActions: DetailViewAction[] = [
    {
      key: "edit",
      label: "Edit Project",
      icon: Edit24Regular,
      onClick: () => editProject(project.id),
      appearance: "primary",
    },
    {
      key: "duplicate",
      label: "Duplicate",
      icon: Copy24Regular,
      onClick: () => duplicateProject(project.id),
    },
    {
      key: "archive",
      label: "Archive",
      icon: Archive24Regular,
      onClick: () => archiveProject(project.id),
      appearance: "subtle",
    },
  ];

  return (
    <ItemEditorDetailView
      left={{
        content: <ProjectMetadata project={project} />,
        title: "Project Info",
        width: 280,
      }}
      center={{
        content: (
          <div className="project-details">
            <h2>{project.name}</h2>
            <p className="project-status">
              Status: {project.status} • Owner: {project.owner}
            </p>

            <Card>
              <CardHeader>
                <Text variant="title3">Overview</Text>
              </CardHeader>
              <CardBody>
                <p>{project.description}</p>
                <div className="project-metrics">
                  <div>Tasks: {project.taskCount}</div>
                  <div>Progress: {project.progress}%</div>
                  <div>Due Date: {project.dueDate}</div>
                </div>
              </CardBody>
            </Card>

            <Card>
              <CardHeader>
                <Text variant="title3">Team Members</Text>
              </CardHeader>
              <CardBody>
                {project.members.map((member) => (
                  <div key={member.id} className="member-item">
                    <Avatar name={member.name} />
                    <div>
                      <Text weight="semibold">{member.name}</Text>
                      <Text variant="caption1">{member.role}</Text>
                    </div>
                  </div>
                ))}
              </CardBody>
            </Card>
          </div>
        ),
      }}
      toolbarActions={toolbarActions}
    />
  );
}
```

### Pattern 3: Tabbed Detail Content

```tsx
export function TabbedDetailView({ item }: { item: DetailItem }) {
  const [selectedTab, setSelectedTab] = useState("overview");

  const tabs = [
    { key: "overview", label: "Overview" },
    { key: "settings", label: "Settings" },
    { key: "history", label: "History" },
  ];

  return (
    <ItemEditorDetailView
      center={{
        content: (
          <div>
            <h2>{item.name}</h2>
            <p>{item.description}</p>

            <TabList
              selectedValue={selectedTab}
              onTabSelect={(e, data) => setSelectedTab(data.value)}
            >
              {tabs.map((tab) => (
                <Tab key={tab.key} value={tab.key}>
                  {tab.label}
                </Tab>
              ))}
            </TabList>

            <div className="tab-content">
              {selectedTab === "overview" && <OverviewContent item={item} />}
              {selectedTab === "settings" && <SettingsContent item={item} />}
              {selectedTab === "history" && <HistoryContent item={item} />}
            </div>
          </div>
        ),
      }}
    />
  );
}
```

### Pattern 4: Loading and Error States

```tsx
export function DetailViewWithStates({ itemId }: { itemId: string }) {
  const [item, setItem] = useState<Item>();
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string>();

  useEffect(() => {
    loadItem(itemId)
      .then(setItem)
      .catch((err) => setError(err.message))
      .finally(() => setLoading(false));
  }, [itemId]);

  if (loading) {
    return (
      <ItemEditorDetailView
        center={{
          content: (
            <div>
              <h2>Loading...</h2>
              <LoadingSpinner />
            </div>
          ),
        }}
      />
    );
  }

  if (error) {
    return (
      <ItemEditorDetailView
        center={{
          content: (
            <div>
              <h2>Error</h2>
              <div className="error-state">
                <Text variant="body1">
                  Failed to load item details: {error}
                </Text>
                <Button onClick={() => window.location.reload()}>Retry</Button>
              </div>
            </div>
          ),
        }}
      />
    );
  }

  return (
    <ItemEditorDetailView
      center={{
        content: <ItemContent item={item!} />,
      }}
      toolbarActions={getItemActions(item!)}
    />
  );
}
```

## ♿ Accessibility

### Semantic HTML

```tsx
// Good - uses semantic elements
<ItemEditorDetailView title="Project Details">
  <article>
    <section aria-labelledby="overview-heading">
      <h3 id="overview-heading">Project Overview</h3>
      {/* overview content */}
    </section>

    <section aria-labelledby="team-heading">
      <h3 id="team-heading">Team Members</h3>
      {/* team content */}
    </section>
  </article>
</ItemEditorDetailView>
```

### ARIA Support

- **Proper Headings**: Title uses h2, content should use h3+
- **Action Labels**: Action buttons have accessible names
- **Tooltips**: Provide additional context for actions
- **Landmarks**: Use section/article elements appropriately

## 🎨 Styling

### Design Tokens

```scss
// Header styling
.detail-view-header {
  padding: var(--spacingVerticalL) var(--spacingHorizontalXL);
  border-bottom: 1px solid var(--colorNeutralStroke2);
  background: var(--colorNeutralBackground1);
}

// Title styling
.detail-view-title {
  font-size: var(--fontSizeHero700);
  font-weight: var(--fontWeightSemibold);
  color: var(--colorNeutralForeground1);
}

// Content area
.detail-view-content {
  padding: var(--spacingVerticalL) var(--spacingHorizontalXL);
  overflow-y: auto;
}
```

## 🔧 Best Practices

### ✅ Do's

✅ **Use descriptive titles** - Clear identification of what's being viewed  
✅ **Provide relevant actions** - Edit, delete, duplicate, etc.  
✅ **Use proper heading hierarchy** - h2 for title, h3+ for content  
✅ **Test back navigation** - Ensure smooth navigation flow  
✅ **Include loading states** - Handle async data properly  
✅ **Use card layouts** for complex content organization

### ❌ Don'ts

❌ **Don't manually add back buttons** - Use `isDetailView: true` instead  
❌ **Don't overcrowd actions** - Limit to 3-5 most important actions  
❌ **Don't forget loading states** - Always handle async operations  
❌ **Don't use nested scrolling** - Let ItemEditorDetailView handle scroll  
❌ **Don't ignore responsive design** - Test on different screen sizes

## 🔗 Related Components

- **[ItemEditor](./README.md)** - Main container with view registration
- **[ItemEditorView](./ItemEditorView.md)** - Default view layout
- **[ItemEditorEmptyView](./ItemEditorEmptyView.md)** - Empty state layout
- **[Ribbon](./Ribbon.md)** - Ribbon container with automatic back navigation

## 📝 Examples

For complete examples, see:

- [ItemEditor README](./README.md) - Integration patterns with detail views
- View registration examples in the main documentation
