# Upgrading

Below are some **notes** as to what has changed for 2.x. However I really recommend to start at [/docs/2](/docs/2) and go through it once over with a completely new blog.

Honestly, some of these notes may be outdated but I've waited so ridiculously long to publish this I can't wait any longer.

Thank you very much for your patience.

## Using Ruhoh 2.x

It's best to install the new gem via bundler so you can execute it locally relative to a specific project.

Step by step instructions are available here:

https://github.com/ruhoh/blog/#readme

## command line

**Most all command-line commands have changed.**

When working with resources, you now have to namespace your command:

    # old
    $ ruhoh post
    
    # new
    $ ruhoh posts new
    

--ext no longer exists, in favor of [default ext per resource](/docs/2/pages#toc_39) in config.yml

## config.yml

**Most configuration formatting has changed.**

Now all the resource specific configuration is namespaced by the resource name. See [Pages Configuration](/docs/2/pages#toc_38)

### Theme config

old:
  
    theme: "twitter"
    
ruhoh 2.0.alpha:

    theme:
      name: "twitter"

The theme config is now like any other collection. You are telling the collection to use the "theme" class logic to act as a theme.

ruhoh 2.x:

    "twitter":
      use: "theme"

### Exclude paths

exclude paths for all resources (posts/pages) now omit the folder:

e.g. 

    # old
    ^posts/untitled 
    
    # new
    ^untitled
    

## Mustache API

Most all mustache methods have changed their API and implementation.

We'll outline some here but it's probably best to download the new 2.x blog scaffold and compare all the helpers.


### pages

Displaying all pages now must use `pages.all`:

old:

{{#raw_code}}
{{# pages }}
  <h2>{{title}}</h2>
  <p>{{summary}}</p>
{{/ pages }}
{{/raw_code}}

new:

{{#raw_code}}
{{# pages.all }}
  <h2>{{title}}</h2>
  <p>{{summary}}</p>
{{/ pages.all }}
{{/raw_code}}


### posts

Displaying all posts now must use `posts.all`

old:

{{#raw_code}}
{{# posts }}
  <h2>{{title}}</h2>
  <p>{{summary}}</p>
{{/ posts }}
{{/raw_code}}

new:

{{#raw_code}}
{{# posts.all }}
  <h2>{{title}}</h2>
  <p>{{summary}}</p>
{{/ posts.all }}
{{/raw_code}}


### posts_latest

Latest posts are now namespaced into the posts resource: `posts.latest`

old:

{{#raw_code}}
{{# posts_latest }}
<div class="post">
  <h3 class="title"><a href="{{url}}">{{title}}</a> <span class="date">{{ date }}</span></h3>
  {{{ summary }}}
</div>
{{/ posts_latest }}
{{/raw_code}}


new:

{{#raw_code}}
{{# posts.latest }}
<div class="post">
  <h3 class="title"><a href="{{url}}">{{title}}</a> <span class="date">{{ date }}</span></h3>
  {{{ summary }}}
</div>
{{/ posts.latest }}
{{/raw_code}}


### Categories

When getting all categories from posts, use `posts.categories.all`

old:

{{#raw_code}}
{{# categories }}
  {{> categories_list }}
{{/ categories }}
{{/raw_code}}


new:

{{#raw_code}}
{{# posts.categories.all }}
  {{> categories_list }}
{{/ posts.categories.all }}
{{/raw_code}}


### Tags

When getting all categories from posts, use `posts.tags.all`

old:

{{#raw_code}}
{{# tags }}
  {{> tags_list }}
{{/ tags }}
{{/raw_code}}


new:

{{#raw_code}}
{{# posts.tags.all }}
  {{> tags_list }}
{{/ posts.tags.all }}
{{/raw_code}}


### Previous and Next

Previous and next objects no longer use context helpers:

old:

{{#raw_code}}
{{# page?previous }}
  <li class="prev"><a href="{{ url }}" title="{{ title }}">&larr; Previous</a></li>
{{/ page?previous }}

{{# page?next }}
  <li class="next"><a href="{{ url }}" title="{{ title }}">&rarr; Next</a></li>
{{/ page?next }}
{{/raw_code}}


new:

{{#raw_code}}
{{# page.previous }}
  <li class="prev"><a href="{{ url }}" title="{{ title }}">&larr; Previous</a></li>
{{/ page.previous }}

{{# page.next }}
  <li class="next"><a href="{{ url }}" title="{{ title }}">&rarr; Next</a></li>
{{/ page.next }}
{{/raw_code}}


### widgets

All widgets must now be namespaced by `widgets`

old:

{{#raw_code}}
{{{ comments }}}
{{/raw_code}}

new:

{{#raw_code}}
{{{ widgets.comments }}}
{{/raw_code}}


### assets

{{#raw_code}}{{ assets }}{{/raw_code}}

has been changed to:

{{#raw_code}}
{{ stylesheets.all }}
{{ javascripts.all }}
{{/raw_code}}


Which means you can load javascripts at the end of the document if you want.

### urls

The `urls` object has all different attribute names: see [View 'view urls' docs](/docs/2/views#toc_12)

### site renamed to data

The `site` object is now named `data`

old:

{{#raw_code}}
<ul>
  <li>{{site.author.name}}</li>
  <li>{{site.author.email}}</li>
</ul>
{{/raw_code}}

new: 

{{#raw_code}}
<ul>
  <li>{{data.author.name}}</li>
  <li>{{data.author.email}}</li>
</ul>
{{/raw_code}}


[data.yml docs](/docs/2/views#toc_23)



## View Helper Plugins

Previously all view helpers shared the same global mustache namespace.

old:

    class Ruhoh
      module Templaters
        module Helpers
          def greeting
            "Hello there! How are you?"
          end
          def raw_code(sub_context)
            ...
          end
        end
      end
    end

To continue to add to the global namespace, attach to the MasterView:

new: 

    module MasterViewAddons
      def greeting
        "Hello there! How are you?"
      end
      def raw_code(sub_context)
        ...
      end
    end
    Ruhoh::Views::MasterView.send(:include, MasterViewAddons)
    
Adding global methods is now discouraged. It is more likely you want resource-specific functionality.
All resource-based helpers are name-spaced and belong to the resource's CollectionView or ModelView.

    module PagesCollectionViewAddons
      def greeting
        "Hello there! How are you?"
      end
      
      def random
        # The 'all' method is implemented by Resources::Pages::CollectionView
        # It returns an array of all pages.
        all.sample
      end
    end
    Ruhoh.collections('pages').send(:include, PagesCollectionViewAddons)


See [Plugin Docs](/docs/2/plugins) for more info.


## Compiler Plugins

Compiler plugins now pass `ruhoh` instance as its single argument:

old:

    class Ruhoh
      module Compiler
        module Posts
          def self.run(target, page)
          end
        end
      end
    end


new:

    class Ruhoh
      module Compiler
        module Posts
          def self.run(ruhoh)
          end
        end
      end
    end


## Converter Plugins

Converter plugins are currently the same.


## Pages URL

The default page URL format is now "pretty" which omits any filename extension even if 
the page is not an index.html page. To preserve the old style and include the .html extension, have a look at the [Page Permalink Docs](/docs/2/pages#toc_33)

## post.ruhoh.com

post.ruhoh.com DOES NOT support ruhoh 2+ yet.




# New Stuff

## Posts Paginator

Yay, finally implementated a paginator. [Paginator docs](/docs/2/posts)

## Categories on Pages

Pages can now have categories just like posts.

## Tags on Pages

Pages can now have tags just like posts.

## Simplified RSS Generator

RSS generator only outputs page.content now. No layouts , stylesheets, javascripts, etc.

## Chained contextual helpers

Contextual helpers can be chained:

{{#raw_code}}
{{{ data?to_hash?to_json }}}
{{/raw_code}}

