# Feature/Implementation Proposals

Proposals for features and how they should be implemented.

See also:

- [SpongePowered/Mixin](https://github.com/SpongePowered/Mixin)
- [Fabric Wiki tutorial:mixin_examples](https://fabricmc.net/wiki/tutorial:mixin_examples)

## Core API

```cs
// Positional targeting enum.
public enum At
{
    Head,
    Tail,
    Return
}

/* Defines the members a Mixin should contain.
 * Different implementations should be available
 * for different targets (System.Reflection.Emit, Mono.Cecil,
 * MonoMod, MonoMod.RuntimeDetour, Harmony, etc.)
 */
public partial interface IMixin {
}

// Example, rough ILCursor implementation of important
// details for reference (TODO).
public class ILCursorMixin : ILMixin {
    protected ILCursor { get; }

    public ILCursorMixin(ILCursor cursor) {
        Cursor = cursor;
    }
}
```

### Inject

Injecting invokable delegates at different parts of a method.

```cs
partial interface IMixin {
    void Inject(At at, Delegate injected);
}
```

#### Head

```cs
mixin.Inject(At.Head, (params) => { });
```

```diff
 public void Foo(params) {
+    Injected(params);
     DoSomething1();
     DoSomething2();
     DoSomething3();
 }
```

#### Tail

```cs
mixin.Inject(At.Tail, (params) => { });
```

```diff
 public void Foo(params) {
     DoSomething1();
     if (DoSomething2()) {
         return;
     }
     DoSomething3();
+    Injected(params);
 }
```

### Return

```cs
mixin.Inject(At.Return, (params) => { });
```

```diff
 public void Foo(params) {
     DoSomething1();
     if (DoSomething2()) {
+        Injected(params);
         return;
     }
     DoSomething3();
+    Injected(params);
 }
```
