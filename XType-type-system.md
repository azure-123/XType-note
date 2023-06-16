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

- 订阅主题“车辆位置”的应用程序可以接收到从写入 VehicleLocation3DType 的发布者那里发送的数据，即应用程序可以写入扩展类型并读取基本类型。换句话说，应用程序可以写入扩展类型并读取基础类型。
- 期望读取VehicleLocation3DType的DataReader可以接受VehicleLocation类型，对于那些拓展的字段，不采用任何值或者使用缺省值，取决于类型的定义

##### 稀疏类型

类型系统支持稀疏类型，其中一个类型可以有被标记为“可选”的字段，以便数据表示可以省略这些字段。为了节省网络带宽，也可以省略非可选字段的值，在这种情况下，服务将代表应用程序自动填充默认值。

- [ ] 没太理解
