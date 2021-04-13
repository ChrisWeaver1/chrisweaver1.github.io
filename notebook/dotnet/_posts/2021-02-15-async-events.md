---
layout: md
title: Dotnet Async Events
description: 'Async event service'
sort_key: 1
---

{% include project-headers.html %}

```c#
/// <summary>
/// Base implementation of an event service
/// </summary>
/// <typeparam name="TArgs">Type of Payload/args emitted by event</typeparam>
public abstract class EventEmitterBase<TArgs>
{
    protected readonly IEnumerable<IEventHandler<TArgs>> events;

    public EventEmitterBase(IEnumerable<IEventHandler<TArgs>> events)
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
