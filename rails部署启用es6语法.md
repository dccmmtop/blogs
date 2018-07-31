### Uglifier::Error: Unexpected token: punc ((). To use ES6 syntax, harmony mode must be enabled with Uglifier.new(:harmony => true)

部署时启用 ES6 语法
Try replacing

```ruby
config.assets.js_compressor = :uglifier
```

with

```ruby
config.assets.js_compressor = Uglifier.new(harmony: true)
```

in `config/environments/production.rb`
