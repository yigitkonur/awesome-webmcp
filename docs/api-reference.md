# API Quick Reference

### [Imperative API](https://webmachinelearning.github.io/webmcp)

```javascript
// Register a tool (#15)
navigator.modelContext.registerTool({
  name: "searchFlights",
  description: "Search for available flights between two airports",
  inputSchema: {
    type: "object",
    properties: {
      origin:      { type: "string", description: "IATA code (e.g. LHR)" },
      destination: { type: "string", description: "IATA code (e.g. JFK)" },
      date:        { type: "string", format: "date" }
    },
    required: ["origin", "destination", "date"]
  },
  annotations: { readOnlyHint: true },  // #53
  execute: async ({ origin, destination, date }) => {
    const results = await searchFlightsAPI(origin, destination, date);
    return { content: [{ type: "text", text: JSON.stringify(results) }] };
  }
});

// Replace all tools (#15)
navigator.modelContext.provideContext({ tools: [/* ... */] });

// Remove tools
navigator.modelContext.unregisterTool("searchFlights");
navigator.modelContext.clearContext();
```

### [Declarative API](https://github.com/webmachinelearning/webmcp/pull/76)

```html
<form toolname="book_table"
      tooldescription="Reserve a table at this restaurant"
      toolautosubmit action="/api/reserve">
  <input type="date" name="date" required toolparamdescription="Reservation date">
  <input type="time" name="time" required toolparamdescription="Reservation time">
  <input type="number" name="guests" min="1" max="20" required>
  <button type="submit">Reserve</button>
</form>
```

### [Events & CSS](https://webmachinelearning.github.io/webmcp)

```javascript
form.addEventListener("submit", (e) => {
  if (e.agentInvoked) {
    e.preventDefault();
    e.respondWith(handleAgentSubmission(e));
  }
});

window.addEventListener('toolactivated', ({ toolName }) => { /* ... */ });
window.addEventListener('toolcancel', ({ toolName }) => { /* ... */ });
```

```css
form:tool-form-active { outline: 2px solid #4CAF50; }
button:tool-submit-active { background: #FFD700; }
```

### [User Interaction](https://github.com/webmachinelearning/webmcp/issues/21)

```javascript
execute: async ({ productId }, agent) => {
  const ok = await agent.requestUserInteraction(async () => {
    return confirm(`Purchase ${productId}?`);
  });
  if (!ok) throw new Error("Canceled");
  return { content: [{ type: "text", text: "Purchased" }] };
}
```
