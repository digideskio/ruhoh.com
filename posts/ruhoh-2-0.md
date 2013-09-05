---
title:
date: '2012-11-06'
description:
categories: "news"
---


# Forward

Looking back, ruhoh 1.0 was more about ideals than usefulness.

**Language Agnostic API**  
I really believe in a language agnostic API but I shouldn't solve problems ruhoh doesn't yet have.
If I want adoption I need to believe in and focus on growing _one_ community -- the ruby community.

**Widgets**  
I also had this idea of "safe widgets". Useful snippets of functionality that do not require arbitrary server side code execution.
I _still_ think its a good idea but if I'm honest, the widgets implementation was born from selfishness.
Widgets allow me to run a value-added hosting service.  
But you, the user, don't care. You want _your_ blog to work like _your_ blog.
I understand and I agree. So I'll figure out my value-add later.

Right now is about building a product we love enough to use _everyday_.

# Ruhoh 2.x: Everything is a Plugin

Ruhoh 2.x makes no assumptions about what resources should exist in a blog and how they should behave.
"Plugins" are used to define all resources, their data-structure, and their behavior.

In this way ruhoh can be whatever _you_ want it to be -- it is merely plumbing.

Most all static-blogs are built on the assumption that a blog has "posts" and that these posts behave a certain pre-defined way.
A post, for example, may have one or more authors, a publish date, tags, categories and a customizable URL permalink.

Suppose you want to publish "essays", "snippets", "quotes", and so on. 
Rather than shoe-horn these types into the post model, 
we can use plugins to completely define custom resources and their behavior.

# Plugins

A plugin encapsulates the mappings, data, and behavior for a given resource.
A plugin primarily acts as a namespace both at the system level and within the user's blog.

## Resources

A resource is typically mapped to individual files within your blog.
Ruhoh identifies each file resource using a **pointer**.

A pointer is a hash specifying the filepath, relative filename, and registered plugin for the resource.
A pointer uniquely identifies a resource across the system.


## Mappings

By default, the name of the plugin is used as the plugin namespace.
The namespace, glob expression, url endpoint and other attributes work to provide mappings
to everything needed and generated by the plugin.

### Public Namespace

Once a namespace is registered, files within that namespace become mapped to that plugin.
A plugin may provide a custom glob expression to further specify how files are identified.
    
    - config.yml
    - pages
    - posts <-- namespace for files for the "posts" plugin
    - partials
    
#### Config

A mapping to user configuration is provided using the plugin's namespace within config.yml

    #config.yml
    
    posts: <-- configuration namespace
      permalink: blah
      limit: blah


## Data

A plugin may provide data to the database via its generate method.
A plugin has access to its config and file resources to intelligently generate data based on these assets.
Since a plugin is just a ruby class, generating data may be arbitrary.

## Behavior

Adding behavior is accomplished by name-spacing special classes within the plugin namespace.

- **Client**  
  The client class provides automatic name-spaced console methods for the given plugin.
- **Watcher**  
  The watcher class provides update logic when a given resource changes.
- **Previewer**  
  The previewer class provides a rack endpoint for the given resource.
- **Mustache View**  
  The mustache view provides all templating logic for the given resource.


# Database Improvements


## Lazy Loading

All resources are lazy-loaded providing automatic dependency resolution.
There is no longer any need to manually update the database. 

When compiling, the database caches all data so a dependency is only loaded once 
and re-used for subsequent requests.

## Singular Resource Retrieval

A resource can now be fetched individually using its pointer. 
The database automatically resolves all of its dependencies and returns the generated resource.

One major implication is the ability to compile a single resource (covered below).

# Mustache Views

Plugins may now define their own name-spaced Mustache views resulting in a much cleaner API.

As seen below the plugin defines its own top-level namespace. Arbitrary view methods are called via dot notation:
{{#raw_code}}
{{#posts.tags}}
  {{> tags_list }}
{{/posts.tags}}
{{/raw_code}}
    
Internally, ruhoh 2.0 simply delegates method calls to the plugin's Mustache view:

    # (dynamically generated internally by ruhoh)
    def posts 
      Ruhoh::Templaters::PostsView.new(@ruhoh)
    end


# Compiling

## Single Resource compile


# Cascade


# Unsolved

- Sane public plugin extensions
- What to do with widget functionality ?
- converter (haven't addressed these)
- nested resources (themes)
  the theme plugin is essentially a set of nested resources
    layouts
    stylesheets
    javascripts
  not really elegantly defined.
- asset pipeline is not well-defined anymore.
  Need a cohesive implementation
  
  




