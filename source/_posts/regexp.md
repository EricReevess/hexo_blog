---
title: JavaScript正则表达式学习笔记
tags:
- "JavaScript"
- "正则表达式"
- "学习笔记"
categories: 
 - JavaScript
 
keywords: "前端, JavaScript, 正则表达式"
cover: https://codesource.io/wp-content/uploads/2020/01/Regular-Expressions-RegExp-in-Javascript.png
---

## 否定字符集

字符集放置在`[]`内部，要创建`否定字符集`，你需要在开始括号后面和不想匹配的字符前面放置`插入字符`（即`^`）。

例如，`/[^aeiou]/gi`匹配所有非元音字符。注意，字符`.`、`!`、`[`、`@`、`/`和空白字符等也会被匹配

```js
let quoteSample = "3 blind mice.";
let myRegex = /[^0-9|aeiou]/gi; // 匹配数字以及元音以外的所有字符
let result = quoteSample.match(myRegex); 
```

## 匹配出现一次或多次的字符

匹配出现一次或者连续多次的的字符（或字符组）。它可能出现一次或者多次

使用`+`符号来检查情况是否如此。记住，字符或匹配模式必须一个接一个地连续出现。

例如，`/a+/g`会在`"abc"`中匹配到一个匹配项，并且返回`["a"]`。因为`+`的存在，它也会在`"aabc"`中匹配到一个匹配项，然后返回`["aa"]`。

如果它是检查字符串`"abab"`，它将匹配到两个匹配项并且返回`["a", "a"]`，因为`a`字符不连续，在它们之间有一个`b`字符。最后，因为在字符串`"bcd"`中没有`"a"`，因此找不到匹配项。

## 匹配出现零次或多次的字符

创建一个变量为`chewieRegex`的正则表达式，使用`*`符号在`chewieQuote`中匹配`"A"`及其之后出现的零个或多个`"a"`。你的正则表达式不需要使用修饰符，也不需要匹配引号。

* 你的正则表达式`chewieRegex`应该使用`*`符号匹配`"A"`之后出现的零个或多个`"a"`字符。

* 你的正则表达式`chewieRegex`应该匹配 16 个字符。

* 你的正则表达式应该匹配`"Aaaaaaaaaaaaaaaa"`。

* 你的正则表达式在`"He made a fair move. Screaming about it can't help you."`中不应该匹配任何字符。

* 你的正则表达式在`"Let him have it. It's not wise to upset a Wookiee."`中不应该匹配任何字符

```js
let chewieQuote = "Aaaaaaaaaaaaaaaarrrgh!";
let chewieRegex = /Aa*/g; // 修改这一行
let result = chewieQuote.match(chewieRegex);
```

## 用惰性匹配来查找字符

在正则表达式中，`贪婪`匹配会匹配到符合正则表达式匹配模式的字符串的最长可能部分，并将其作为匹配项返回。另一种方案称为`懒惰`匹配，它会匹配到满足正则表达式的字符串的最小可能部分。

你可以将正则表达式`/t[a-z]*i/`应用于字符串`"titanic"`。这个正则表达式是一个以`t`开始，以`i`结束，并且中间有一些字母的匹配模式。

正则表达式默认是`贪婪`匹配，因此匹配返回为`["titani"]`。它会匹配到适合该匹配模式的最大子字符串。

但是，你可以使用`?`字符来将其变成`懒惰`匹配。调整后的正则表达式`/t[a-z]*?i/`匹配字符串`"titanic"`返回`["ti"]`。

## 匹配所有的字母和数字

使用字符类，你可以使用`[a-z]`搜寻字母表中的所有字母。这种字符类是很常见的，它有一个缩写，但这个缩写也包含额外的字符。

JavaScript 中与字母表匹配的最接近的字符类是`\w`，这个缩写等同于`[A-Za-z0-9_]`。它不仅可以匹配大小写字母和数字，注意，它还会匹配下划线字符（`_`）。

```js
// 返回字符串内英文句子的单词数
let quoteSample = "The five boxing wizards jump quickly .";
let alphabetRegexV2 = /\w+/g; // 修改这一行
let result = quoteSample.match(alphabetRegexV2).length;
console.log(result);
```

## 匹配除了字母和数字的所有符号

你已经了解到可以使用缩写`\w`来匹配字母和数字`[A-Za-z0-9_]`。不过，有可能你想要搜寻的匹配模式与字母数字相反。

你可以使用`\W`搜寻和`\w`相反的匹配模式。注意，相反匹配模式使用大写字母。此缩写与`[^A-Za-z0-9_]`是一样的。

```js
let quoteSample = "The five boxing wizards jump quickly.";
let nonAlphabetRegex = /\W/g; // 修改这一行
let result = quoteSample.match(nonAlphabetRegex).length; // result = 6
```

## 匹配所有非数字

查找非数字字符的缩写是`\D`。这等同于字符串`[^0-9]`，它查找不是 0 - 9 之间数字的单个字符。

## 案例：限制可能的用户名

你需要检查数据库中的所有用户名。以下是用户在创建用户名时必须遵守的一些简单规则。

1) 用户名中的数字必须在最后，且数字可以有零个或多个。

2) 用户名字母可以是小写字母和大写字母。

3) 用户名长度必须至少为两个字符。两位用户名只能使用字母。

```js
let username = "JackOfAllTrades";
let userCheck = /[a-z][a-z]\d*/i;
let result = userCheck.test(username);
```

## 匹配空白字符

迄今为止的挑战包括匹配的字母和数字。你还可以匹配字母之间的空格。

你可以使用`\s`搜寻空格，其中`s`是小写。此匹配模式不仅匹配空格，还匹配回车符、制表符、换页符和换行符，你可以将其视为与`[\r\t\f\n\v]`类似。

## 指定匹配的上限和下限

回想一下，你使用加号`+`查找一个或多个字符，使用星号`*`查找零个或多个字符。这些都很方便，但有时你需要匹配一定范围的匹配模式。

你可以使用`数量说明符`指定匹配模式的上下限。数量说明符与花括号（`{`和`}`）一起使用。你可以在花括号之间放两个数字，这两个数字代表匹配模式的上限和下限。

例如，要在字符串`"ah"`中匹配仅出现`3`到`5`次的字母`a`，你的正则表达式应为`/a{3,5}h/`。

* 匹配在`"Oh no"`中仅出现`3`到`6`次的字母`h`。

```js
let ohStr = "Ohhh no";
let ohRegex = /Oh{3,6} no/;
let result = ohRegex.test(ohStr);
```

## 指定匹配的确切数量

你可以使用带有花括号的`数量说明符`来指定匹配模式的上下限。但有时你只需要特定数量的匹配。

要指定一定数量的匹配模式，只需在大括号之间放置一个数字。

例如，要只匹配字母`a`出现`3`次的单词`"hah"`，你的正则表达式应为`/ha{3}h/`。

## 检查全部或无

有时，你想要搜寻的匹配模式可能有不确定是否存在的部分。尽管如此，你还是想检查它们。

为此，你可以使用问号`?`指定可能存在的元素。这将检查前面的零个或一个元素。你可以将此符号视为前面的元素是可选的

* 匹配美式英语（favorite）和英式英语（favourite）的单词版本。

```js
let favWord = "favorite";
let favRegex = /favou?rite/; // 修改这一行
let result = favRegex.test(favWord);
```

## 正向先行断言和负向先行断言

**先行断言**是告诉 JavaScript 在字符串中向前查找的匹配模式。当你想要在同一个字符串上搜寻多个匹配模式时，这非常有用。

有两种先行断言方式：`正向先行断言`和`负向先行断言`

**正向先行断言**会查看并确保搜索匹配模式中的元素存在，但实际上并不匹配。正向先行断言的用法是`(?=...)`，其中`...`就是需要存在但不会被匹配的部分，每个`(?=...)`都是一个对整个测试对象的测试规则，

另一方面，**负向先行断言**会查看并确保搜索匹配模式中的元素不存在。负向先行断言的用法是`(?!...)`，其中`...`是你希望不存在的匹配模式。如果负向先行断言部分不存在，将返回匹配模式的其余部分。

* **先行断言**的更实际用途是检查一个字符串中的两个或更多匹配模式。这里有一个简单的密码检查器，密码规则是 3 到 6 个字符且至少包含一个数字：

  ```js
  let password = "abc123";
  let checkPass = /(?=\w{3,6})(?=\D*\d)/;
  checkPass.test(password); // Returns true
  ```

* 使用**先行断言**以匹配至少5个字符且有两个连续数字的密码。

  ```js
  let sampleWord = "astronaut";
  // \D* 匹配前面多个非数字字符，这2个(?=) 为串行匹配，意思为，先通过第一个(?=)，然后再通过第二个(?=)，每个
  let pwRegex = /(?=\w{5,})(?=\D*\d{2,})/; 
  let result = pwRegex.test(sampleWord);
  ```

## 使用捕获组重用模式（重要）

如果要搜寻的匹配模式会在字符串中出现多次，那么手动重复该正则表达式不是一个高效的方法。此时就需要用到**捕获组**

你可以使用**捕获组**搜寻重复的子字符串。括号`(`和`)`可以用来匹配重复的子字符串。你只需要把重复匹配模式的正则表达式放在括号中即可。

要指定重复字符串将出现的位置，可以使用反斜杠（`\`）后接一个数字。这个数字从 1 开始，随着你使用的每个捕获组的增加而增加。这里有一个示例，`\1`可以匹配第一个组。

下面的示例匹配任意两个被空格分割的单词：

```js
let repeatStr = "regex regex";
let repeatRegex = /(\w+)\s\1/; // 
repeatRegex.test(repeatStr); // Returns true
repeatStr.match(repeatRegex); // Returns ["regex regex", "regex"]
```

* 此处的\1 为占位符，复用一号位`(\w+)`匹配到的值作为规则在当前位置进行检测
* `(\w+)` 捕获到第一个单词`regex` ，那么后面的\1就以这个`regex`作为当前\1所在位置的规则进行匹配

在字符串上使用`.match()`方法将返回一个数组，其中包含它匹配的字符串及其捕获组。

* 在正则表达式`reRegex`中使用`捕获组`，以匹配在字符串中仅重复三次的数字，每一个都由空格分隔。

  ```js
  let repeatNum = "42 42 42";
  let reRegex =  /^(\d+)\s\1\s\1$/; //使用开头^和结尾$来包裹表达式模板进行严格匹配,\1处复用第一个匹配到的值作为规则
  let result = reRegex.test(repeatNum);
  ```

## 使用捕获组搜索和替换

搜索功能是很有用的。但是，当你的搜索也执行更改（或替换）匹配文本的操作时，搜索功能就会显得更加强大。

可以使用字符串上`.replace()`方法来搜索并替换字符串中的文本。`.replace()`的输入首先是你想要搜索的正则表达式匹配模式，第二个参数是用于替换匹配的字符串或用于执行某些操作的函数。

```js
let wrongText = "The sky is silver.";
let silverRegex = /silver/;
wrongText.replace(silverRegex, "blue");
// Returns "The sky is blue."
```

你还可以使用美元符号（`$`）访问替换字符串中的捕获组。

```js
"Code Camp".replace(/(\w+)\s(\w+)/, '$2 $1');
// Returns "Camp Code"
```

## 案例：删除开头和结尾的空白

```js
let hello = "   Hello, World!  ";
let wsRegex = /^\s+|\s+$/g; // 匹配多个空格开头和结尾的多个空格
let result = hello.replace(wsRegex,'');
```

## 案例：验证或者匹配文本中的邮箱

* 匹配出邮箱

  ```js
  let email = "my email is  ljp123456@sina.com";
  let emailRegex = /\w{5,16}@\D{2,}.[com|net|org|cn]$/; // 修改这一行
  let result = email.match(emailRegex)
  console.log(result);
  [
    'ljp123456@sina.com',
    index: 13,
    input: 'my email is  ljp123456@sina.com',
    groups: undefined
  ]
  ```

  

* 验证邮箱格式

  ```js
  let email = "ljp123456@sina.com";
  let emailRegex = /^\w{5,16}@\D{2,}.[com|net|org|cn]$/; // 修改这一行
  let result = emailRegex.test(email)
  console.log(result);
  ```

## 案例：短线连接格式转换

在这道题目中，我们需要写一个函数，把一个字符串转换为“短线连接格式”。短线连接格式的意思是，所有字母都是小写，且用`-`连接。比如，对于`Hello World`，应该转换为`hello-world`；对于`I love_Javascript-VeryMuch`，应该转换为`i-love-javascript-very-much`。

* `spinalCase("This Is Spinal Tap")`应该返回`"this-is-spinal-tap"`。

* `spinalCase("thisIsSpinalTap")`应该返回`"this-is-spinal-tap"`。

* `spinalCase("The_Andy_Griffith_Show")`应该返回`"the-andy-griffith-show"`。

* `spinalCase("Teletubbies say Eh-oh")`应该返回`"teletubbies-say-eh-oh"`。

* `spinalCase("AllThe-small Things")`应该返回`"all-the-small-things"`

```js
function spinalCase(str) {
  let temp = str.replace(/^\s+|\s+$/,'')
  temp = (temp.charAt(0).toUpperCase() + temp.substring(1)) //将字符串头部
    .replace(/[\-]/g,' ') 
    .match(/\w+/g)
    .map(item => item.charAt(0).toUpperCase() + item.substring(1))
    .join('')
    .match(/[A-Z][a-z]+/g)
    .join('-')
    .toLowerCase()
  return temp
}

spinalCase('This Is Spinal Tap');
```

## 儿童黑话

我们需要写一个函数，把传入的字符串翻译成“儿童黑话”。

[儿童黑话](http://en.wikipedia.org/wiki/Pig_Latin)的基本转换规则很简单，只需要把一个英文单词的第一个辅音字母或第一组辅音从移到单词的结尾，并在后面加上`ay`即可。在英语中，字母 a、e、i、o、u 为元音，其余的字母均为辅音。辅音从的意思是连续的多个辅音字母。

额外地，如果单词本身是以元音开头的，那只需要在结尾加上`way`。

在本题中，传入的单词一定会是英文单词，且所有字母均为小写。

* `translatePigLatin("california")`应该返回 "aliforniacay"。

* `translatePigLatin("paragraphs")`应该返回 "aragraphspay"。

* `translatePigLatin("glove")`应该返回 "oveglay"。

* `translatePigLatin("algorithm")`应该返回 "algorithmway"。

* `translatePigLatin("eight")`应该返回 "eightway"。

* 你的代码应当能够处理第一个元音字母在单词结尾的情况。比如`translatePigLatin("she")`应该返回 "eshay"。

* 你的代码应当能够处理单词中不含元音字母的情况。比如`translatePigLatin("rhythm")`应该返回 "rhythmay"。

```js
function translatePigLatin(str) {
  let a = str.match(/[aeiou]\w*/) // 匹配元音开头且后续字母出现0次或多次的片段
  let b = str.match(/^[^aeiou]+/) // 匹配以元音字母开头且后续至少一次的片段
  // 如果是以字符串
  return /^[aeiou]+/.test(str) ? str + 'way' : (a ? a : '') + (b ? (b + 'ay') : 'way')
}

translatePigLatin("consonant");
```

## 搜索和替换

我们需要写一个字符串的搜索与替换函数，它的返回值为完成替换后的新字符串。

这个函数接收的第一个参数为待替换的句子。第二个参数为句中需要被替换的单词。第三个参数为替换后的单词。

**注意：**
需要保留被替换单词首字母的大小写格式。即如果传入的第二个参数为 "Book"，第三个参数为 "dog"，那么替换后的结果应为 "Dog"

```js
function myReplace (str, before, after) {
  let regExp = new RegExp(`${before}`, 'gi')
  return /[A-Z]/.test(str.match(regExp)[0].charAt(0)) ?
    str.replace(regExp, after.charAt(0).toUpperCase() + after.slice(1))
    :
    str.replace(regExp, after)
}
console.log(myReplace('He is Sleeping on the couch', 'Sleeping', 'sitting'))
```

