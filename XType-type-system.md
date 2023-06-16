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

