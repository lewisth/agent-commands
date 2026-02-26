Review the changes on @branch and apply the following guidelines: 

# C# & .NET Development Guidelines

## Language Features & Modern Syntax
- **Discards:** Use `_` for unused out parameters or tuple deconstruction elements.
- **Raw String Literals:** Use `"""` for multi-line strings or JSON to avoid escape characters. Use `$$"""` for interpolation with curly braces.
- **Required Members:** Use the `required` keyword for properties that must be set during initialization. Use `[SetsRequiredMembers]` on constructors if they handle initialization.
- **Primary Constructors:** Use primary constructors in classes and structs for cleaner dependency injection and field initialization.
- **Collection Expressions:** Use `[]` for array, list, and span initialization instead of `new[]` or `new List<T>()`.
- **Init-Only Setters:** Use `init` instead of `set` for immutable properties to allow object initialization while preventing post-creation modification.
- **Pattern Matching:** Use property patterns `if (obj is Type { Property: value } name)` for concise null checks and type casting.
- **Field Keyword:** Use the `field` keyword (C# 13) to access auto-property backing fields without manual declarations.
- **Static Abstract Members:** Use `static abstract` in interfaces to enforce static method implementation for generic constraints.
- **Default Interface Methods:** Use default implementations in interfaces to add functionality without breaking existing implementers.
- **Null-Coalescing Assignment:** Use `??=` to assign values to variables only if they are null.
- **Tuple Swapping:** Swap variables using `(a, b) = (b, a)` to avoid temporary variables.

## Performance & Memory Management
- **Sealed Classes:** Mark classes as `sealed` by default. This enables JIT devirtualization and improves performance.
- **Span<T> and Memory<T>:** Use `Span<T>` for stack-allocated or sliceable memory operations to avoid heap allocations.
- **ValueTask:** Return `ValueTask` or `ValueTask<T>` for async methods that frequently complete synchronously.
- **ArrayPool:** Use `ArrayPool<T>.Shared.Rent()` and `Return()` for large, short-lived buffers to reduce GC pressure.
- **StringBuilder:** Always use `StringBuilder` for string concatenation inside loops.
- **CollectionsMarshal:** Use `CollectionsMarshal.AsSpan(list)` for high-performance, zero-copy access to `List<T>` internal buffers.
- **TrimExcess:** Call `TrimExcess()` on large collections after significant removals to release unused memory.
- **Pre-size Collections:** Specify capacity in the constructor for `List` or `Dictionary` if the final size is known.
- **Stopwatch.GetTimestamp:** Use `Stopwatch.GetTimestamp()` for high-resolution timing instead of `DateTime.Now`.
- **MethodImpl:** Use `[MethodImpl(MethodImplOptions.AggressiveInlining)]` for small, hot-path methods.

## Async & Concurrency
- **Avoid Async Over Sync:** Never use `.Result` or `.Wait()`. Use `await` throughout the entire call stack.
- **CancellationToken:** Always propagate `CancellationToken` to async methods.
- **ConfigureAwait:** Use `.ConfigureAwait(false)` in library code to avoid context synchronization overhead.
- **Task.WhenAll:** Execute multiple independent tasks in parallel using `Task.WhenAll` rather than awaiting them sequentially.
- **Channel<T>:** Use `System.Threading.Channels` for high-performance producer/consumer patterns.
- **PriorityQueue:** Use `PriorityQueue<TElement, TPriority>` for efficient priority-based processing.
- **Async Factory Methods:** Use static async `CreateAsync` methods instead of logic-heavy constructors.

## LINQ & Collections
- **Avoid Double Enumeration:** Materialize LINQ queries with `.ToList()` or `.ToArray()` if they will be accessed multiple times.
- **TryGetNonEnumeratedCount:** Use this to check collection size without triggering a full enumeration.
- **Chunking:** Use `.Chunk(size)` to process large collections in batches.
- **MinBy / MaxBy:** Use `.MinBy(x => x.Prop)` instead of ordering and taking the first element.
- **StringSplitOptions:** Use `StringSplitOptions.TrimEntries | StringSplitOptions.RemoveEmptyEntries` when parsing strings.
- **Efficient Filtering:** Use `.Where()` before `.Select()` to reduce the number of projections.

## Entity Framework Core
- **AsNoTracking:** Use `.AsNoTracking()` for read-only queries to improve performance and reduce memory usage.
- **Bulk Operations:** Use `ExecuteUpdateAsync` and `ExecuteDeleteAsync` for mass updates without loading entities into memory.
- **DbContext Pooling:** Use `AddDbContextPool` in DI to reuse context instances.
- **Split Queries:** Use `.AsSplitQuery()` to prevent Cartesian explosion on queries with multiple `.Include()` calls.
- **Filtered Includes:** Apply filters directly inside `.Include(x => x.Collection.Where(...))`.

## Best Practices & Tooling
- **Structured Logging:** Pass parameters as arguments `Log("User {Id}", id)` rather than using string interpolation for better searchability and performance.
- **Path.Combine:** Use `Path.Combine` or `Path.Join` instead of manual string concatenation for file paths.
- **User Secrets:** Use the Secret Manager (`dotnet user-secrets`) to store sensitive local development data.
- **Directory.Build.props:** Use a single `Directory.Build.props` file to manage shared NuGet versions and project settings across a solution.
- **Deterministic Builds:** Enable `<Deterministic>true</Deterministic>` in project files for consistent binary output.
- **DebuggerDisplay:** Use `[DebuggerDisplay("{Property}")]` on classes to improve the debugging experience.
- **DateOnly/TimeOnly:** Use `DateOnly` and `TimeOnly` for domain models that do not require a full `DateTime` offset.