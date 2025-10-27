# Utility types

## Partial\<T>

Makes all the properties optional.

> Perfect for update forms and PATCH requests.

```typescript
interface User {
    id: number;
    name: string;
    email: string;
}

function updateUser(updates: Partial<User>) {
    // ...
}

updateUser({ name: "Alice" })
```

## Pick<T, K>

Extracts specific properties.

> Ideal for component props and API responses.

```typescript
interface Product {
    id: number;
    name: string;
    price: number;
    description: string;
    internalNotes: string;
}

type ProductSummary = Pick<Product, "id" | "name" | "price">
```

## Omit<T, K>

Excludes unwanted properties.

> Good for clean data sanitization and secure APIs.

```typescript
interface ApiUser {
    id: number;
    email: string;
    password: string;
    name: string;
}

type UserCardProps = Omit<ApiUser, "password">;
```

## Record<K, T>

Creates key-value mappings.

> Useful for type-safe objects and configurations.

```typescript
type Theme = "dark" | "light";

const colors: Record<Theme, { bg: string, text: string }> = {
    light: { bg: "#ffffff", text: "#000000" },
    dark:  { bg: "#121212", text: "#ffffff" },
}
```

## ReturnType\<T>

Extracts function return types.

> Useful for consistend API responses and composable functions.

```typescript
function fetchUser(id: number) {
    return {
        user: { id, name: "Alice" },
        timestamp: new Date(),
    };
}

type UserResponse = ReturnType<typeof fetchUser>;

const response: UserResponse = fetchUser(1);
```
