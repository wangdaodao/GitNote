## 数据模板定义规范

### 数据模板中的每个属性由 3 部分构成：属性名、生成规则、属性值：

```
// 属性名   name
// 生成规则 rule
// 属性值   value
'name|rule': value
```

### 生成规则和示例：

1. 属性值是字符串 String

```
//通过重复 string 生成一个字符串，重复次数大于等于 min，小于等于 max。
'name|min-max': string

//通过重复 string 生成一个字符串，重复次数等于 count。
'name|count': string
```

2. 属性值是数字 Number

```
//属性值自动加 1，初始值为 number。
'name|+1': number

//生成一个大于等于 min、小于等于 max 的整数，属性值 number 只是用来确定类型。
'name|min-max': number

//生成一个浮点数，整数部分大于等于 min、小于等于 max，小数部分保留 dmin 到 dmax 位。
'name|min-max.dmin-dmax': number
```

例如：

```
Mock.mock({
    'number1|1-100.1-10': 1,
    'number2|123.1-10': 1,
    'number3|123.3': 1,
    'number4|123.10': 1.123
})
// =>
{
    "number1": 12.92,
    "number2": 123.51,
    "number3": 123.777,
    "number4": 123.1231091814
}
```

3. 属性值是布尔型 Boolean

```
//随机生成一个布尔值，值为 true 的概率是 1/2，值为 false 的概率同样是 1/2。
'name|1': boolean

//随机生成一个布尔值，值为 value 的概率是 min / (min + max)，值为 !value 的概率是 max / (min + max)。
'name|min-max': value
```

4. 属性值是对象 Object

```
//从属性值 object 中随机选取 count 个属性。
'name|count': object

//从属性值 object 中随机选取 min 到 max 个属性。
'name|min-max': object
```

5. 属性值是数组 Array

```
//从属性值 array 中随机选取 1 个元素，作为最终值。
'name|1': array

//从属性值 array 中顺序选取 1 个元素，作为最终值。
'name|+1': array

//通过重复属性值 array 生成一个新数组，重复次数大于等于 min，小于等于 max。
'name|min-max': array

//通过重复属性值 array 生成一个新数组，重复次数为 count。
'name|count': array
```

6. 属性值是函数 Function

```
//执行函数 function，取其返回值作为最终的属性值，函数的上下文为属性 'name' 所在的对象。
'name': function
```

7. 属性值是正则表达式 RegExp

```
'name': regexp
```

根据正则表达式 `regexp` 反向生成可以匹配它的字符串。用于生成自定义格式的字符串。

```
Mock.mock({
    'regexp1': /[a-z][A-Z][0-9]/,
    'regexp2': /\w\W\s\S\d\D/,
    'regexp3': /\d{5,10}/
})
// =>
{
    "regexp1": "pJ7",
    "regexp2": "F)\fp1G",
    "regexp3": "561659409"
}
```

## 数据占位符定义规范

占位符 只是在属性值字符串中占个位置，并不出现在最终的属性值中。

占位符 的格式为：

```
@占位符
@占位符(参数 [, 参数])
```

1. 用 `@` 来标识其后的字符串是 占位符。  
2. 占位符 引用的是 `Mock.Random` 中的方法。  
3. 通过 `Mock.Random.extend()` 来扩展自定义占位符。  
4. 占位符 也可以引用 数据模板 中的属性。  
5. 占位符 会优先引用 数据模板 中的属性。  
6. 占位符 支持 相对路径 和 绝对路径。  

```
Mock.mock({
  'avatar':'@image(100x100)',
  'name': {
    'first': '@FIRST',
    'middle': '@FIRST',
    'last': '@LAST',
    'full': '@first @middle @last',
    'cname': '@cname',
  },
  'city': '@city(true)',
  'birthday': '@date("yyyy-MM-dd")  @time()'
  'luckColor' : '@color',
  'about':'@paragraph( 20, 50 )'
})
// =>
{
  "avatar":"http://dummyimage.com/100x100"
  "name": {
      "first": "Charles",
      "middle": "Brenda",
      "last": "Lopez",
      "full": "Charles Brenda Lopez",
      "cname": "郝刚",
  },
  "city": "香港特别行政区 九龙",
  "birthday": "1996-10-14 20:38:38"
  "luckColor" : "#87f279",
  "about":"Esc jkcfm qykqdod ocskv vnnntrvdf gvjv ykrquyh gudefpbf bfx pduggbhpo drrmgb eulxjcxau xvzw."
}
```

## Mock.Random

`Mock.Random` 是一个工具类，用于生成各种随机数据。

`Mock.Random` 的方法在数据模板中称为『占位符』，书写格式为 `@占位符(参数 [, 参数])` 。

### 方法

|Type|Method|
|----|------|
|Basic|boolean, natural, integer, float, character, string, range, date, time, datetime, now|
|Image|image, dataImage|
|Color|color|
|Text|paragraph, sentence, word, title, cparagraph, csentence, cword, ctitle|
|Name|first, last, name, cfirst, clast, cname|
|Web|url, domain, email, ip, tld|
|Address|area, region|
|Helper|capitalize, upper, lower, pick, shuffle|
|Miscellaneous|guid, id|

### 扩展

`Mock.Random` 中的方法与数据模板的 `@占位符` 一一对应，在需要时还可以为 `Mock.Random` 扩展方法，然后在数据模板中通过 `@扩展方法` 引用。例如：

```
Random.extend({
  constellation: function(date) {
    var constellations = ['白羊座', '金牛座', '双子座', '巨蟹座', '狮子座', '处女座', '天秤座', '天蝎座', '射手座', '摩羯座', '水瓶座', '双鱼座']
    return this.pick(constellations)
  },
  weekday: function(date) {
    var weekdays = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
    return this.pick(weekdays);
  },
  sex: function(date) {
    var sexes = ['男', '女', '中性', '未知'];
    return this.pick(sexes);
  }
})


Random.constellation()
// => "水瓶座"
Mock.mock('@constellation')
// => "天蝎座"
Mock.mock({
  constellation: '@constellation'
})
// => { constellation: "射手座" }

Random.weekday()
// => "Saturday"
Mock.mock('@weekday')
// => "Saturday"
Mock.mock({
  weekday: '@weekday'
})
// => { weekday: "Saturday" }

Random.sex()
// => "男"
Mock.mock('@sex')
// => "男"
Mock.mock({
  sex: '@sex'
})
// => { sex: "男" }
```