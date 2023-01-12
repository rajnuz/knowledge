# idea 提速

- [idea 提速](#idea-提速)
  - [.var](#var)
  - [reload改动](#reload改动)

---

## .var

当多次使用getXXX()获取某个类成员变量时，最好将该变量提取出来，不用每次都用get方法。可以在原本的get方法后加上`.var` 再按tab，会生成一个新的变量（idea的内置功能）
例如 Mapper类有成员变量String name
原本有多处 `mapper.getName()`
快捷改法：

```java
mapper.getName().var 
```

再按tab

## reload改动

当我们在运行或debug时 修改了部分代码，如果方法签名没变，只是改了一些方法的内容，则不需要rerun，而是点击build->Recompile 改动类。会提示是否reload。点击是后，不需要重新rerun即可以新代码继续调试