# HyperClient

HyperClient is a Ruby Hypermedia API client.

## Usage

Example API client:

    class MyAPIClient
      include HyperClient

      entry_point 'http://myapp.com/api'
      content_type [:json, :xml]
      authorization 'user', 'password', :digest 
    end

    api = MyAPIClient.new
    api.resources
    # => [#<Collection @name="posts">, #<Collection @name="authors">]

## Collections

Based on standard HTTP methods, you can interact with a collection in different
ways:

### List collection elements (GET)

    posts = api.resources.posts

    posts.list
    # => [#<Element @name='post'>, #<Element @name='post'>, #<Element @name='post'>]

### Replace the entire list (PUT)

Given a `new_collection` array with two post elements:

    posts = api.resources.posts

    posts.replace(new_collection)
    # => [#<Element @name='post'>, #<Element @name='post'>]

### Create a new element (POST)

    posts = api.resources.posts
    post = {title: 'Creating a post from the api', body: 'Lorem ipsum'}

    posts.create(post)
    # => #<Element @name='post', @data={:title => 'Creating a post from the
    API', body: 'Lorem ipsum'}>


### Delete the entire collection (DELETE)

    posts = api.resources.posts

    posts.delete
    # => nil

## Elements

### Retrieve a representation (GET)

    post = api.resources.first

    post.retrieve
    # => #<Element @name='post', @data={:title => 'Creating a post from the
    API', body: 'Lorem ipsum'}, @uri='http://myblog.com/api/posts/1'>

### Replace the element

    post = api.resources.posts.first
    data = {title: 'Modifying the post title'}

    post.replace(data)
    # => #<Element @name='post', @data={:title => 'Modifying the post title',
    body: 'Lorem ipsum'}>

### Create a new element

    post = api.resources.posts.first
    new_post = {title: 'Creating a post from the api', body: 'Lorem ipsum'}

    post.create(new_post)
    # => #<Element @name='post', @data={:title => 'Creating a post from the
    API', body: 'Lorem ipsum'}, @uri='http://myblog.com/api/posts/2'>

### Delete the element

    post = api.resources.posts.first

    post.delete
    # => nil

### Accessing element data

    post = api.resources.posts.first

    post.data
    # => {:title => 'My Post', :body => 'This is a nice post'}

### Accessing element resources

    post = api.resources.posts.first

    post.resources
    # => [#<Collection @name='comments'>, #<Element @name='author'>]

## Resource discovery

* From a HAL response (see http://stateless.co/hal_specification.html)
* From headers
* From JSON response with an element 'links'
* From XML response with an element 'links'
* Using a custom parser

## Resource permissions

Using the `Allow` header HyperClient should be able to restrict the allowed
method on a given `Collection` or `Element`.

## Embedded

HyperClient should be able to fetch `Elements` either from a link given in a
`Collection` or by getting the data when is embedded in a `Collection`.