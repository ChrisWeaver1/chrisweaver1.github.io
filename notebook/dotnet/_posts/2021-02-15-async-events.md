---
layout: md
title: Dotnet Async Events
description: 'Async event service'
sort_key: 1
---

{% include project-headers.html %}

```c#
/// <summary>
/// Async implementation of an Event.
/// </summary>
/// <typeparam name="TArgs">Type of Payload/args emitted by event</typeparam>

public delegate Task AsyncEventHandler<TArgs>(object sender, TArgs args);

/// <summary>
/// Base Implementation of an EventEmitter. This will provide all the base functionality
/// an event needs to operate, can be extended and overridden by implementing classes.
/// </summary>
/// <typeparam name="TArgs">Type of event arguments</typeparam>
public class BaseEventEmitter<TArgs> : IAsyncDisposable
{
    protected readonly IEnumerable<IEventHandler<TArgs>> events;

    public BaseEventEmitter(IEnumerable<IEventHandler<TArgs>> events)
    {
        // Add event handlers from service provider
        foreach (var ev in events)
        {
            this.Event += async (s, args) => { await ev.Handle(args); };
        }
    }

    protected virtual event AsyncEventHandler<TArgs> Event;

    /// <summary>
    /// Queue of events to be emitted on disposal of service.
    /// </summary>
    protected List<(Object sender, TArgs args)> queue { get; set; } 
        = new List<(object sender, TArgs args1)>();

    /// <summary>
    /// Emit event, triggering each of the registered event handlers.
    /// </summary>
    public virtual async Task Emit(Object sender, TArgs args)
    {
        if (this.Event != null)
        {
            await this.Event?.Invoke(sender, args);
        }

        return;
    }

    /// <summary>
    /// Add an emit call to the disposal queue
    /// </summary>
    public virtual void EmitOnDispose(Object sender, TArgs args)
    {
        this.queue.Add((sender, args));
        return;
    }

    /// <summary>
    /// Implementation of async disposal method. Triggers each of the events in the queue.
    /// </summary>
    public async ValueTask DisposeAsync()
    {
        foreach(var evt in this.queue)
        {
            await this.Emit(evt.sender, evt.args);
        }

        return;
    }
```
