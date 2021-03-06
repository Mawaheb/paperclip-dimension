paperclip-dimension
===================

This gem used to be called `mongoid_paperclip_image_dimension`, but it has been improved to be ORM agnostic.
Hence, the name of this gem is now simply `paperclip-dimension`.
If you were using `mongoid_paperclip_image_dimension` before, you can safely upgrade to this gem.


This is a simple gem to persist image dimensions of originals and thumbnails.
It works for both s3 and filesystem storages.
This lib is based on [this stackoverflow.com article](http://stackoverflow.com/questions/4065295/paperclip-saving-the-images-dimentions-width-height).

Install
-------

You can simply install from rubygems:

```ruby
gem install paperclip-dimension
require 'paperclip-dimension'
```

or in Gemfile:

```ruby
  gem 'paperclip-dimension'
```

Setup
-----

You need to make sure the following is in place:

* `Paperclip::Glue` is included in your model.
* `<attachment>_dimensions` attribute is requried to operate.


**Rails & ActiveRecord Migration**

`paperclip` gives you the ability to create migrations with `attachment`.
For example:

```ruby
class CreatePosts < ActiveRecord::Migration
  def up
    create_table :posts do |t|
      t.attachment :image
    end
  end
end
```

This creates the following columns:

* image_file_name
* image_file_size
* image_content_type
* image_updated_at

`paperclip-dimension` will create an extra field for you: `image_dimensions` when the above migration is run.

**Mongoid**

Exmaple Mongoid setup:

```ruby
class Post
  include Mongoid::Document
  include Paperclip::Glue

  # The usual paperclip fields
  field :image_file_name,     :type => String
  field :image_file_size,     :type => Integer
  field :image_content_type,  :type => String
  field :image_updated_at,    :type => Time

  # The extra paperclip-dimension field
  field :image_dimensions,    :type => Hash

  # call #has_attached_file like usual
  has_attached_file :image, :styles => {
    :large    =>    ['350x350>',     :jpg],
    :medium   =>    ['150x150>',     :jpg],
    :small    =>    ['30x30>',       :jpg]
  }
end
```

Usage
-----

This gem gives your model a few extra helpers:

```ruby
# suppose we used the example from above
p = Post.first

# retrieve image dimensions for all sytles
p.image_dimensions
#   {
#     'original' => [2048, 1024],
#     'large'    => [350, 175],
#     'medium'   => [150, 75],
#     'small'    => [30, 15]
#   }

# get image dimension as an array
p.image_dimension               # [2048, 1024]
p.image_dimension(:original)    # [2048, 1024]
p.image_dimension(:large)       # [350, 175]

# get it now as a string
p.image_dimension_str             # "2048x1024"
p.image_dimension_str(:original)  # "2048x1024"
p.image_dimension_str(:large)     # "350x175"
```

Contributing to paperclip-dimension
-----------------------------------

* Check out the latest master to make sure the feature hasn't been implemented or the bug hasn't been fixed yet
* Check out the issue tracker to make sure someone already hasn't requested it and/or contributed it
* Fork the project
* Start a feature/bugfix branch
* Commit and push until you are happy with your contribution
* Make sure to add tests for it. This is important so I don't break it in a future version unintentionally.
* Please try not to mess with the Rakefile, version, or history. If you want to have your own version, or is otherwise necessary, that is fine, but please isolate to its own commit so I can cherry-pick around it.

Copyright
---------

Copyright (c) 2011 Aaron Qian. See LICENSE.txt for
further details.

