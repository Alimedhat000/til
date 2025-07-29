`cva` (Class Variance Authority) is a utility from the `class-variance-authority` package that helps you **manage className variants** in a clean, scalable way—particularly useful in **component libraries** using **Tailwind CSS**.

It’s especially helpful for components with multiple variations like:

- size: `sm`, `md`, `lg`
- variant: `primary`, `secondary`, `outline`
- state: `disabled`, `active`

### Basic Usage
Here’s a simple example using a `button` component:

```tsx
import { cva, type VariantProps } from 'class-variance-authority';

export const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-md font-medium transition-colors focus:outline-none focus:ring-2 focus:ring-offset-2',
  {
    variants: {
      variant: {
        default: 'bg-slate-900 text-white hover:bg-slate-700',
        outline: 'border border-slate-200 text-slate-900 hover:bg-slate-100',
        ghost: 'hover:bg-slate-100 text-slate-900',
      },
      size: {
        sm: 'h-8 px-3 text-sm',
        md: 'h-10 px-4',
        lg: 'h-12 px-6 text-lg',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'md',
    },
  }
);

// Type-safe props for use in component
export type ButtonVariants = VariantProps<typeof buttonVariants>;

```

and then in your component : 


```tsx
import { buttonVariants, type ButtonVariants } from './button';
import { cn } from '@/lib/utils'; // or your own classNames joiner

type ButtonProps = React.ButtonHTMLAttributes<HTMLButtonElement> & ButtonVariants;

export function Button({ className, variant, size, ...props }: ButtonProps) {
  return (
    <button
      className={cn(buttonVariants({ variant, size }), className)}
      {...props}
    />
  );
}
```

Now you can use it like:

```tsx
<Button>Default</Button>
<Button variant="outline" size="sm">Outline Small</Button>
<Button variant="ghost" size="lg">Ghost Large</Button>
```