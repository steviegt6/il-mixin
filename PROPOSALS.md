# Feature/Implementation Proposals

Proposals for features and how they should be implemented.

See also:

- [SpongePowered/Mixin](https://github.com/SpongePowered/Mixin)
- [Fabric Wiki tutorial:mixin_examples](https://fabricmc.net/wiki/tutorial:mixin_examples)

# Shared Types

```cs
// Positional targeting enum.
public enum At
{
    Head,
    Tail,
    Return
}
```

# `ISoftMixin`

Defines standard mixin behavior that can be expected to work when patching assemblies on-disk, at runtime, or during coremod passes.

```cs
/* Defines the members a soft Mixin should contain.
 * Different implementations should be available
 * for different targets (System.Reflection.Emit, Mono.Cecil,
 * MonoMod, MonoMod.RuntimeDetour, Harmony, etc.)
 */
public partial interface ISoftMixin {
    void Inject(At at, Delegate injected);
}
```

## `void Inject(At at, Delegate injected)`

Injecting invokable delegates at different parts of a method.

### Head

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

### Tail

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

# `IHardMixin`

Defines standard mixin behavior that can be expected to work when patching assemblies on-disk or during coremod passes, but **_not_ at runtime**.

```cs
/* Defines the members a 
 * Different implementations should be available
 * for different targets (System.Reflection.Emit, Mono.Cecil,
 * MonoMod, etc.)
 */
public partial interface IHardMixin {
}
```
