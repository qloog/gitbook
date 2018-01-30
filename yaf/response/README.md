# 响应

## 概述

响应对象和请求对象相对应, 是发送给请求端的响应的载体。

我们来直观的看一下类的具体内容

```
Yaf\Response_Abstract 
{
    /* Constants */
    const string DEFAULT_BODY = "content" ;
    
    /* 属性 */
    protected $_header ;
    protected $_body ;
    protected $_sendheader ;
    
    /* 方法 */
    public bool appendBody ( string $content [, string $key ] )
    public bool clearBody ([ string $key ] )
    public void clearHeaders ( void )
    private void __clone ( void )
    public __construct ( void )
    public void __destruct ( void )
    public mixed getBody ([ string $key ] )
    public void getHeader ( void )
    public bool prependBody ( string $content [, string $key ] )
    public void response ( void )
    protected void setAllHeaders ( void )
    public bool setBody ( string $content [, string $key ] )
    public void setHeader ( void )
    public void setRedirect ( void )
    private void __toString ( void )
}
```

## 响应类

### Yaf\Response\Http

Yaf\Response\Http是在Yaf作为Web应用的时候默认响应载体。因继承自`Yaf\Response_Abstract`， 所以`Yaf\Response_Abstract`有的方法该类里也有。

```
final Yaf\Response\Http extends Yaf\Response_Abstract 
{
    protected array _code = 200 ; // 响应给请求端的HTTP状态码
}
```

### Yaf\Response\Cli

Yaf\Response\Cli是在Yaf作为命令行应用的时候默认响应载体。 因继承自`Yaf\Response_Abstract`， 所以`Yaf\Response_Abstract`有的方法该类里也有。

```
final Yaf\Response\Cli extends Yaf\Response_Abstract 
{

}
```

## Yaf\Response\Http 和 Yaf\Response\Cli比较

通过使用`var_dump(get_class_methods(Yaf\Response\Http::class));` 得出：

```
array (size=14)
  0 => string 'setHeader' (length=9)
  1 => string 'setAllHeaders' (length=13)
  2 => string 'getHeader' (length=9)
  3 => string 'clearHeaders' (length=12)
  4 => string 'setRedirect' (length=11)
  5 => string 'response' (length=8)
  6 => string '__construct' (length=11)
  7 => string '__destruct' (length=10)
  8 => string '__toString' (length=10)
  9 => string 'setBody' (length=7)
  10 => string 'appendBody' (length=10)
  11 => string 'prependBody' (length=11)
  12 => string 'clearBody' (length=9)
  13 => string 'getBody' (length=7)
```
 
通过使用`var_dump(get_class_methods(Yaf\Response\Cli::class));`  得出
```
array (size=9)
  0 => string '__construct' (length=11)
  1 => string '__destruct' (length=10)
  2 => string '__toString' (length=10)
  3 => string 'setBody' (length=7)
  4 => string 'appendBody' (length=10)
  5 => string 'prependBody' (length=11)
  6 => string 'clearBody' (length=9)
  7 => string 'getBody' (length=7)
  8 => string 'response' (length=8)
```
对比后我们可以发现, `Yaf\Response\Http` 比 `Yaf\Response\Cli`的多了 header相关的方法：
```
setHeader
setAllHeaders
getHeader
clearHeaders
```

### 类方法

