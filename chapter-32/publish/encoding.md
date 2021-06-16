# 强制指定编码方式

出于跨平台的考虑，习惯上 Mod 编译时使用 UTF-8 编码方式对字符进行编码。
但是 Windows 家族的操作系统的默认编码方式不是 UTF-8。
这可能会导致构建时因为出现所谓的“非法字符”而导致构建失败。你可以通过在 `build.gradle` 的结尾加上这样一段来强制指定编码方式：

```groovy
tasks.withType(JavaCompile) {  
    options.encoding = "UTF-8"  
}
```

## `JavaCompile`？

是的，这个选项是在 `JavaCompile` 这一个 task type 下的。
如果你的 Mod 里还有别的类型的源码，换成对应的 task type 即可。