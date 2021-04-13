---
layout: md
title: Dotnet Async Events
description: 'Async event service'
sort_key: 1
---

{% include project-headers.html %}

### IEventService.cs
```c#
/// <summary>
/// Defines a new Event service
/// </summary>
/// <typeparam name="TArgs">Type of Payload/args emitted by event</typeparam>
public interface IEventService<TArgs>
{
    Task Emit(object sender, TArgs args);
}
```

### AsyncEvent.cs
```c#
/// <summary>
/// Async implementation of an Event
/// </summary>
/// <typeparam name="TArgs">Type of Payload/args emitted by event</typeparam>

public delegate Task AsyncEvent<TArgs>(object sender, TArgs args);

```

### IEventHandler.cs
```c#
/// <summary>
/// Event handler definition
/// </summary>
/// <typeparam name="TArgs">Type of Payload/args emitted by event</typeparam>
public interface IEventHandler<TArgs>
{
    Task HandleEvent(Object sender, TArgs args);
}
```

### EventServiceBase.cs
```c#
/// <summary>
/// Base implementation of an event service
/// </summary>
/// <typeparam name="TArgs">Type of Payload/args emitted by event</typeparam>
public abstract class EventServiceBase<TArgs> : IEventService<TArgs>
{
    protected readonly IEnumerable<IEventHandler<TArgs>> events;

    public EventServiceBase(IEnumerable<IEventHandler<TArgs>> events)
    {
        // Add event handlers from service provider
        foreach (var ev in events)
        {
            this.@event += async (sender, args) => { await ev.Handle(sender, args); };
        }
    }

    protected virtual event AsyncEvent<TArgs> @event;

    /// <summary>
    /// Emit event now and wait for completion
    /// </summary>
    public virtual async Task Emit(Object sender, TArgs args)
    {
        if (this.@event != null)
        {
            await this.@event?.Invoke(sender, args);
        }

        return;
    }
}
```

### OnDisposalEventServiceBase.cs
```c#
/// <summary>
/// An extended implementation of BaseEventService. This implementation will capture & queue
/// the calls to emit rather than executing them right away.
/// They will be triggered on disposal of the service  
/// </summary>
/// <typeparam name="TArgs">Type of Payload/args emitted by event</typeparam>
public abstract class OnDisposalEventServiceBase<TArgs> : EventServiceBase<TArgs>, IAsyncDisposable, IEventService<TArgs>
{
    public OnDisposalEventServiceBase(IEnumerable<IEventHandler<TArgs>> events) : base(events)
    {
        this.queue = new List<(object sender, TArgs args)>();
    }

    /// <summary>
    /// Queue of events to be emitted on disposal of service.
    /// </summary>
    protected List<(Object sender, TArgs args)> queue { get; set; } 


    /// <summary>
    /// Add an emit call to the disposal queue
    /// </summary>
    public override Task Emit(Object sender, TArgs args)
    {
        this.queue.Add((sender, args));
        return Task.CompletedTask;
    }

    /// <summary>
    /// Implementation of async disposal method. Triggers each of the events in the queue.
    /// </summary>
    public async ValueTask DisposeAsync()
    {
        foreach(var evt in this.queue)
        {
            await base.Emit(evt.sender, evt.args);
        }

        return;
    }
}
```
