#### 数据表示

参考代码：cycloneDDS

##### OMG CDR形式扩展

能够适应可选成员和可附加/可变类型

##### XCDR两种编码格式

PLAIN_CDR：引入了扩展以处理可选成员、位掩码和映射。

PL_CDR：利用了RTPS参数列表编码来处理可变类型。

###### PLAIN_CDR

primitive types、character data、enumerated types、map、structures

在序列化数据子消息元素之后，应添加填充字节，以使下一个子消息相对于RTPS消息的开头具有4字节的偏移量。XType要求序列化数据末尾添加的任何填充字节都应设置为零。

原始类型遵循传统CDR：

- 序列化数据应该编码到与原始类型大小对齐的偏移量上。
- 如果本地系统字节序与当前配置在XCDR流（XCDR.cendian）中的字节序不同，则应执行字节交换。

*对齐方式注意：*

*对于1字节数据，为了调整输出流以容纳更多数据，输出流缓冲区以4K为单位扩展，通过dds_cdr_resize实现；*

*对于小于等于4字节的数据，返回其本身大小的对齐方式，对于大于4字节的数据，需要进行8字节（XCDR1）/4字节（XCDR2）对齐，获取对齐的格式的操作，通过dds_cdr_get_align实现；*

*对于以上对齐后的数据本身不是对齐的，需要进行填充操作，通过dds_cdr_alignto_clear_and_resize实现*。

**boolean类型**（DDS_OP_VAL_BLN）

1字节，通过dds_os_put1BO写入，缓冲区以4K为单位扩展，索引加1

**1 byte simple type类型**（DDS_OP_VAL_1BY）

1字节，通过dds_os_put1BO写入，缓冲区以4K为单位扩展，索引加1

**2 byte simple type类型**（DDS_OP_VAL_2BY）

2字节，通过dds_os_put2BO写入，缓冲区以4K为单位扩展，由于小于4字节，所以按照2字节对齐，无需填充，索引加2

**4 byte simple type类型**（DDS_OP_VAL_4BY）

4字节，通过dds_os_put4BO写入，缓冲区以4K为单位扩展，由于等于4字节，所以按照4字节对齐，无需填充，索引加4

**8 byte simple type类型**（DDS_OP_VAL_8BY）

8字节，通过dds_os_put4BO写入，缓冲区以4K为单位扩展，由于8字节，所以进行8字节（XCDR1）/4字节（XCDR2）对齐，无需填充，在写入时，需要考虑LE和BE，索引加8

**enum类型**（DDS_OP_VAL_ENU）

值大于最大值（ops[2]）：false

提取枚举类型的存储大小，1/2/4字节的写入参考1/2/4 byte simple type类型，其他大小都abort

**bitmask类型**（DDS_OP_VAL_BMK）

提取枚举类型的存储大小，判断值的高低位是否与bits_h（ops[2]）和bits_l（ops[3]）相匹配（不匹配false）,1/2/4/8字节的写入参考1/2/4/8 byte simple type类型，其他大小都abort

**string类型**（DDS_OP_VAL_STR）

按照4 byte方式写入string的长度，即长度为4字节

string为NULL：按照1 byte方式写入一个0

string不为NULL：调整输出流大小，写入string

**bounded string类型**（DDS_OP_VAL_BST）

同string类型

**array类型**（DDS_OP_VAL_ARR）

提取子类型（boolean、1/2/4/8 byte、enum、bitmask，其余子类型均abort）

1、子类型为boolean、1/2/4/8 byte

获取子类型大小以后，根据该大小获得其对齐方式（参考boolean、1/2/4/8 byte的对齐方式），以num*elem_size为大小进行对齐和填充操作（参考dds_cdr_alignto_clear_and_resize），把array内容写入缓冲区（本来还有一个交换字节序的步骤，但是此处不需要，故函数没有实现这部分）

2、子类型为enum

若为XCDR2，预留它的DHEADER空间（4字节）

判断枚举类型的存储大小（1/2/4 byte，其他大小abort），一个个将enum类型的元素写进去，参考enum类型的写入。

若为XCDR2，写入DHEADER内容

3、子类型为bitmask

若为XCDR2，预留它的DHEADER空间（4字节）

判断枚举类型的存储大小（1/2/4/8 byte，其他大小abort），一个个将bitmask类型的元素写进去，参考bitmask类型的写入（需考虑高低位匹配问题）。

若为XCDR2，写入DHEADER内容

**external类型**（DDS_OP_VAL_EXT）

递归调用写入操作，将external类型的数据分别写入。

*！map类型尚未在代码中找到*

*！sequence、bounded_sequence、union、struct没有实现*

*!XType协议中在TypeIdentifier和TypeObject部分提及SMALL和LARGE概念，没有在代码中找到相应部分*

###### PL_CDR

基于[RTPS]中定义的RTPS参数列表CDR编码

参数列表数据结构中的每个元素或参数都是一个CDR封装的数据块。每个参数之前都有一个参数头，由两个字节的参数ID和两个字节的参数长度组成。一个参数跟随另一个参数，直到到达列表终止标志。

参数ID：16位（2位宽的位掩码+14位宽无符号整数）

• 位掩码的第一位 - 整个16位宽参数ID的最高有效位 - 表示参数是否具有实现特定的解释。本规范将此位称为FLAG_IMPL_EXTENSION。
• 位掩码的第二位表示，如果消费方实现未能识别该参数，参数是被简单地忽略，还是导致整个数据样本被丢弃。本规范将此位称为FLAG_MUST_UNDERSTAND。

##### XCDR2三种编码格式

PLAIN_CDR2、DELIMITED_CDR和PL_CDR2

PLAIN_CDR2：所有原始类型、字符串和枚举类型。编码类似于PLAIN_CDR，不同之处在于INT64、UINT64、FLOAT64和FLOAT128被序列化到CDR缓冲区的偏移量上，这些偏移量与PLAIN_CDR中的情况不同，被对齐到4而不是8。

DELIMITED_CDR：带有APPENDABLE扩展性类型的类型。

PL_CDR2：带有MUTABLE扩展性类型的聚合类型。与DELIMITED_CDR类似，它在序列化对象之前也会序列化一个DHEADER。此外，它还在每个序列化成员之前序列化一个成员头（EMHEADER）。成员头编码了成员ID、must-understand标志和接下来的序列化成员的长度。
