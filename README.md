# Rewrite


>插件的设计目的在于对请求进行匹配。 如果符合以下规则，则认为匹配一致:
> * 请求的Method符合预定义Method
> * 请求的Header中包含预定义的Header
>如果匹配一致，则将请求重定向到设定的URL。
>此插件不会修改upstream,因此仅仅是修改了源URL。


## Example 1

假设有API /_ping. 在没有启用rewrite plugin之前，其Upstream URL为 http://up1/_ping。 
也就是说，当发起 xxx/_ping请求时，Kong会将此请求路由到http://up1/_ping. 

如果启用rewrite plugin之后，设定如下参数:

```lua
name=rewrite&
config.methods=POSTd&
config.rules=H2?/v1/echo?source=rewrite%26source2=kongplugin
```
上述参数的意思是，当对/_ping发起POST请求后，如果请求Header中存在[H2]，则会将此请求重定向到/v1/echo，并且添加额外的query_string。source=rewrite&source2=kongplugin. 
最终的目标URL为:
```lua
http://up1/v1/echo?source=rewrite&source2=kongplugin
``` 

## Example 2

Rewrite Plugin 会对重名的query_string进行覆盖。假设，源请求为:
```lua
/_ping?soucre2=mymac&id=2
```
rewrite插件会将此请求重定向为: 
```lua
http://up1/v1/echo?soucre2=mymac&id=2&source=rewrite&source2=kongplugin
```

因此最终的source2=kongpluing。 因为Rewrite仅仅是将规则中的query_string附加到源query_string中。 因此这种行为是Openresty/Nginx的行为。


# ChangLog

### v0.1.0
* 支持按照Method，Header规则进行匹配。 
* 支持增加自定义query_string