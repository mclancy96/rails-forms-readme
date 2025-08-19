# Rails Forms Overview

> **Note:** This lesson is written for students working on legacy Rails codebases that still use `form_tag` and `form_for`. In the current versions of Rails, these helpers are considered legacy and should not be used in new code. The modern and recommended approach is to use `form_with`, which is covered below. However, you will encounter `form_tag` and `form_for` in older projects, so it is important to understand them.

Imagine that you're on a roadtrip across the country (I'm already jealous) with
a starting point of Santa Barbara and a final destination of New York City. If
you enter the addresses into Google Maps, you'll be shown multiple routes, and
each route has an associated duration.

How do you select which route to take? Some of the points that should be
included in your decision are below:

- Duration

- Cities that you go through

- Landmarks that you want to drive through

Forms in Rails are similar to your roadtrip. Rails supplies a few different
options to choose from when creating forms. How do you know the right option to
select? Just like your roadtrip you consider the strengths of each form option
and see how well it aligns with your intended behavior and the application
requirements.

In this lesson we will review:

- Both of the main form implementations in Rails

- Discuss when one type of form should be selected over the other

- Walk through the different form options


## `form_tag` (Legacy)


**Legacy Notice:** `form_tag` is considered legacy as of Rails 5.1 and should not be used in new code. It is covered here for reference and to help you work with older codebases.

Attributes of the `form_tag` helper:

- Most basic form helper that's available in Rails

- Uses tag form elements to build out a form

- Unlike the `form_for` helper, it does not use a form builder

Let's build out a form that lets users enter in their cat's name and their associated color:

```erb
<%= form_tag("/cats") do %>
  <%= label_tag('cat[name]', "Name") %>
  <%= text_field_tag('cat[name]') %>

  <%= label_tag('cat[color]', "Color") %>
  <%= text_field_tag('cat[color]') %>

  <%= submit_tag "Create Cat" %>
<% end %>
```

This will build a form and auto generate the following HTML:

```html
<form accept-charset="UTF-8" action="/cats" method="POST">
  <label for="cat_name">Name</label>
  <input id="cat_name" name="cat[name]" type="text" />
  <label for="cat_color">Color</label>
  <input id="cat_color" name="cat[color]" type="text" />
  <input name="commit" type="submit" value="Create Cat" />
</form>
```


## `form_for` (Legacy)


**Legacy Notice:** `form_for` is also considered legacy as of Rails 5.1 and should not be used in new code. It is covered here for reference and to help you work with older codebases.

Attributes for the `form_for` form helper method:

- More magical form helper in Rails

- `form_for` is a ruby method into which a Ruby object is passed. This means that a form that utilizes `form_for` is directly connected with an Active Record model

- `form_for` yields a `FormBuilder` object that lets you create form elements that correspond to attributes in the model

So, what does this all mean? When you're using the `form_for` method, the object
is passed as a `form_for` parameter, and it creates corresponding inputs with
each of the attributes. For example, if you have `form_for(@cat)`, the form
field params would look like `cat[name]`, `cat[color]`, etc

Let's refactor the cat form that we discussed in the previous section. With
`form_for`, it can be simplified to look like this:

```erb
<%= form_for(@cat) do |f| %>
  <%= f.label :name %>
  <%= f.text_field :name %>
  <%= f.label :color %>
  <%= f.text_field :color %>
  <%= f.submit %>
<% end %>
```

The `form_for` above will auto generate the following HTML:

```html
<form accept-charset="UTF-8" action="/cats" method="post">
  <label for="cat_name">Name</label>
  <input id="cat_name" name="cat[name]" type="text" />
  <label for="cat_color">Color</label>
  <input id="cat_color" name="cat[color]" type="text" />
  <input name="commit" type="submit" value="Create" />
</form>
```

## `form_with` (Recommended)

As of Rails 5.1, `form_with` is the recommended way to build forms in Rails. It unifies the functionality of `form_tag` and `form_for` into a single, flexible helper. You should use `form_with` for all new code.

Attributes of the `form_with` helper:

- Modern, flexible form helper introduced in Rails 5.1
- Can be used with or without a model object
- Yields a `FormBuilder` object, similar to `form_for`
- By default, generates forms that submit data via AJAX (remote: true), but you can disable this

Example using a model object:

```erb
<%= form_with(model: @cat, local: true) do |form| %>
  <%= form.label :name %>
  <%= form.text_field :name %>
  <%= form.label :color %>
  <%= form.text_field :color %>
  <%= form.submit %>
<% end %>
```

Example without a model object:

```erb
<%= form_with(url: "/cats", local: true) do |form| %>
  <%= form.label :name, "Name" %>
  <%= form.text_field :name %>
  <%= form.label :color, "Color" %>
  <%= form.text_field :color %>
  <%= form.submit "Create Cat" %>
<% end %>
```

**Note:** The `local: true` option makes the form submit with a normal HTTP request instead of AJAX, which is often preferred for beginners and for compatibility with legacy code.


## Understanding Params Structure and Strong Parameters

All three helpers (`form_tag`, `form_for`, and `form_with`) generate form fields that result in a similar nested params structure when the form is submitted. For example, if you have a form for a `Cat` model with fields for `name` and `color`, the submitted params will look like this:

```ruby
{
  "cat" => {
    "name" => "Whiskers",
    "color" => "Tabby"
  }
}
```


This means that in your controller, you can access the values with `params[:cat][:name]` and `params[:cat][:color]`. This nested structure is consistent across all three helpers, making it easier to work with model attributes in Rails controllers.

### Handling Params in the Controller (Strong Parameters)

In modern Rails applications, you should use strong parameters to safely handle form input. For example, in your `CatsController`, you might have:

```ruby
def create
  @cat = Cat.new(cat_params)
  if @cat.save
    redirect_to @cat
  else
    render :new
  end
end

private

def cat_params
  params.require(:cat).permit(:name, :color)
end
```

This ensures that only the permitted attributes (`name` and `color`) are allowed through from the form submission.

## Differences between `form_with`, `form_for`, and `form_tag`

To summarize the differences:

- `form_with` is the modern, unified form helper. It can be used with or without a model, and is the only form helper you should use in new Rails code.
- `form_for` and `form_tag` are legacy helpers. They are still present in the current versions of Rails for backward compatibility, but should not be used in new code. You may encounter them in older codebases.
- `form_for` is best for forms tied to a model (CRUD operations), while `form_tag` is for forms not directly associated with a model (like search forms). `form_with` can handle both use cases.

**When to use which:**

- Use `form_with` for all new forms.
- Learn `form_for` and `form_tag` only to maintain or understand legacy code.

---

## Quick Reference Table

| Helper      | Use Case                        | Notes                                  |
|-------------|----------------------------------|----------------------------------------|
| form_tag    | Forms not tied to a model        | Use only in legacy codebases           |
| form_for    | Forms tied to a model (CRUD)     | Use only in legacy codebases           |
| form_with   | All new forms (model or not)     | Preferred for all new Rails code       |

**References:**
- [Rails Guides: Form Helpers](https://guides.rubyonrails.org/form_helpers.html)
