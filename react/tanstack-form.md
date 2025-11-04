# TanStack Form

A solution for handling forms designed with first-class TypeScript support and headless UI copmonents.

## Basic concepts

- **`formOptions`**: Options for your form that can be shared between multiple forms.
- **Form instance (`useForm`)**: Object that represents a form and provides methods and properties to interact with it. You can create one either by passing the `formOptions`or not.
- **Field**: A single form input (e.g., a text or a checkbox). Is created using the `form.Field` component provided by the _form instance_. The component accepts a `name` prop that should match a key in the form default values and a `children` prop that is a renderd prop function that takes a `field`object as its argument.
- **Field state (`field.state`)**: State of a field which includes its current value, validation status, error messages, and other metadata.

## Field states

- `isTouched`: after the user changes or blurs the field.
- `isDirty`: after the field's value has been changed, even if reverted to default. Opposite of `isPristine`.
- `isPristine`: until the user changes the field value. Opposite of `isDirty`.
- `isBlurred`: after the field has been blurred.
- `isDefaultValue`: whether the current value is the default value.

```ts
const { isTouched, isDirty, isPristine, isBlurred, isDefaultValue } =
  field.state.meta;
```

## Validation

`@tanstack/react-form` provides both sync and async validation functions that can be passed to the `form.Field` component using the `validators` prop.

```ts
<form.Field
  name="firstName"
  validators={{
    onChange: ({ value }) =>
      !value
        ? "A first name is required"
        : value.length < 3
        ? "First name must be at least 3 characters"
        : undefined,
    onChangeAsync: async ({ value }) => {
      await new Promise((resolve) => setTimeout(resolve, 1000));
      return value.includes("error") && 'No "error" allowed in first name';
    },
  }}
  children={(field) => (
    <>
      <input
        value={field.state.value}
        onBlur={field.handleBlur}
        onChange={(e) => field.handleChange(e.target.value)}
      />
      <FieldInfo field={field} />
    </>
  )}
/>
```

You can also define a schema using libraries like `zod`.

## Other

- You can react to specifig triggers and listen to them to dispatch side effects.
- You can use array fields.
- You can reset the form.
