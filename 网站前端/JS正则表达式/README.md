# JS正则表达式

## 目录
1. [创建RegExp对象](#创建regexp对象)
1. [特殊字符](#特殊字符)
1. [正则函数](#正则函数)

>正则性能：
>1. 尽可能准确地匹配目标字符串，以减少不必要的回溯。
>2. 正则表达式是匹配模式：要么匹配字符，要么匹配位置。

---
### 创建RegExp对象
1. 创建方法

    如果要匹配字符`\`：

    1. 字面量

        ```javascript
        /\\/gimuy;  // 需要对“\”进行转义
        ```

        加载JS脚本后就编译（compilation），更好的性能。
    2. 构造函数

        ```javascript
        new RegExp('\\\\', 'gimuy');    // 要对两个“\”都进行转义

        new RegExp(/\\/, 'gimuy');      // 需要对“\”进行转义
        ```

        运行JS脚本时编译（runtime compilation），传参数情况下才使用。

        >使用字符串字面量时，需要[字符串字面量的转义](https://github.com/realgeoffrey/knowledge/blob/master/网站前端/前端内容/标准库文档.md#js特殊字符)。
2. 标志（修饰符）

    1. `g`：

        global，找到所有匹配而不是在第一个匹配后停止（影响lastIndex），默认每次都重新匹配。

        >1. 如果不带g，在正则过程中，字符串是从左至右匹配的，如果匹配成功就不再继续向右匹配了；如果带g，它会从头到尾的把正确匹配的字符串挑选出来。
        >2. 影响正则相关方法的结果。
    2. `i`：

        ingore case，忽略大小写，默认大小写敏感。
    3. `m`：

        multiple lines，多行搜索，默认单行搜索。
    4. `u`：

        Unicode，用来正确处理大于`\uFFFF`的Unicode字符（对于大于`\uFFFF`的字，把4字节的字符当做单独1个字来解析，否则当做2个2字节的字），把`\u{}`当做Unicode（否则当做正则内容）。
    5. `y`：

        sticky，必须从上一次匹配成功的下一个位置开始（`lastIndex`），作用是达到`^`在全局匹配中都有效。
    6. `s`：
    
        使得`.`可以匹配任意单个字符（包括终止符）。

### 特殊字符
在正则表达式`/内容/`中的内容：

1. 需要转义的特殊字符

    `(` `)` `[` `]` `{` `}` `\` `/` `^` `$` `|` `?` `*` `+` `.`

    因为有特殊含义，所以在使用以上元字符的字面值时必须对其进行`\`转义。

    >可以给除了特殊字符的所有字符前面添加`\`（不建议的写法）。
2. 元字符

    1. 字符类别

        | 字符 | 表示的意义 |
        | :--- | :--- |
        | `.` | 匹配除~~终止符~~以外的任意字符（终止符：`\u000a`换行符`\n`、`\u000d`回车符`\r`、`\u2028`行分隔符、`\u2029`段分隔符）。 |
        | `\d` | 匹配数字。等价于：`[0-9]`。 |
        | `\D` | 匹配不是数字。等价于：`[^0-9]`。 |
        | `\w` | 匹配字母、数字、下划线。等价于：`[A-Za-z0-9_]`。 |
        | `\W` | 匹配不是字母、数字、下划线。等价于：`[^A-Za-z0-9_]`。 |
        | `\s` | 匹配不可见字符，包括空格、制表符、换页符、换行符和其他Unicode空格。等价于：`[ \f\n\r\t\v\u00a0\u1680\u180e\u2000-\u200a\u2028\u2029\u202f\u205f\u3000\ufeff]`。 |
        | `\S` | 匹配可见字符。等价于：`[^ \f\n\r\t\v\u00a0\u1680\u180e\u2000-\u200a\u2028\u2029\u202f\u205f\u3000\ufeff]`。 |
        | `\t` | 匹配水平制表符（tab）。 |
        | `\r` | 匹配回车符（carriage return）。 |
        | `\n` | 匹配换行符（linefeed）。 |
        | `\v` | 匹配垂直制表符（vertical tab）。 |
        | `\f` | 匹配换页符（form-feed）。 |
        | `[\b]` | 匹配退格符（backspace）。 |
        | `\0` | 匹配 NUL 字符。不要在此后面跟小数点。 |
        | `\cA至Z的一个字母` | 匹配字符串中的一个控制字符。如`/\cM/`匹配字符串中的control-M。 |
        | `\x两个16进制数` | 匹配16进制数指定的字符。 |
        | `\u四个16进制数` | 匹配16进制数指定的UTF-16字符。 |
        | `\u{四至五个16进制数}` | 匹配16进制数指定的字符。 |
        | `\其他字符` | 匹配（转义）特殊字符。 |
    2. 字符集合

        | 字符 | 表示的意义 |
        | :--- | :--- |
        | `[xyz]`或`[x-z]` | 匹配集合中的任意一个字符。也以使用连字符`-`指定一个连续范围。 |
        | `[^xyz]`或`[^x-z]` | 匹配任意不在括号内的字符。也可通过使用连字符`-`指定一个范围内的字符。 |
    3. 边界

        | 字符 | 表示的意义 |
        | :--- | :--- |
        | `^` | 匹配输入`/`字符串的开始。如果多行（multiline）标志被设为 true，该字符也会匹配一个断行（line break）符后的开始处。 |
        | `$` | 匹配输入`/`字符串的结尾。如果多行（multiline）标志被设为 true，该字符也会匹配一个断行（line break）符的前的结尾处。 |
        | `\b` | 匹配一个零宽单词边界（zero-width word boundary），如一个字母与一个空格之间。如/\ba/匹配'aaa aaa'中的第一和第四的'a'。它不匹配任何内容，因此`/\w\b\w/`将不能匹配任何字符串，因为一个单词中的字符永远也不可能被一个非词汇字符与一个词汇字符同时紧跟。 |
        | `\B` | 匹配一个零宽非单词边界（zero-width non-word boundary），如两个字母之间或两个空格之间。如/\Ba/匹配'aaa aaa'中的第二、第三、第五和第六的'a'。 |
    4. 分组

        | 字符 | 表示的意义 |
        | :--- | :--- |
        | `(整体)` | 匹配整体并且捕获匹配项。 这被称为捕获括号（capturing parentheses）。 |
        | `(?:整体)` | 匹配整体不会捕获匹配项。这被称为非捕获括号（non-capturing parentheses）。 |
        | `\一个正整数` | 一个反向引用（back reference），指向正则表达式中第 n 个捕获匹配项中匹配的字符串。 |

        >括号分组可以嵌套，外部的分组比内部分组的编号靠前。
        >
        >`RegExp.$1`~`RegExp.$9`值为String类型，返回上一次正则表达式匹配中第n个分组所匹配的文本。
    5. 数量词

        | 字符 | 表示的意义 |
        | :--- | :--- |
        | `x?` | 匹配前面的模式x的0或1次。等价于：`{0,1}`。 |
        | `x*` | 匹配前面的模式x的0或多次。等价于：`{0,}`。 |
        | `x+` | 匹配前面的模式x的1或多次。等价于：`{1,}`。 |
        | `x{一个正整数}` | 前面的模式x的连续出现n次时匹配。 |
        | `x{一个正整数,}` | 前面的模式x的连续出现至少n次时匹配。 |
        | `x{一个正整数,一个正整数}` | 前面的模式x的连续出现至少n次，至多m次时匹配。 |
        | `x??` `x*?` `x+?` `x{n}?` `x{n,}?` `x{n,m}?` | 惰性模式，只进行最小限度的匹配（不加`?`则默认贪婪模式）。 |
        | `x\|y` | 匹配x或y。 |
        | `x(?=y)` | 只有当x后面紧跟着y时，才匹配x。 |
        | `x(?!y)` | 只有当x后面不是紧跟着y时，才匹配x。 |

        >贪婪模式：趋向于最大长度匹配。当贪婪匹配之后还有需要匹配的内容，则回退缩短贪婪匹配内容（回溯）。

    >正则表达式测试：[测试模拟](http://regexr.com/)、[图像解释](https://regexper.com/)。

### 正则函数
1. RegExp对象的方法

    1. `regexp.test(str)`：

        判断正则表达式与指定的字符串是否匹配。返回`true`或`false`。
    2. `regexp.exec(str)`：

        >返回结果与`str.match(regexp)`带`g`标志的结果相同。

        指定的一段字符串执行搜索匹配操作。返回`数组`或`null`。

        如果成功匹配，返回一个数组，并且更新正则表达式对象的属性。返回的数组包括匹配的字符串作为第一个元素，紧接着多个元素对应被捕获的字符串，数组还额外拥有`index`和`input`属性。

        ><details>
        ><summary>e.g.</summary>
        >
        >```javasscript
        >var regexp = /quick\s(brown).+?(jumps)/ig;
        >var arr = regexp.exec('The Quick Brown Fox Jumps Over The Lazy Dog');
        >```
        >
        >产生的结果：
        >
        >| 返回对象[属性/索引] | 描述 | 例子中的值 |
        >| :--- | :--- | :--- |
        >| `arr[0]` | 匹配得到的字符串 | `Quick Brown Fox Jumps` |
        >| `arr[1]`~`arr[n]` | 捕获项 | `arr[1] = Brown``arr[2] = Jumps` |
        >| `arr.index` | 匹配到的字符位于原始字符串的基于0的索引值 | `4` |
        >| `arr.input` | 原始字符串 | `The Quick Brown Fox Jumps Over The Lazy Dog` |
        >
        >| 正则对象[属性/索引] | 描述 | 例子中的值 |
        >| :--- | :--- | :--- |
        >| `regexp.lastIndex` | 下一次匹配开始的位置 | `25` |
        >| `regexp.ignoreCase` | 是否使用了`i`标记 | `true` |
        >| `regexp.global` | 是否使用了`g`标记 | `true` |
        >| `regexp.multiline` | 是否使用了`m`标记 | `false` |
        >| `regexp.source` | 正则模式的字符串 | `quick\s(brown).+?(jumps)` |
        ></details>
    3. 正则表达式的属性：

        1. `source`
        
            当前正则表达式对象的模式文本的字符串，该字符串不会包含正则字面量两边的斜杠以及任何标志字符，并会自动添加字符串的`\`转义（基本仅对`\`、`"`、`'`转义）。
    
            <details>
            <summary>e.g.</summary>
            
            ```javascript
            /\w/g.source === '\\w';                     // true
            new RegExp('\\w', 'g').source === '\\w';    // true
            ```
            </details>
        2. `flags`
        
            返回修饰符。
        3. `sticky`
        
            是否设置了`y`修饰符。

        4. `dotAll`
        
            是否设置了`s`修饰符。
2. String对象的方法

    1. `str.match(regexp)`：

        当字符串匹配到正则表达式时，方法返回`匹配项`，否则返回`null`。

        匹配到的情况：

        1. 如果正则表达式没有`g`标志，返回和`regexp.exec(str)`相同的结果的`数组`（包含匹配的字符串、捕获项，还拥有`index`和`input`属性）。

        2. 如果正则表达式包含`g`标志，返回一个`包含所有匹配结果的数组`。
    2. `str.search(regexp)`：

        判断字符串对象与一个正则表达式是否匹配，返回`正则表达式在字符串中首次匹配项的索引`，否则返回`-1`。
    3. `str.replace(regexp|substr, newSubStr|function[, flags])`：

        方法使用一个替换值（replacement）替换掉一个匹配模式（pattern）在原字符串中某些或所有的匹配项，并返回替换后的`字符串`（不改变调用的字符串对象）。

        被替换的参数`newSubStr`值类型：

        1. 使用字符串作为参数

            替换的字符串如果拥有以下内容，则会被解析成特殊内容：

            | 变量名 | 代表的值 |
            | :--- | :--- |
            | `$$` | 插入一个`$`。 |
            | `$&` | 插入匹配的子串。 |
            | ``$` `` | 插入当前匹配的子串左边的内容。 |
            | `$'` | 插入当前匹配的子串右边的内容。 |
            | `$n` | 若第一个参数是RegExp对象，并且n是个十进制的数字，则插入第n个捕获匹配项的内容。 |

            >e.g. `str.replace(regexp, '$$ $& $1');`，特殊字符就是替换字符串的一部分。

        2. 使用函数作为参数

            当匹配执行后，该函数就会执行，函数的返回值作为替换字符串。

            >若第一个参数是正则表达式且为全局匹配模式，则这个方法将被多次调用、每次匹配都会被调用。

            函数参数为：

            | 参数顺序 | 代表的值 |
            | :--- | :--- |
            | `arguments[0]` | 匹配的子串。 |
            | `arguments[1]`~`arguments[n]` | 若replace()方法的第一个参数是一个RegExp对象，则代表第n个捕获匹配项的内容。 |
            | 倒数第二个参数 | 匹配到的子字符串在原字符串中的偏移量。 |
            | 倒数第一个参数 | 被匹配的原字符串。 |
    4. `str.split([separator][, limit])`：

        以separator为间隔，把字符串分割成一个字符串`数组`。

        1. `separator`：可以是一个字符串或正则表达式，指定用来分割。 如果忽略参数，则返回整个字符串的数组形式;如果是一个空字符串，则返回单字符组成的数组。
        2. `limit`：一个整数，限定返回的分割片段数量。
