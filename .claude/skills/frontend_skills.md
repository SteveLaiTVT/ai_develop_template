# Frontend Development Skills

前端开发规范与最佳实践 - 适用于所有包含前端组件的项目。

This skill defines frontend development standards and best practices for projects with frontend components.

---

## Core Principles

### 1. Component-Based Architecture

```
所有前端项目应采用组件化架构
All frontend projects should use component-based architecture
```

**Benefits:**
- Reusability across pages
- Easier testing and maintenance
- Clear separation of concerns
- Better team collaboration

### 2. Progressive Enhancement

- Core functionality works without JavaScript
- Enhanced experience with JavaScript enabled
- Graceful degradation for older browsers

### 3. Performance First

- Optimize for Core Web Vitals (LCP, FID, CLS)
- Lazy load non-critical resources
- Minimize bundle size
- Use code splitting

---

## Project Structure

### Standard Frontend Structure

```
src/
├── components/          # Reusable UI components
│   ├── common/          # Shared components (Button, Input, Modal)
│   ├── layout/          # Layout components (Header, Footer, Sidebar)
│   └── features/        # Feature-specific components
├── pages/               # Page components (route-level)
├── hooks/               # Custom React hooks
├── utils/               # Utility functions
├── services/            # API service layer
├── stores/              # State management
├── styles/              # Global styles, themes
├── types/               # TypeScript types/interfaces
├── constants/           # Constants and configuration
└── assets/              # Static assets (images, fonts)
```

### Component File Structure

```
components/
└── Button/
    ├── index.ts           # Exports
    ├── Button.tsx         # Component implementation
    ├── Button.styles.ts   # Styled components / CSS modules
    ├── Button.types.ts    # TypeScript interfaces
    ├── Button.test.tsx    # Tests
    └── Button.stories.tsx # Storybook stories (optional)
```

---

## Component Guidelines

### Functional Components (React)

```typescript
// ✅ Good: Functional component with proper types
interface ButtonProps {
  variant: 'primary' | 'secondary';
  size?: 'sm' | 'md' | 'lg';
  children: React.ReactNode;
  onClick?: () => void;
  disabled?: boolean;
}

export const Button: React.FC<ButtonProps> = ({
  variant,
  size = 'md',
  children,
  onClick,
  disabled = false,
}) => {
  return (
    <button
      className={`btn btn-${variant} btn-${size}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
};
```

### Component Best Practices

| Practice | Why |
|----------|-----|
| Single Responsibility | One component, one purpose |
| Props validation | Use TypeScript interfaces |
| Default props | Sensible defaults reduce errors |
| Memoization | Prevent unnecessary re-renders |
| Error boundaries | Handle component errors gracefully |

---

## State Management

### Local State (Component Level)

```typescript
// For simple, component-scoped state
const [isOpen, setIsOpen] = useState(false);
const [formData, setFormData] = useState({ name: '', email: '' });
```

### Global State (Application Level)

**Recommended Libraries:**
- **Zustand** - Simple, minimal boilerplate
- **Redux Toolkit** - Feature-rich, middleware support
- **Jotai** - Atomic state management

```typescript
// Zustand example
import { create } from 'zustand';

interface UserStore {
  user: User | null;
  setUser: (user: User) => void;
  logout: () => void;
}

export const useUserStore = create<UserStore>((set) => ({
  user: null,
  setUser: (user) => set({ user }),
  logout: () => set({ user: null }),
}));
```

### Server State

**Use TanStack Query (React Query)** for server state:

```typescript
const { data, isLoading, error } = useQuery({
  queryKey: ['users', userId],
  queryFn: () => fetchUser(userId),
});
```

---

## Styling Guidelines

### CSS Architecture Options

| Approach | When to Use |
|----------|-------------|
| CSS Modules | Component isolation, team projects |
| Tailwind CSS | Rapid development, utility-first |
| Styled Components | Dynamic styles, theming |
| CSS-in-JS | Complex dynamic styling |

### Tailwind CSS Best Practices

```tsx
// ✅ Good: Semantic class grouping
<button className="
  px-4 py-2                    /* Spacing */
  bg-blue-500 hover:bg-blue-600 /* Colors */
  text-white font-medium       /* Typography */
  rounded-lg                   /* Shape */
  transition-colors            /* Animation */
">
  Click Me
</button>

// ❌ Bad: Excessive classes without organization
<button className="
  px-4 py-2 bg-blue-500 text-white font-medium rounded-lg
  hover:bg-blue-600 hover:shadow-lg hover:scale-105
  active:bg-blue-700 shadow-md transition-all duration-200
  ease-in-out transform
">  {/* Too many classes - extract to component or use @apply */}
```

### Design System Tokens

```css
:root {
  /* Colors */
  --color-primary: #3b82f6;
  --color-secondary: #64748b;
  --color-success: #22c55e;
  --color-error: #ef4444;

  /* Spacing */
  --spacing-xs: 0.25rem;
  --spacing-sm: 0.5rem;
  --spacing-md: 1rem;
  --spacing-lg: 1.5rem;

  /* Typography */
  --font-size-sm: 0.875rem;
  --font-size-md: 1rem;
  --font-size-lg: 1.25rem;
}
```

---

## Accessibility (A11y)

### WCAG 2.1 AA Compliance

| Requirement | Implementation |
|-------------|----------------|
| Keyboard navigation | All interactive elements focusable |
| Color contrast | Minimum 4.5:1 for normal text |
| Alt text | All images have descriptive alt |
| ARIA labels | Label all interactive elements |
| Focus indicators | Visible focus states |

### Common A11y Patterns

```tsx
// ✅ Good: Accessible button
<button
  onClick={handleSubmit}
  aria-label="Submit form"
  disabled={isLoading}
>
  {isLoading ? 'Submitting...' : 'Submit'}
</button>

// ✅ Good: Accessible form field
<label htmlFor="email">Email Address</label>
<input
  id="email"
  type="email"
  aria-required="true"
  aria-invalid={!!errors.email}
  aria-describedby={errors.email ? 'email-error' : undefined}
/>
{errors.email && (
  <span id="email-error" role="alert">
    {errors.email}
  </span>
)}
```

---

## Performance Optimization

### Code Splitting

```tsx
// Lazy load pages/components
const Dashboard = React.lazy(() => import('./pages/Dashboard'));
const Settings = React.lazy(() => import('./pages/Settings'));

// Use with Suspense
<Suspense fallback={<LoadingSpinner />}>
  <Routes>
    <Route path="/dashboard" element={<Dashboard />} />
    <Route path="/settings" element={<Settings />} />
  </Routes>
</Suspense>
```

### Image Optimization

```tsx
// Next.js Image component
import Image from 'next/image';

<Image
  src="/hero.jpg"
  alt="Hero image"
  width={1200}
  height={600}
  priority      // Above-the-fold images
  placeholder="blur"
/>

// Standard HTML with lazy loading
<img
  src="/image.jpg"
  alt="Description"
  loading="lazy"
  decoding="async"
/>
```

### Memoization

```tsx
// Memoize expensive calculations
const sortedItems = useMemo(() => {
  return items.sort((a, b) => a.name.localeCompare(b.name));
}, [items]);

// Memoize callbacks
const handleClick = useCallback((id: string) => {
  dispatch(selectItem(id));
}, [dispatch]);

// Memoize components
const ExpensiveComponent = React.memo(({ data }) => {
  // Renders only when data changes
  return <ComplexVisualization data={data} />;
});
```

---

## Error Handling

### Error Boundaries

```tsx
class ErrorBoundary extends React.Component<Props, State> {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error: Error) {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    // Log to error tracking service
    logErrorToService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback error={this.state.error} />;
    }
    return this.props.children;
  }
}
```

### API Error Handling

```typescript
// Centralized API error handling
const handleApiError = (error: unknown): string => {
  if (error instanceof ApiError) {
    return error.userMessage;
  }
  if (error instanceof NetworkError) {
    return 'Network error. Please check your connection.';
  }
  return 'An unexpected error occurred.';
};

// In components
try {
  await submitForm(data);
} catch (error) {
  setErrorMessage(handleApiError(error));
}
```

---

## Form Handling

### Recommended Libraries

- **React Hook Form** - Performance, minimal re-renders
- **Formik** - Full-featured, good for complex forms
- **Zod** - Schema validation

### Form Best Practices

```typescript
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const schema = z.object({
  email: z.string().email('Invalid email'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
});

type FormData = z.infer<typeof schema>;

export const LoginForm = () => {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<FormData>({
    resolver: zodResolver(schema),
  });

  const onSubmit = async (data: FormData) => {
    await loginUser(data);
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email')} />
      {errors.email && <span>{errors.email.message}</span>}
      
      <input type="password" {...register('password')} />
      {errors.password && <span>{errors.password.message}</span>}
      
      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Logging in...' : 'Login'}
      </button>
    </form>
  );
};
```

---

## Testing Strategy

### Testing Pyramid for Frontend

```
        /\
       /  \  E2E Tests (Playwright, Cypress)
      /----\  Integration Tests (Testing Library)
     /------\  Unit Tests (Jest, Vitest)
    /--------\  Static Analysis (TypeScript, ESLint)
```

### Component Testing

```typescript
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from './Button';

describe('Button', () => {
  it('renders with correct text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button')).toHaveTextContent('Click me');
  });

  it('calls onClick when clicked', () => {
    const handleClick = jest.fn();
    render(<Button onClick={handleClick}>Click</Button>);
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('is disabled when disabled prop is true', () => {
    render(<Button disabled>Click</Button>);
    expect(screen.getByRole('button')).toBeDisabled();
  });
});
```

---

## Security Practices

### XSS Prevention

```tsx
// ✅ React automatically escapes content
<div>{userInput}</div>

// ⚠️ Dangerous - avoid unless absolutely necessary
<div dangerouslySetInnerHTML={{ __html: sanitizedHtml }} />

// Always sanitize if using dangerouslySetInnerHTML
import DOMPurify from 'dompurify';
const sanitized = DOMPurify.sanitize(userHtml);
```

### Secure Data Handling

- Never expose API keys in frontend code
- Use environment variables for configuration
- Validate all user inputs
- Implement CSRF protection
- Use HTTPS for all API calls

---

## Quick Reference Checklist

Before submitting frontend code:

```
✓ Components are properly typed (TypeScript)
✓ No console.log statements in production code
✓ All images have alt text
✓ Forms have proper validation
✓ Error states are handled gracefully
✓ Loading states are implemented
✓ Mobile responsiveness is verified
✓ Performance is acceptable (< 3s load time)
✓ Accessibility basics are met (keyboard, contrast)
✓ No hardcoded strings (use i18n or constants)
```
