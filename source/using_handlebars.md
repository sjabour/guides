## Using Handlebars

This guide covers displaying content in the browser using Handlebars templates and SC.View. After reading this guide, you will be able to:

 * Describe the markup of your application using Handlebars-flavored HTML.
 * Have your HTML update automatically when your data model changes.

endprologue.

### Handlebars

SproutCore comes bundled with [Handlebars](http://handlebars.strobeapp.com), a semantic templating language. These templates look like regular HTML, with embedded handlebars expressions.

You should store your Handlebars templates inside your application's HTML file. At runtime, SproutCore will compile these templates so they are available for you to use in your views.

To immediately insert a template into your document, place it inside a `<script>` tag within your `<body>` tag:
  
<html>
  <body>
    <script type="text/x-handlebars">
      Hello, <b>{{MyApp.name}}</b>
    </script>
  </body>
</html>

To make a template available to be used later, give the `<script>` tag a name attribute:
  
<html>
  <head>
    <script type="text/x-handlebars" data-template-name="say-hello">
      Hello, <b>{{MyApp.name}}</b>
    </script>
  </head>
</html>

### SC.View

You can use SC.View to render a Handlebars template and insert it into the DOM.

To tell the view which template to use, set its `templateName` property. For example, if I had a `<script>` tag like this:

<html>
  <head>
    <script type="text/x-handlebars" data-template-name="say-hello">
      Hello, <b>{{name}}</b>
    </script>
  </head>
</html>
 
I would set the `templateName` property to `"say-hello"`.

<javascript>
var view = SC.TemplateView.create({
  templateName: 'say-hello',
  name: "Bob"
});
</javascript>

NOTE: For the remainder of the guide, the `templateName` property will be omitted from most examples. You can assume that if we show a code sample that includes an SC.View and a Handlebars template, the view has been configured to display that template via the `templateName` property.

### Handlebars Basics

As you've already seen, you can print the value of a property by enclosing it in a Handlebars expression, or a series of braces, like this:

<html>
My new car is {{color}}.
</html>

This will look up and print the TemplateView's `color` property. For example, if your view looks like this:

<javascript>
App.CarView = SC.View.extend({
  color: 'blue'
});
</javascript>

Your view would appear in the browser like this:

<html>
My new car is blue.
</html>

All of the features described in this guide are __bindings aware__. That means that if the values used by your templates ever change, your HTML will be updated automatically. It's like magic.

In order to know which part of your HTML to update when an underlying property changes, Handlebars will insert marker elements with a unique ID. If you look at your application while it's running, you might notice these extra elements:

<html>
My new car is <span id="sc232">blue</span>.
</html>

Because all Handlebars expressions are wrapped in these markers, make sure each HTML tag stays inside the same block. For example, you shouldn't do this:

<html>
<!-- Don't do it! -->

<div {{#if isUrgent}}class="urgent"{{/if}}>
</html>

If you want to avoid your property output getting wrapped in these markers, use the `unbound` helper:

<html>
My new car is {{unbound color}}.
</html>

Your output will be free of markers, but be careful, because the output won't be automatically updated!

<html>
My new car is blue.
</html>


#### {{#if}}, {{else}}, and {{#unless}}

Sometimes you may only want to display part of your template if a property exists. For example, let's say we have a view with a `person` property that contains an object with `firstName` and `lastName` fields:

<javascript>
App.SayHelloView = SC.View.extend({
  person: SC.Object.create({
    firstName: "Joy",
    lastName: "Clojure"
  })
});
</javascript>

In order to display part of the template only if the `person` object exists, we can use the `{{#if}}` helper to conditionally render a block:

<html>
{{#if person}}
  Welcome back, <b>{{person.firstName}} {{person.lastName}}</b>!
{{/if}}
</html>

NOTE: Handlebars will not render the block if the argument passed evaluates to `false`, `undefined`, `null` or `[]` (i.e., any "falsy" value).

If the expression evaluates to falsy, we can also display an alternate template using `{{else}}`:

<html>
{{#if person}}
  Welcome back, <b>{{person.firstName}} {{person.lastName}}</b>!
{{else}}
  Please log in.
{{/if}}
</html>

To only render a block if a value is falsy, use `{{#unless}}`:

<html>
{{#unless hasPaid}}
  You owe: ${{total}}
{{/unless}}
</html>

`{{#if}}` and `{{#unless}}` are examples of block expressions. These allow you to invoke a helper with a portion of your template. Block expressions look like normal expressions except that they contain a hash (#) before the helper name, and require a closing expression.

#### {{#with}}

Sometimes you may want to invoke a section of your template with a context different than the SC.View. For example, we can clean up the above template by using the `{{#with}}` helper:

<html>
{{#with person}}
  Welcome back, <b>{{firstName}} {{lastName}}</b>!
{{/with}}
</html>

NOTE: {{#with}} changes the _context_ of the block you pass to it. The context is the object on which properties are looked up. By default, the context is the SC.View to which the template belongs.

#### Binding Element Attributes with {{bindAttr}}

In addition to text, you may also want your templates to dictate the attributes of your HTML elements. For example, imagine a view that contains a URL:

<javascript>
App.LogoView = SC.View.extend({
  logoUrl: 'http://www.mycorp.com/images/logo.png'
});
</javascript>

The best way to display the URL as an image in Handlebars is like this:

<html>
<div id="logo">
  <img {{bindAttr src="logoUrl"}} alt="Logo">
</div>
</html>

This generates the following HTML:
<html>
<div id="logo">
  <img src="http://www.mycorp.com/images/logo.png" alt="Logo">
</div>
</html>

If you use `{{bindAttr}}` with a Boolean value, it will add or remove the specified attribute. For example, given this SproutCore view:

<javascript>
App.InputView = SC.View.extend({
  isSelected: true
});
</javascript>

And this template:
<html>
<input type="checkbox" {{bindAttr checked="isSelected"}}>
</html>

Handlebars will produce the following HTML element:

<html>
<input type="checkbox" checked>
</html>

#### Binding Class Names with {{bindAttr}}

The `class` attribute can be bound like any other attribute, but it also has some additional special behavior. The default behavior works like you'd expect:

<javascript>
App.AlertView = SC.View.extend({
  priority: "p4",
  isUrgent: true
});
</javascript>

<html>
<div {{bindAttr class="priority"}}>
  Warning!
</div>
</html>

This template will emit the following HTML:

<html>
<div class="p4">
  Warning!
</div>
</html>

If the value to which you bind is a Boolean, however, the dasherized version of that property will be applied as a class:

<html>
<div {{bindAttr class="isUrgent"}}>
  Warning!
</div>
</html>

This emits the following HTML:

<html>
<div class="is-urgent">
  Warning!
</div>
</html>

Unlike other attributes, you can also bind multiple classes:

<html>
<div {{bindAttr class="isUrgent priority"}}>
  Warning!
</div>
</html>

#### Localized Strings with {{loc}}

SproutCore has built-in support for localized applications. To emit a localized version of a string, use the `{{loc}}` helper:

<html>
{{loc myLocalizedString}}
</html>

### Building a View Hierarchy

So far, we've discussed writing templates for a single view. However, as your application grows, you will often want to create a hierarchy of views to encapsulate different areas on the page. Each view is responsible for handling events and maintaining the properties needed to display it.

#### {{view}}

To add a child view to a parent, use the `{{view}}` helper, which takes a path to a view class.

<javascript>
// Define parent view
App.UserView = SC.View.extend({
  templateName: 'user',

  firstName: "Albert",
  lastName: "Hofmann"
});

// Define child view
App.InfoView = SC.View.extend({
  templateName: 'info',

  posts: 25,
  hobbies: "Riding bicycles"
});
</javascript>

<html filename="user.handlebars">
User: {{firstName}} {{lastName}}
{{view App.InfoView}}
</html>

<html filename="info.handlebars">
<b>Posts:</b> {{posts}}
<br>
<b>Hobbies:</b> {{hobbies}}
</html>

If we were to create an instance of `App.UserView` and render it, we would get a DOM representation like this:

<html>
User: Albert Hofmann
<div>
  <b>Posts:</b> 25
  <br>
  <b>Hobbies:</b> Riding bicycles
</div>
</html>

#### Relative Paths

Instead of specifying an absolute path, you can also specify which view class to use relative to the parent view. For example, we could nest the above view hierarchy like this:

<javascript>
App.UserView = SC.View.extend({
  templateName: 'user',

  firstName: "Albert",
  lastName: "Hofmann",

  InfoView: SC.View.extend({
    templateName: 'info',

    posts: 25,
    hobbies: "Riding bicycles"
  })
});
</javascript>

<html filename="user.handlebars">
User: {{firstName}} {{lastName}}
{{view InfoView}}
</html>

#### Setting Child View Templates

If you'd like to specify the template your child views use (instead of having to place them in a separate Handlebars file), you can use the block form of the `{{view}}` helper. We might rewrite the above example like this:

<javascript>
App.UserView = SC.View.extend({
  templateName: 'user',

  firstName: "Albert",
  lastName: "Hofmann"
});

App.InfoView = SC.View.extend({
  posts: 25,
  hobbies: "Riding bicycles"
});
</javascript>

<html filename="user.handlebars">
User: {{firstName}} {{lastName}}
{{#view App.InfoView}}
  <b>Posts:</b> {{posts}}
  <br>
  <b>Hobbies:</b> {{hobbies}}
{{/view}}
</html>

When you do this, it may be helpful to think of it as assigning views to portions of the page. This allows you to encapsulate event handling for just that part of the page.

#### Setting Up Bindings

So far in our examples, we have been setting static values directly on the views. But to best implement an MVC architecture, we should actually be binding the properties of our views to the controller layer.

Let's set up a controller to represent our user data:

<javascript>
App.userController = SC.Object.create({
  content: SC.Object.create({
    firstName: "Albert",
    lastName: "Hofmann",
    posts: 25,
    hobbies: "Riding bicycles"
  })
});
</javascript>

Now let's update `App.UserView` to bind to `App.userController`:

<javascript>
App.UserView = SC.View.extend({
  templateName: 'user',

  firstNameBinding: 'App.userController.content.firstName',
  lastNameBinding: 'App.userController.content.lastName'
});
</javascript>

When we only have a few bindings to configure, like with `App.InfoView`, it is sometimes useful to be able to declare those bindings in the template. You can do that by passing additional arguments to the `{{#view}}` helper. If all you're doing is configuring bindings, this often allows you to bypass having to create a new subclass.

<html filename="user.handlebars">
User: {{firstName}} {{lastName}}
{{#view App.InfoView postsBinding="App.userController.content.posts"
        hobbiesBinding="App.userController.content.hobbies"}}
  <b>Posts:</b> {{posts}}
  <br>
  <b>Hobbies:</b> {{hobbies}}
{{/view}}
</html>

NOTE: You can actually pass __any__ property as a parameter to {{view}}, not just bindings. However, if you are doing anything other than setting up bindings, it is generally a good idea to create a new subclass.

#### Modifying a View's HTML

When you append a view, it creates a new HTML element that holds its content. If your view has any child views, they will also be displayed as child nodes of the parent's HTML element.

By default, new instances of `SC.View` create a `<div>` element. You can override this by passing a `tagName` parameter:

<html>
{{view App.InfoView tagName="span"}}
</html>

You can also assign an ID attribute to the view's HTML element by passing an `id` parameter:

<html>
{{view App.InfoView id="info-view"}}
</html>

This makes it easy to style using CSS ID selectors:

<css>
/** Give the view a red background. **/
#info-view {
  background-color: red;
}
</css>

You can assign class names similarly:

<html>
{{view App.InfoView class="info urgent"}}
</html>

You can bind class names to a property of the view by using `classBinding` instead of `class`. The same behavior as described in `bindAttr` applies:

<javascript>
App.AlertView = SC.View.extend({
  priority: "p4",
  isUrgent: true
});
</javascript>

<html>
{{view App.AlertView classBinding="isUrgent priority"}}
</html>

This yields a view wrapper that will look something like this:

<html>
<div id="sc420" class="sc-view is-urgent p4"></div>
</html>

### Displaying a List of Items

If you need to display a basic list of items, use Handlebar's `{{#each}}` helper:

<javascript>
App.PeopleView = SC.View.extend({
  people: [ SC.Object.create({ name: 'Steph' }),
            SC.Object.create({ name: 'Tom' }) ]
});
</javascript>

<html>
{{#each people}}
  Hello, {{name}}!
{{/each}}
</html>

This will print a list like this:

<html>
<ul>
  <li>Hello, Steph!</li>
  <li>Hello, Tom!</li>
</ul>
</html>

#### SC.CollectionView

Sometimes you will need each item in your list to handle events. In that case, you will need more sophistication than what `{{#each}}` can provide. You can use the `{{#collection}}` helper to create a new `SC.CollectionView`. You can bind the instance of `SC.CollectionView` to an array, and it will create a new `SC.View` for each item.

Usually, you will bind the collection to an `SC.ArrayProxy`, like this:

<javascript>
App.peopleController = SC.ArrayProxy.create({
  content: ['Steph', 'Tom', 'Ryan', 'Chris', 'Jill']
});
</javascript>

<html>
{{#collection contentBinding="App.peopleController"}}
  Hi, {{content}}!
{{/collection}}
</html>

NOTE: The template you pass to the block helper will look up properties relative to each child view. To access the item in the array that the view should represent, refer to the `content` property via {{content}}.

Remember that under the hood, `SC.CollectionView` creates a new view for each item in the bound array. By default, each new view will be an instance of `SC.View`. You can tell the collection which type of view to create instances of by subclassing `SC.CollectionView` and supplying a custom class:

<javascript>
App.PeopleCollectionView = SC.CollectionView.extend({
  itemViewClass: SC.View.extend({
    mouseDown: function(evt) {
      window.alert('You clicked on ' + this.get('content'));
    }
  })
});
</javascript>

<html>
{{#collection App.PeopleCollectionView contentBinding="App.peopleController"}}
  Hi, {{content}}!
{{/collection}}
</html>

If you run this code, you should see an alert every time you click on one of the items.

The `{{#collection}}` helper takes the same options as `{{#view}}`, as described above. For example, you can set an HTML `id` attribute on the container of `SC.CollectionView` like this:

<html>
{{collection App.MyCollectionView id="my-collection"}}
</html>

What if you want to set the class name of every child view, though? If you prepend an option with `item`, that option will instead be set on the child. For example, let's say you wanted to set a class name on each item in your collection:

<html>
{{collection App.MyCollectionView itemClass="collection-item"}}
</html>

### Writing Custom Helpers

Sometimes, you may use the same HTML in your application multiple times. In those case, you can register a custom helper that can be invoked from any Handlebars template.

For example, imagine you are frequently wrapping certain values in a `<span>` tag with a custom class. You can register a helper from your JavaScript like this:
  
<javascript>
Handlebars.registerHelper('highlight', function(property) {
  var value = SC.getPath(this, property);
  return '<span class="highlight">"+value+'</span>';
});
</javascript>

Anywhere in your Handlebars templates, you can now invoke this helper:

<html>
{{highlight name}}
</html>

and it will output the following:

<html>
<span class="highlight">Peter</span>
</html>

NOTE: Parameters to helper functions are passed as names, not their current values. This allows you to optionally set up observers on the values. To get the current value of the parameter, use SC.getPath, as shown above.

### Changelog

 * May 5, 2011: Initial version by [Tom Dale](credits.html#tomdale)
 * July 21, 2011: Corrections made for SC2 by [Erik Bryn](credits.html#ebryn)