# jessewaites/webmcp-rails

> Ruby on Rails implementation of the WebMCP protocol for server-rendered applications.

## Overview

[jessewaites/webmcp-rails](https://github.com/jessewaites/webmcp-rails) is a Ruby on Rails gem that brings WebMCP support to Rails applications. It provides helpers, generators, and middleware for registering WebMCP tools in server-rendered Rails views, enabling AI agents to interact with Rails applications through the standardized `navigator.modelContext` API.

- **Repository**: [github.com/jessewaites/webmcp-rails](https://github.com/jessewaites/webmcp-rails)
- **Author**: Jesse Waites ([@jessewaites](https://github.com/jessewaites))
- **Language**: Ruby
- **Framework**: Ruby on Rails 7+

## Key Features

### Declarative Tool Registration

Rails view helpers for the [declarative WebMCP API](https://github.com/webmachinelearning/webmcp/pull/76):

```erb
<!-- app/views/reservations/new.html.erb -->
<%= webmcp_form_for @reservation,
      toolname: "make_reservation",
      tooldescription: "Make a restaurant reservation" do |f| %>

  <%= f.date_field :date, required: true,
        toolparamdescription: "Desired reservation date" %>

  <%= f.time_field :time, required: true,
        toolparamdescription: "Preferred reservation time" %>

  <%= f.number_field :party_size, min: 1, max: 20, required: true,
        toolparamdescription: "Number of guests" %>

  <%= f.submit "Reserve" %>
<% end %>
```

Renders HTML with proper WebMCP attributes:

```html
<form toolname="make_reservation"
      tooldescription="Make a restaurant reservation"
      action="/reservations" method="post">
  <input type="date" name="reservation[date]" required
         toolparamdescription="Desired reservation date">
  <input type="time" name="reservation[time]" required
         toolparamdescription="Preferred reservation time">
  <input type="number" name="reservation[party_size]" min="1" max="20" required
         toolparamdescription="Number of guests">
  <button type="submit">Reserve</button>
</form>
```

### Imperative Tool Registration

JavaScript helpers injected into Rails views for imperative tool registration:

```erb
<!-- app/views/layouts/application.html.erb -->
<%= webmcp_tools do %>
  <%= webmcp_tool name: "searchProducts",
        description: "Search the product catalog",
        input_schema: {
          type: "object",
          properties: {
            query: { type: "string", description: "Search query" },
            category: { type: "string", description: "Product category" }
          },
          required: ["query"]
        },
        controller_action: "products#search" %>
<% end %>
```

### Controller Integration

Handle WebMCP tool invocations in Rails controllers:

```ruby
# app/controllers/products_controller.rb
class ProductsController < ApplicationController
  include WebMCP::Controllable

  def search
    if webmcp_request?
      results = Product.search(params[:query], category: params[:category])
      render_webmcp_response(results)
    else
      # Normal HTML response
      @products = Product.search(params[:query])
    end
  end
end
```

### Agent Detection Middleware

Rack middleware for detecting AI agent interactions:

```ruby
# config/application.rb
config.middleware.use WebMCP::AgentDetection

# In controllers
def create
  if agent_invoked?
    # Handle agent-initiated request
    handle_agent_submission
  else
    # Handle human submission
    handle_normal_submission
  end
end
```

## Installation

### Gemfile

```ruby
gem 'webmcp-rails'
```

### Install

```bash
bundle install
rails generate webmcp:install
```

### Generator Output

```
create  config/initializers/webmcp.rb
create  app/javascript/webmcp.js
insert  app/views/layouts/application.html.erb
```

## Configuration

```ruby
# config/initializers/webmcp.rb
WebMCP::Rails.configure do |config|
  config.enable_declarative = true
  config.enable_imperative = true
  config.include_polyfill = true  # Include @mcp-b/global polyfill
  config.default_annotations = {
    readOnlyHint: false
  }
end
```

## View Helpers

| Helper | Description |
|--------|-------------|
| `webmcp_form_for` | Form builder with `toolname`/`tooldescription` attributes |
| `webmcp_form_tag` | Simple form tag with WebMCP attributes |
| `webmcp_tools` | Block helper for imperative tool registration JavaScript |
| `webmcp_tool` | Define a single imperative tool |
| `webmcp_polyfill_tag` | Include the `@mcp-b/global` polyfill script |

## Requirements

- Ruby 3.1+
- Rails 7.0+
- Chrome 146+ (or polyfill for other browsers)

## See Also

- [@mcp-b/react-webmcp](./react-webmcp.md) — React hooks for WebMCP
- [webmcp-kit](./webmcp-kit.md) — Framework-agnostic WebMCP toolkit
- [WordPress wmcp.dev](../cms/wordpress-wmcp-dev.md) — WordPress WebMCP integration
- [Declarative API](https://github.com/webmachinelearning/webmcp/pull/76) — W3C declarative API spec
- [W3C WebMCP Spec](https://webmachinelearning.github.io/webmcp) — Normative specification
- [Core concepts and overview](../../01-overview/what-is-webmcp.md) — Core concepts and overview
- [Tool registration API](../../02-specification/register-tool.md) — Tool registration API
- [Declarative API overview](../../03-declarative-api/overview.md) — Declarative API overview
- [Form mapping for declarative tools](../../03-declarative-api/form-mapping.md) — Form mapping for declarative tools
- [Service workers overview](../../05-service-workers/overview.md) — Service workers overview
- [Declarative API tutorial](../../12-tutorials/declarative-api-tutorial.md) — Declarative API tutorial
- [Best practices](../../12-tutorials/best-practices.md) — Best practices
- [E-commerce use case](../../11-use-cases/e-commerce.md) — E-commerce use case
- [Security checklist](../../04-security/developer-security-checklist.md) — Security checklist
- [Complete API reference](../../15-reference/api-reference.md) — Complete API reference
