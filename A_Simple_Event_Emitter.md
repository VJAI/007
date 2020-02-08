# A Simple Event Emitter

Below code shows you how we can implement a simple global event emitter.

## Source Code

```js
class Emitter {

  _objectsEventsHandlersMap = {};

  on(id, eventName, handler, once = false) {
    if (!this._hasObject(id)) {
      this._objectsEventsHandlersMap[id] = {};
    }

    const objEvents = this._objectsEventsHandlersMap[id];

    if (!objEvents.hasOwnProperty(eventName)) {
      objEvents[eventName] = [];
    }

    objEvents[eventName].push({
      handler: handler,
      once: once
    });

    return this;
  }

  off(id, eventName, handler) {
    if (!this._hasEvent(id, eventName)) {
      return this;
    }

    const objEvents = this._objectsEventsHandlersMap[id];

    if (!handler) {
      objEvents[eventName] = [];
    } else {
      objEvents[eventName] = objEvents[eventName].filter(eventSubscriber => {
        return eventSubscriber.handler !== handler;
      });
    }

    return this;
  }

  fire(id, eventName, ...args) {
    if (!this._hasEvent(id, eventName)) {
      return this;
    }

    let eventSubscribers = this._objectsEventsHandlersMap[id][eventName];

    for (let i = 0; i < eventSubscribers.length; i++) {
      let eventSubscriber = eventSubscribers[i];

      setTimeout(function (subscriber) {
        const { handler, once } = subscriber;

        handler(...args);

        if (once) {
          this.off(id, eventName, handler);
        }
      }.bind(this, eventSubscriber), 0);
    }

    return this;
  }

  clear(id) {
    if (!id) {
      this._objectsEventsHandlersMap = {};
      return this;
    }

    if (this._hasObject(id)) {
      delete this._objectsEventsHandlersMap[id];
    }

    return this;
  }

  _hasObject(id) {
    return this._objectsEventsHandlersMap.hasOwnProperty(id);
  }

  _hasEvent(id, eventName) {
    return this._hasObject(id) && this._objectsEventsHandlersMap[id].hasOwnProperty(eventName);
  }
}
```
