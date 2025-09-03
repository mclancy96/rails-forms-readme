
# Recap: Rails Form Helpers

You’ve just learned how Rails provides three main ways to build forms: `form_tag`, `form_for`, and `form_with`. Each helper reflects a different stage in Rails’ evolution, and you’ve practiced using all three in previous lessons and labs.

Earlier, you saw that:

- Rails started with `form_tag` for basic forms not tied to models.
- Then added `form_for` to make it easier to build forms connected to Active Record models.
- Later, Rails unified both approaches into `form_with`, which is now the recommended way to build forms in new code.

## Comparing the Helpers

Here’s a quick summary of each helper, with a short example to jog your memory:

**form_tag** (legacy, not tied to a model):

```erb
<%= form_tag('/search') do %>
  <%= text_field_tag(:query) %>
  <%= submit_tag 'Search' %>
<% end %>
```

**form_for** (legacy, tied to a model):

```erb
<%= form_for(@cat) do |f| %>
  <%= f.text_field :name %>
  <%= f.submit %>
<% end %>
```

**form_with** (recommended for all new code):

```erb
<%= form_with(model: @cat, local: true) do |form| %>
  <%= form.text_field :name %>
  <%= form.submit %>
<% end %>
```

*Using `local: true` ensures the form submits normally instead of via AJAX.*

## Params & Strong Parameters

You saw how all three helpers generate a nested params structure. For example, submitting a form for a `Cat` with a `name` and `color` field will send:

```ruby
{
  "cat" => {
    "name" => "Whiskers",
    "color" => "Tabby"
  }
}
```

In your controller, you handled this with strong parameters:

```ruby
def cat_params
  params.require(:cat).permit(:name, :color)
end
```

## What to Use in the Real World

In new Rails code, always reach for `form_with`. When maintaining older projects, you may need to understand `form_tag` or `form_for`.

## Reflective Prompts

As a check, try answering:

- Which helper would you use if you were building a search form in an older Rails app?
- Which helper would you use for new Rails code?
- Why did Rails move toward a unified approach with `form_with`?
- How does the params structure differ (or stay the same) across these helpers?

---

## Quick Reference Table

| Helper      | Use Case                        | Notes                                  |
|-------------|----------------------------------|----------------------------------------|
| form_tag    | Forms not tied to a model        | Use only in legacy codebases           |
| form_for    | Forms tied to a model (CRUD)     | Use only in legacy codebases           |
| form_with   | All new forms (model or not)     | Preferred for all new Rails code       |

**References:**

- [Rails Guides: Form Helpers](https://guides.rubyonrails.org/form_helpers.html)
