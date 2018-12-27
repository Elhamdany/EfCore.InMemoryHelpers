# EfCore.InMemoryHelpers

Provides a wrapper around the [EF Core In-Memory Database Provider](https://docs.microsoft.com/en-us/ef/core/providers/in-memory/). Specifically works around the following EF bugs.

 * [InMemory: Improve in-memory key generation](https://github.com/aspnet/EntityFrameworkCore/issues/6872) <br>
  Reasoning: For many bootstrapping and integration tests, the id generation should be predictable, i.e. an in-memory persistence should not share static mutable state. This is especially important when using unit tests as a bootstrap to generate ad-hoc data.
 * Add index validation<br>
  Reasoning: It is desirable for indexes to be validated when running unit tests. This allows bugs to be caught earlier without the need for integration testing against a real database.
 * Add support for [Timestamp/row version](https://docs.microsoft.com/en-us/ef/core/modeling/concurrency#timestamprow-version) (`[Timestamp]`, and `.Property(p => p.X).IsRowVersion()`)<br>
  Reasoning: It is desirable for exceptions to be thrown when a update violates a RowVersion. This allows bugs to be caught earlier without the need for integration testing against a real database.

**This project is supported by the community via [Patreon sponsorship](https://www.patreon.com/join/simoncropp). If you are using this project to deliver business value or build commercial software it is expected that you will provide support [via Patreon](https://www.patreon.com/join/simoncropp).**


## NuGet [![NuGet Status](http://img.shields.io/nuget/v/EfCore.InMemoryHelpers.svg?longCache=true&style=flat)](https://www.nuget.org/packages/EfCore.InMemoryHelpers/)

https://nuget.org/packages/EfCore.InMemoryHelpers/

    PM> Install-Package EfCore.InMemoryHelpers


## Usage

The main entry point is `InMemoryContextBuilder` which can be used to build an in-memory context.

<!-- snippet: simple -->
```cs
using (var context = InMemoryContextBuilder.Build<MyDataContext>())
{
    var entity = new MyEntity
    {
        Property = "prop"
    };
    context.Add(entity);
    context.SaveChanges();
}
```
<!-- endsnippet -->

A custom `DbContextOptionsBuilder` can be passed in:

<!-- snippet: withBuilder -->
```cs
var builder = new DbContextOptionsBuilder<MyDataContext>();
using (var context = InMemoryContextBuilder.Build<MyDataContext>(builder))
{
    var entity = new MyEntity
    {
        Property = "prop"
    };
    context.Add(entity);
    context.SaveChanges();
}
```
<!-- endsnippet -->

Both the above usages assume that the target context has a public constructor that accepts a `DbContextOptions`.

<!-- snippet: dataContextCtor -->
```cs
public MyDataContext(DbContextOptions options) :
    base(options)
{
}
```
<!-- endsnippet -->

If this is not the case a custom context constructor can be passed in:

<!-- snippet: customContextConstructor -->
```cs
var builder = new DbContextOptionsBuilder<MyDataContext>();
using (var context = InMemoryContextBuilder.Build(builder, options => new MyDataContext(options)))
{
    var entity = new MyEntity
    {
        Property = "prop"
    };
    context.Add(entity);
    context.SaveChanges();
}
```
<!-- endsnippet -->


## Icon

<a href="https://thenounproject.com/term/memory/884922/" target="_blank">memory</a> designed by Montu Yadav from [The Noun Project](https://thenounproject.com)
