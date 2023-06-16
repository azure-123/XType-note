##### 类型进化

比如有两个版本的系统，第一个版本定义了一个类型VehicleLocationType，用IDL定义如下：

```c++
// Initial Version
struct VehicleLocationType {
 float latitude;
 float longitude;
};
```

后来，系统更新迭代有了新的版本，需要对这个类型有更加详细的定义，需要增加成员变量，在这里的例子是增加两个检测错误的字段和位置速度信息，则在新版本中定义IDL如下：

```c++
// New version
struct VehicleLocationType {
 float latitude;
 float longitude;
 float latitude_error_estimate; // added field
 float longitude_error_estimate; // added field
 float direction; // added field
 float speed; // added field
};
```

为了实现版本兼容，当拥有不同版本的组件通信时，需要固定的规则进行特殊处理。

就上面的例子来说，有以下两种情况：

- DataWriter版本initial version，DataReader版本new version，则DataReader会给多出来的字段赋一些缺省值或者被告知这些字段没有提供（如何告知目前没有说明）
- DataWriter版本new version，DataReader版本initial version，则DataReader会将多余字段剥除

- [ ] 转换细节看XType条款7.2.4

##### 类型继承

还是以VehicleLocationType为例，定义一个拓展版本VehicleLocation3DType

```C++
// Extended Location
struct VehicleLocation3DType : VehicleLocationType {
 float altitude;
 float vertical_speed;
};
```

这也有两个情况：

- 期望读取VehicleLocation的DataReader可以接受VehicleLocation3DType和其基类型
- 期望读取VehicleLocation3DType的DataReader可以接受VehicleLocation类型，对于那些拓展的字段，不采用任何值或者使用缺省值，取决于类型的定义
