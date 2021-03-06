# paperclip-backblaze

[![Build Status](https://travis-ci.org/alextsui05/paperclip-backblaze.svg?branch=master)](https://travis-ci.org/alextsui05/paperclip-backblaze)

The `paperclip-backblaze` provides a [Paperclip](https://github.com/thoughtbot/paperclip) storage adapter so that
attachments can be saved to [Backblaze B2 Cloud Storage API](https://www.backblaze.com/b2/docs/).
It makes use of Winston Durand's [backblaze](https://github.com/R167/backblaze) gem
to access the B2 API behind the scenes.

Backblaze B2 Cloud Storage is similar to Amazon's AWS S3 Storage, but it has a few selling points:

1. They run their own hardware (that's open-sourced, including the schematics, and drive reports)
2. It's $0.005/GB/month vs S3's $0.030/GB/month
3. You actually get a free GB of bandwidth a day, so it might be nice for personal projects.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'paperclip-backblaze', github: 'alextsui05/paperclip-backblaze'
```
And then execute:

    $ bundle

## Usage

You should be familiar with configuring Paperclip attachments for your model.
If not, please start with the Paperclip documentation
[here](https://github.com/thoughtbot/paperclip#usage).

Configuring Backblaze storage is very similar to [configuring S3 storage](http://www.rubydoc.info/gems/paperclip/Paperclip/Storage/S3).
Let's suppose we have a `Note` model with an `image` attachment that we would
like to be backed by Backblaze storage.



First, put your credentials and bucket name in a YML file.

```.yml
# config/b2.yml
account_id: 123456789abc
application_key: 0123456789abcdef0123456789abcdef0123456789
bucket: my_b2_bucket_name
```

Then let Paperclip know about it. You can put it in your environment config:

```.rb
# config/environments/production.rb

...
config.paperclip_defaults = {
  storage: :backblaze,
  b2_credentials: YAML.load(ERB.new(File.read("#{Rails.root}/config/b2.yml")).result).with_indifferent_access
}
```

Now just specify the attachment in the model, and it will use your backblaze configuration:

```.rb
# app/models/note.rb
class Note < ApplicationRecord
  has_attached_file :image
  ...
```

Currently, these are the required options for paperclip:

- `:storage` - This should be set to :backblaze in order to use this
   storage adapter.

- `:b2_credentials` - Should be a Hash containing required fields listend in config/b2.yml.

There is also a way to override the default bucket specified in the config. That can come handy when using a private bucket for specific files.

```.rb
# app/models/note.rb
class Note < ApplicationRecord
  has_attached_file :secret_photo,
    bucket: 'secret-images',
    path: '/image/:name.:extension',
    ....
```

## Contributing

This started as a proof of concept for a hobby project, so there's lots of room
for improvement, and it would be great to have your help.

Bug reports and pull requests are welcome on GitHub at
https://github.com/alextsui05/paperclip-backblaze. This project is intended to be a safe,
welcoming space for collaboration, and contributors are expected to adhere to
the [Contributor Covenant](contributor-covenant.org) code of conduct.

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

## Version Log

### 0.2.2

- Allow default bucket name override

### 0.2.1

- CI setup and code style improvements

### 0.2.0

- Remove `:b2_bucket` option. It should now be specified in the credentials file with a `bucket` key.
- Update example that doesn't pollute model with paperclip configuration

### 0.1.0

First release


