#### Cdr.h

##### Cdr类

提供序列化/反序列化基础类型接口

###### CdrType

两种CDR序列化方式（CORBA_CDR, DDS_CDR）

###### DDSCdrPlFlag

是否带有parameter list

###### Endianness

大头端or小头端

###### state类

存储CDR序列化的当前状态

m_currentPosition：状态创建时在buffer中的位置

m_alignPosition：状态创建时计算的对其位置

m_swapBytes：是否需要交换字节

m_lastDataSize：存储在状态创建时序列化/反序列化的最后数据大小

###### read_encapsulation

- 函数名称：read_encapsulation
- 函数作用：读取CDR流的封装。如果CDR流包含封装，则在开始反序列化之前应调用此函数。
- 返回值：eprosima::fastcdr::Cdr对象的引用。
- 异常：当尝试反序列化超出内部内存大小的位置时，会抛出exception::NotEnoughMemoryException异常；当尝试反序列化无效值时，会抛出exception::BadParamException异常。

###### serialize_encapsulation

- 函数名称：serialize_encapsulation
- 函数作用：将CDR流的封装写入到缓冲区中。如果CDR流应该包含封装，则在开始序列化之前应该调用此函数。
- 返回值：eprosima::fastcdr::Cdr对象的引用。
- 异常：如果尝试序列化超出内部内存大小的位置，则会抛出exception::NotEnoughMemoryException异常。

###### getDDSCdrPlFlag

- 函数名称：getDDSCdrPlFlag
- 函数作用：DDS_CDR时，获取参数列表标志。

###### setDDSCdrPlFlag

- 函数名称：setDDSCdrPlFlag
- 函数作用：DDS_CDR时，设置参数列表标志。

###### getDDSCdrOptions

- 函数名称：setDDSCdrOptions
- 函数作用：DDS_CDR时，获取选项标志。

###### setDDSCdrOptions

- 函数名称：setDDSCdrOptions
- 函数作用：DDS_CDR时，设置选项标志。

###### changeEndianness

- 函数名称：changeEndianness
- 函数作用：设置当前的大小头端。

###### jump

- 函数名称：jump
- 函数作用：在CDR流缓冲区当中跳过一定数量的字节。

###### reset

- 函数名称：reset
- 函数作用：将缓冲区中的当前位置重置到最开头。

###### getBufferPointer

- 函数名称：getBufferPointer
- 函数作用：返回当前使用缓冲区的指针。

###### getCurrentPosition

- 函数名称：getCurrentPosition
- 函数作用：返回当前CDR流中的位置

###### getSerializedDataLength

- 函数名称：getSerializedDataLength
- 函数作用：返回流中的序列化数据长度

###### alignment

- 函数名称：alignment
- 函数作用：计算将一个位置对对齐到特定数据大小所需的字节数

###### getState

- 函数名称：getState
- 函数作用：返回CDR序列化过程的当前状态

###### setState

- 函数名称：setState
- 函数作用：设置一个先前的CDR序列化过程的状态

###### serialize

类型：uint8_t、char、int8_t、uint16_t、int16_t、uint32_t、int32_t、wchar_t、uint64_t、int64_t、float、double、long double、bool、string、wstring、array（调用serializeArray）、sequence（调用serializeArray）、map

