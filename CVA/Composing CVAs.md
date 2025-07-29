You can **extend or compose** variants from other components (great for design systems).

```tsx
export const iconButtonVariants = cva(
  buttonVariants({ size: 'sm', variant: 'ghost' }),
  {
    variants: {
      shape: {
        square: 'rounded-md',
        circle: 'rounded-full',
      },
    },
    defaultVariants: {
      shape: 'square',
    },
  }
);

```