# Wizard Control

## 📋 Overview

The `WizardControl` component provides a step-by-step interface for guiding users through multi-stage processes. It features a visual progress indicator, step navigation, and content areas following Fabric UX System guidelines.

## ✨ Features

✅ **Step Progress Tracking** - Visual indicators for completed, current, and upcoming steps  
✅ **Interactive Navigation** - Click to navigate to completed or current steps  
✅ **Flexible Content Areas** - Customizable content panel and optional footer  
✅ **Responsive Design** - Adapts to different screen sizes  
✅ **Accessibility Compliant** - Semantic HTML structure and keyboard support  
✅ **Fabric Design System** - Uses official design tokens and spacing  
✅ **TypeScript Support** - Full type definitions and IntelliSense

## 🏗️ Architecture

### Layout Structure

```
┌─────────────────────────────────────────────────────┐
│  WizardControl                                       │
│  ┌─────────────┬─────────────────────────────────┐  │
│  │ Steps Panel │        Content Panel            │  │
│  │ ┌─────────┐ │                                 │  │
│  │ │ ● 1     │ │                                 │  │
│  │ │ Step 1  │ │        Dynamic Content          │  │
│  │ │ Done    │ │        (children prop)          │  │
│  │ ├─────────┤ │                                 │  │
│  │ │ ● 2     │ │                                 │  │
│  │ │ Step 2  │ │                                 │  │
│  │ │ Current │ │                                 │  │
│  │ ├─────────┤ │                                 │  │
│  │ │ ○ 3     │ │                                 │  │
│  │ │ Step 3  │ │                                 │  │
│  │ │ Pending │ │                                 │  │
│  │ └─────────┘ │                                 │  │
│  └─────────────┴─────────────────────────────────┘  │
│  ┌─────────────────────────────────────────────────┐  │
│  │              Footer (optional)                   │  │
│  └─────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────┘
```

### Step States

- **Completed**: Green circle with checkmark, clickable
- **Current**: Green circle with number, clickable
- **Upcoming**: Gray circle with number, not clickable

## 🚀 Quick Start

### Enhanced Usage with View Registration

```tsx
import { WizardControl } from "../../components/Wizard";

// Step components
function SetupStep({ context, onNext }: WizardStepProps) {
  return (
    <div>
      <h2>Setup Information</h2>
      <TextField
        label="Name"
        value={context.name || ""}
        onChange={(_, data) => (context.name = data.value)}
      />
    </div>
  );
}

function ConfigStep({ context }: WizardStepProps) {
  return (
    <div>
      <h2>Configuration</h2>
      <Checkbox
        label="Enable notifications"
        checked={context.notifications || false}
        onChange={(_, data) => (context.notifications = data.checked)}
      />
    </div>
  );
}

// Steps with view registration
const steps = [
  {
    id: "setup",
    title: "Setup",
    description: "Basic information",
    component: SetupStep,
    validate: async (context) => !!context.name,
  },
  {
    id: "config",
    title: "Configure",
    description: "Settings and preferences",
    component: ConfigStep,
  },
  {
    id: "review",
    title: "Review",
    description: "Confirm your choices",
    component: ({ context }) => (
      <div>
        <h2>Review</h2>
        <p>Name: {context.name}</p>
        <p>Notifications: {context.notifications ? "Enabled" : "Disabled"}</p>
      </div>
    ),
  },
];

function MyWizard() {
  return (
    <WizardControl
      steps={steps}
      initialStepId="setup"
      showNavigation={true}
      canFinish={(stepId, context) => {
        // Only allow finishing if name is provided
        return !!context.name;
      }}
      onComplete={(context) => {
        console.log("Wizard completed with:", context);
      }}
      onCancel={() => {
        console.log("Wizard cancelled");
      }}
    />
  );
}
```

### Basic Usage (Legacy Pattern)

```tsx
import { WizardControl } from "../../components/Wizard";

const steps = [
  {
    id: "step1",
    title: "Get Started",
    description: "Set up your basic information",
  },
  {
    id: "step2",
    title: "Configure",
    description: "Configure your settings",
  },
  {
    id: "step3",
    title: "Review",
    description: "Review and confirm",
  },
];

function MyWizard() {
  const [currentStep, setCurrentStep] = useState("step1");

  return (
    <WizardControl
      steps={steps}
      currentStepId={currentStep}
      onStepChange={setCurrentStep}
    >
      {/* Your step content here */}
      <div>
        <h2>Current Step Content</h2>
        <p>This content changes based on the current step.</p>
      </div>
    </WizardControl>
  );
}
```

### With Footer

```tsx
<WizardControl
  steps={steps}
  currentStepId={currentStep}
  onStepChange={setCurrentStep}
  footer={
    <div
      style={{
        padding: "16px",
        display: "flex",
        justifyContent: "space-between",
      }}
    >
      <Button appearance="secondary" onClick={handlePrevious}>
        Previous
      </Button>
      <Button appearance="primary" onClick={handleNext}>
        Next
      </Button>
    </div>
  }
>
  {stepContent}
</WizardControl>
```

### Complete Example with Step Management

```tsx
import React, { useState } from "react";
import { WizardControl, WizardStep } from "../../components/Wizard";
import { Button } from "@fluentui/react-components";

function CompleteWizardExample() {
  const [currentStepId, setCurrentStepId] = useState("basics");

  const steps: WizardStep[] = [
    {
      id: "basics",
      title: "Basic Information",
      description: "Enter your name and email",
    },
    {
      id: "preferences",
      title: "Preferences",
      description: "Set your preferences",
    },
    {
      id: "review",
      title: "Review & Submit",
      description: "Review your information",
    },
  ];

  const currentIndex = steps.findIndex((s) => s.id === currentStepId);

  const handleNext = () => {
    if (currentIndex < steps.length - 1) {
      setCurrentStepId(steps[currentIndex + 1].id);
    }
  };

  const handlePrevious = () => {
    if (currentIndex > 0) {
      setCurrentStepId(steps[currentIndex - 1].id);
    }
  };

  const renderStepContent = () => {
    switch (currentStepId) {
      case "basics":
        return (
          <div>
            <h2>Basic Information</h2>
            {/* Your form fields */}
          </div>
        );
      case "preferences":
        return (
          <div>
            <h2>Preferences</h2>
            {/* Your preference settings */}
          </div>
        );
      case "review":
        return (
          <div>
            <h2>Review & Submit</h2>
            {/* Your review content */}
          </div>
        );
      default:
        return null;
    }
  };

  return (
    <WizardControl
      steps={steps}
      currentStepId={currentStepId}
      onStepChange={setCurrentStepId}
      footer={
        <div
          style={{
            padding: "16px 20px",
            display: "flex",
            justifyContent: "space-between",
            alignItems: "center",
          }}
        >
          <Button
            appearance="secondary"
            onClick={handlePrevious}
            disabled={currentIndex === 0}
          >
            Previous
          </Button>

          <span>
            {currentIndex + 1} of {steps.length}
          </span>

          <Button
            appearance="primary"
            onClick={handleNext}
            disabled={currentIndex === steps.length - 1}
          >
            {currentIndex === steps.length - 1 ? "Submit" : "Next"}
          </Button>
        </div>
      }
    >
      {renderStepContent()}
    </WizardControl>
  );
}
```

## 📝 API Reference

### WizardControl Props

| **Prop**              | **Type**                                                    | **Required** | **Default**   | **Description**                                          |
| --------------------- | ----------------------------------------------------------- | ------------ | ------------- | -------------------------------------------------------- |
| `steps`               | `WizardStep[]`                                              | ✅           | -             | Array of step configurations with view registration      |
| `initialStepId`       | `string`                                                    | ❌           | `steps[0].id` | ID of the initially active step (defaults to first step) |
| `onStepChange`        | `(stepId: string, stepIndex: number) => void`               | ❌           | `undefined`   | Callback fired when step changes                         |
| `onComplete`          | `(context: Record<string, any>) => void`                    | ❌           | `undefined`   | Callback fired when wizard is completed                  |
| `onCancel`            | `() => void`                                                | ❌           | `undefined`   | Callback fired when wizard is cancelled                  |
| `canFinish`           | `(stepId: string, context: Record<string, any>) => boolean` | ❌           | `() => true`  | Function to determine if wizard can be finished          |
| `initialContext`      | `Record<string, any>`                                       | ❌           | `{}`          | Initial wizard context data                              |
| `showNavigation`      | `boolean`                                                   | ❌           | `true`        | Whether to show built-in navigation footer               |
| `className`           | `string`                                                    | ❌           | `""`          | Additional CSS classes                                   |
| `allowStepNavigation` | `boolean`                                                   | ❌           | `true`        | Whether to allow clicking on previous steps              |
| `navigationLabels`    | `NavigationLabels`                                          | ❌           | `{}`          | Custom labels for navigation buttons                     |

### WizardStep Interface

| **Property**  | **Type**                                                        | **Required** | **Description**                             |
| ------------- | --------------------------------------------------------------- | ------------ | ------------------------------------------- |
| `id`          | `string`                                                        | ✅           | Unique identifier for the step              |
| `title`       | `string`                                                        | ✅           | Display title for the step                  |
| `description` | `string`                                                        | ❌           | Optional description text                   |
| `component`   | `React.ComponentType<WizardStepProps>`                          | ✅           | React component to render for this step     |
| `validate`    | `(context: Record<string, any>) => boolean \| Promise<boolean>` | ❌           | Function to validate step before proceeding |
| `onEnter`     | `(context: Record<string, any>) => void \| Promise<void>`       | ❌           | Callback when entering this step            |
| `onExit`      | `(context: Record<string, any>) => void \| Promise<void>`       | ❌           | Callback when leaving this step             |

### WizardStepProps Interface

| **Property**  | **Type**                   | **Description**                   |
| ------------- | -------------------------- | --------------------------------- |
| `stepId`      | `string`                   | Current step identifier           |
| `stepIndex`   | `number`                   | Current step index (0-based)      |
| `context`     | `Record<string, any>`      | Shared wizard context data        |
| `onNext`      | `() => Promise<void>`      | Function to advance to next step  |
| `onPrevious`  | `() => void`               | Function to go to previous step   |
| `onGoToStep`  | `(stepId: string) => void` | Function to jump to specific step |
| `isFirstStep` | `boolean`                  | Whether this is the first step    |
| `isLastStep`  | `boolean`                  | Whether this is the last step     |

### WizardNavigationProps Interface

| **Property**       | **Type**                  | **Description**                 |
| ------------------ | ------------------------- | ------------------------------- |
| `canFinish`        | `boolean`                 | Whether wizard can be completed |
| `onPrevious`       | `() => void`              | Function to go to previous step |
| `onNext`           | `() => Promise<void>`     | Function to go to next step     |
| `onComplete`       | `() => void`              | Function to complete wizard     |
| `onCancel`         | `() => void \| undefined` | Function to cancel wizard       |
| `labels`           | `NavigationLabels`        | Button labels                   |
| `currentStepIndex` | `number`                  | Current step index (derived)    |
| `totalSteps`       | `number`                  | Total number of steps (derived) |

## 🎨 Styling

### CSS Classes

The Wizard uses BEM naming convention with the `wizard-control` prefix:

```scss
.wizard-control                      // Main container
.wizard-control__main               // Main content area
.wizard-control__steps-panel        // Left steps navigation panel
.wizard-control__step               // Individual step container
.wizard-control__step--clickable    // Clickable step state
.wizard-control__step--disabled     // Disabled step state
.wizard-control__step-circle        // Step indicator circle
.wizard-control__step-circle--completed  // Completed step circle
.wizard-control__step-circle--current    // Current step circle
.wizard-control__step-circle--upcoming   // Upcoming step circle
.wizard-control__step-checkmark     // Checkmark for completed steps
.wizard-control__step-number        // Step number text
.wizard-control__step-content       // Step title/description area
.wizard-control__step-title         // Step title text
.wizard-control__step-title--current     // Current step title
.wizard-control__step-title--normal      // Normal step title
.wizard-control__step-description   // Step description text
.wizard-control__step-connector     // Connecting line between steps
.wizard-control__step-connector--completed   // Completed connector
.wizard-control__step-connector--upcoming    // Upcoming connector
.wizard-control__content-panel      // Right content panel
.wizard-control__footer             // Footer area
```

### Design Tokens Used

```scss
// Colors
--colorPaletteGreenForeground1: #018574 // Completed/current steps
  --colorNeutralBackground2: #fafafa // Steps panel background
  --colorNeutralBackground1: white // Content panel background
  --colorNeutralStroke2: #e1dfdd // Borders and upcoming steps
  --colorNeutralForeground1: #323130 // Primary text
  --colorNeutralForeground3: #616161 // Secondary text
  // Spacing
  --spacingVerticalL: 20px // Panel padding
  --spacingVerticalM: 12px // Mobile padding
  --spacingHorizontalS: 12px // Step gap
  --spacingHorizontalXS: 8px // Mobile step gap
  // Typography
  --fontWeightSemibold // Current step emphasis
  --fontWeightMedium; // Normal step weight
```

### Custom Styling

```tsx
// Add custom CSS class
<WizardControl
  className="my-custom-wizard"
  steps={steps}
  currentStepId={currentStep}
>
  {content}
</WizardControl>
```

```scss
// Override styles
.my-custom-wizard {
  .wizard-control__steps-panel {
    width: 200px; // Wider steps panel
    background-color: #f0f0f0;
  }

  .wizard-control__step-circle--current {
    background-color: #0078d4; // Custom brand color
  }
}
```

## 🔧 Best Practices

### ✅ Do's

✅ **Use descriptive step titles** - Clear, action-oriented step names  
✅ **Provide step descriptions** - Help users understand what each step involves  
✅ **Keep steps focused** - Each step should have a single, clear purpose  
✅ **Handle step validation** - Validate current step before allowing navigation  
✅ **Provide navigation components** - Include Previous/Next buttons in footer  
✅ **Show progress indication** - Let users know how many steps remain  
✅ **Test responsive behavior** - Ensure wizard works on mobile devices  
✅ **Save user progress** - Persist step data to prevent loss on navigation

### ❌ Don'ts

❌ **Don't skip step validation** - Always validate before moving to next step  
❌ **Don't use too many steps** - Keep wizards focused (3-5 steps ideal)  
❌ **Don't hide navigation options** - Always provide way to go back  
❌ **Don't overwhelm users** - Break complex forms into logical steps  
❌ **Don't ignore mobile experience** - Test on small screens  
❌ **Don't force linear progression** - Allow returning to previous steps when possible

## 🎯 Usage Patterns

### Onboarding Wizard

```tsx
const onboardingSteps = [
  {
    id: "welcome",
    title: "Welcome",
    description: "Get started with the platform",
  },
  { id: "profile", title: "Profile Setup", description: "Create your profile" },
  {
    id: "preferences",
    title: "Preferences",
    description: "Set your preferences",
  },
  { id: "complete", title: "Complete", description: "You're all set!" },
];
```

### Data Import Wizard

```tsx
const importSteps = [
  { id: "upload", title: "Upload File", description: "Select your data file" },
  {
    id: "mapping",
    title: "Map Fields",
    description: "Map file columns to fields",
  },
  {
    id: "validate",
    title: "Validate",
    description: "Review data validation results",
  },
  { id: "import", title: "Import", description: "Complete the import process" },
];
```

### Settings Configuration

```tsx
const settingsSteps = [
  { id: "general", title: "General", description: "Basic settings" },
  { id: "security", title: "Security", description: "Security preferences" },
  {
    id: "notifications",
    title: "Notifications",
    description: "Notification settings",
  },
  { id: "review", title: "Review", description: "Review and save changes" },
];
```

## ♿ Accessibility

### Built-in Accessibility Features

- **Semantic HTML structure** with proper heading hierarchy
- **Keyboard navigation support** for step selection
- **ARIA attributes** for screen reader compatibility
- **Focus management** with visible focus indicators
- **Color contrast compliance** meeting WCAG 2.1 standards

### Accessibility Enhancements

```tsx
// Add ARIA labels for better screen reader experience
<WizardControl
  steps={steps.map((step) => ({
    ...step,
    title: `${step.title} - Step ${steps.indexOf(step) + 1} of ${steps.length}`,
  }))}
  currentStepId={currentStep}
  onStepChange={setCurrentStep}
>
  <div role="main" aria-live="polite">
    {stepContent}
  </div>
</WizardControl>
```

## 📱 Responsive Behavior

### Desktop (≥ 769px)

- **Steps panel**: 180px width with full descriptions
- **Content panel**: Flexible width with 20px padding
- **Step gaps**: 12px spacing between elements

### Mobile (≤ 768px)

- **Steps panel**: 140px width (reduced)
- **Content panel**: 12px padding (reduced)
- **Step gaps**: 8px spacing (tighter)
- **Minimum width**: 100% (full width)

## 🔗 Related Components

- **[Button](https://react.fluentui.dev/?path=/docs/components-button--default)** - For navigation controls in footer
- **[Text](https://react.fluentui.dev/?path=/docs/components-text--default)** - For step titles and descriptions
- **[Card](https://react.fluentui.dev/?path=/docs/components-card--default)** - For step content containers
- **[Form](https://react.fluentui.dev/?path=/docs/concepts-forms--default)** - For step content forms

---

**Component**: `WizardControl`  
**Location**: `Workload/app/components/Wizard/Wizard.tsx`  
**Version**: 1.0.0 - Initial release with CSS-based styling and responsive design
