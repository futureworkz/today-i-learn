By default, strong parameter just permit parameter with values are string.

```ruby
"category"=>{"options_attributes"=>{"0"=>{"value"=>["FSE", "FDI"], "id"=>"61"}, "1"=>{"value"=>"14", "id"=>"62"}, "2"=>{"value"=>"10", "id"=>"63"}}}

params.require(:category).permit(options_attributes: [:id, :value])

{"options_attributes"=>{"0"=>{"id"=>"61"}, "1"=>{"id"=>"62", "value"=>"14"}, "2"=>{"id"=>"63", "value"=>"10"}}}
```

If you need to permit parameter with values are an array. Let's use:

```ruby
params.require(:category).permit(options_attributes: [:id, value: []])

{"options_attributes"=>{"0"=>{"id"=>"61", "value"=>["FSE", "FDI"]}, "1"=>{"id"=>"62"}, "2"=>{"id"=>"63"}}}
```

But the string values are cleared out, so in case you need to permit both of string values and array values. Let's use simple trick:

```ruby
params.require(:category).permit(options_attributes: [:id, :value, value: []])

{"options_attributes"=>{"0"=>{"id"=>"61", "value"=>["FSE", "FDI"]}, "1"=>{"id"=>"62", "value"=>"14"}, "2"=>{"id"=>"63", "value"=>"10"}}}
```