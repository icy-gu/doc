### js获取数组的交集、并集、差集、补集

### `filter()`方法
 `filter()`方法创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素

* 注意：`filter()`不会对空数组进行检测

* 注意：`filter()`不会改变原始数组


> #### 方法一：直接使用`filter`、`concat`计算

``` js
var a = [1,2,3,4,5];
var b = [2,4,6,8,10];
//  交集
var c = a.filter( 
            function(value){ 
                return b.index.Of( value ) > -1 
            } 
        );
//  并集
var d = a.concat( 
            b.filter( 
                function( value ){ 
                    return !( a.indexOf( value ) > -1 )
                }
            )
        );
//  差集
var e = a.filter( 
            function( value ){ 
                return b.indexOf(  value ) == -1 
            }
        );
//  补集
var f = a.filter( 
            function( value ){ 
                return !( b.indexOf( value ) > -1 )
            }.concat( 
                b.filter( function( value ){
                    return ! a.indexOf( value ) > -1 
                    }
                )
            )
        );
          
```

> #### 方法二、使用ES6语法实现

``` js
var a = [1,2,3,4,5];
var b = [2,4,6,8,10];
var sa = new Set(a);
var sb = new Set(b);
//  交集
let intersect  = a.filter(  x => sb.has( x ) );
//  并集
let unionSet = Array.from( new Set( [ ...a , ...b ] ) );
//  差集
let minus = a.filter( x => ! sb.has( x ) );
//  补集
let complement = [ ...a.filter( x => ! sb.has( x ) , ...b.filter( x => ! sa.has( x ) ) ) ];

```