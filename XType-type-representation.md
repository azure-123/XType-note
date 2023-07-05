- [ ] 疑问：“通过网络消息传递数据类型，以便应用程序可以动态地发现彼此的类型或评估诸如is-assignable-from等关系是真还是假。”（7.3）所以这个is-assignable-from关系到底是在什么地方完成的呢？

#### IDL注释

annotation的作用范围：构造类型、bitmask、enumeration字面值、聚合类型的成员

##### 成员ID分配

两种分配方式：显式分配、自动分配

###### 显式分配

显式分配成员ID需要使用@id或者@hashid的annotation

- @id注释在[IDL]的子条款8.3.1.1中定义。它应该将成员ID分配给注释参数中指定的值。
- @hashid注释如下所定义。它应该将成员ID分配给从成员名称或注释参数中指定的字符串派生出的值。

```idl
@annotation hashid{
	string value default ""
}
```

###### 自动分配

- @autoid(SEQUENTIAL)：成员ID顺序分配

- @autoid(HASH)：成员ID使用哈希算法计算


当一个类型使用@autoid注释，而该类型的新版本更改了成员的名称时，@hashid注释非常有用。该注释的值可以设置为旧成员的名称，从而导致两个成员被分配相同的哈希值作为它们的ID。

- [ ] 疑问：此处与前面的is-assignable-from概念冲突，当时的规定是相同名字的成员ID相同，相同ID成员的名字相同，这里如果改变名称是否就不满足is-assignable-from的概念了？

如果注释在没有任何参数或者参数为空字符串的情况下使用，则成员ID应该是成员名称的哈希值

##### 可选成员

@optional

##### key成员

@key

##### 可扩展性

@final

@appendable

@mutable

如果给定类型的表示不指示类型的可扩展性，则应将该类型视为appendable。如果未指定值的注释，或者将值设置为USE_DEFAULT，则行为将设置为使用默认值。