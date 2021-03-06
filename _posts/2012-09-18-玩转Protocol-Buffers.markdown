---
layout: post
title:  "玩转Protocol Buffers"
date:   2012-09-18 10:09:48
author: 桂南
categories: program
---

## 玩转Protocol Buffers
### by 桂南
### at 2012-09-18 10:09:48
### original <http://www.udpwork.com/item/8132.html>

<h1>1. 人人都爱Protocal Buffers</h1>
<h2>1.1 Protocol Buffers（PB）是什么?</h2>
<p>Protocol buffers are Google’s language-neutral, platform-neutral, extensible mechanism for serializing structured data – <strong>think XML, but smaller, faster, and simpler</strong>
. You define how you want your data to be structured once, then you can use special generated source code to easily write and read your structured data to and from a variety of data streams and using a variety of languages – Java, C++, or Python. You can even update your data structure without breaking deployed programs that are compiled against the “old” format.（摘自<a href="https://developers.google.com/protocol-buffers/?hl=zh-CN">PB官网</a>）</p>
<p>针对英文不太好的同学，除了强烈建议好好学一下英文外（PB的最新文档总是英文的），这里笔者按照自己的理解试着翻译一下：protocol buffers是google提供的一种将结构化数据进行序列化和反序列化的方法，其优点是语言中立，平台中立，可扩展性好，目前在google内部大量用于数据存储，通讯协议等方面。PB在功能上类似XML，但是序列化后的数据更小，解析更快，使用上更简单。用户只要按照proto语法在.proto文件中定义好数据的结构，就可以使用PB提供的工具（protoc）自动生成处理数据的代码，使用这些代码就能在程序中方便的通过各种数据流读写数据。PB目前支持Java, C++和Python3种语言。另外，PB还提供了很好的向后兼容，即旧版本的程序可以正常处理新版本的数据，新版本的程序也能正常处理旧版本的数据。</p>
<h2>1.2 如何使用Protocol Buffers？</h2>
<p>这里以官网<a href="https://developers.google.com/protocol-buffers/docs/cpptutorial?hl=zh-CN">Tutorial</a>的<strong>通讯簿</strong>
例子来简单介绍一下PB的常规使用方式，非常规的使用方式在后面几章逐一介绍
<br>
1.在addressbook.proto文件里定义通讯簿消息的格式，一个通讯簿（AddressBook）由可重复的Person组成，一个person由两个必需存在的name和id字段，以及一个可选的email字段，和可重复的PhoneNumber构成。PhoneNumber由number和type组成。</p>
<pre>
message Person {
  required string name = 1;
  required int32 id = 2;
  optional string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    required string number = 1;
    optional PhoneType type = 2 [default = HOME];
  }

  repeated PhoneNumber phone = 4;
}

message AddressBook {
  repeated Person person = 1;
}
</pre><p>2.使用PB提供的工具 protoc根据.proto文件自动生成处理消息的代码</p>
<pre>
protoc -I=$SRC_DIR --cpp_out=$DST_DIR $SRC_DIR/addressbook.proto
</pre><pre>&lt;/pre&gt;
在$DST_DIR里生成了下面两个文件：
addressbook.pb.h,
addressbook.pb.cc
&lt;pre&gt;</pre><p>3.程序使用生成的代码来读写（序列化，反序列化）和操作（get，set）消息</p>
<pre>
//保存address book
fstream output(argv[1], ios::out | ios::trunc | ios::binary);
address_book.SerializeToOstream(&amp;amp;output))；
</pre><h2>1.3 为什么写这篇文章</h2>
<p>目前网上关于PB的文章大部分只涉及到上面讲的内容， 而实际上PB的能力远不止如此，本文尝试使用PB内建的支持实现自描述消息，动态消息以及两者的结合：动态自描述消息，在此基础上给出一些性能参考和建议。本文以下部分适合对PB有一定使用经验的同学阅读，强烈建议感兴趣的同学在阅读下面章节前再去复习一下Tutorial，因为我会以Tutorial的AddressBook例子来演示自描述消息的实现。
<br>
由于笔者知识有限，本文只涉及C++语言的内容，使用Java和Python的同学可参考下文并阅读官网API Reference自己摸索，应该问题不大，笔者就是这么摸索过来的。
<br>
为了下文介绍的方便，先明确生产者，消费者两个角色。
<br>
<strong>生产者</strong>
：产生消息，填充内容，并序列化保存
<br>
<strong>消费者</strong>
：读取数据，反序列化得到消息，使用消息
<br>
在我们的例子里生产者和消费者均为为独立的程序，消息序列化后保存在文件中。网络通讯的情况类似，请自行推理</p>
<h1>2. 自描述消息</h1>
<h2>2.1 分析</h2>
<p>Tutorial介绍的使用方法要求生产者和消费者在编译时就确定消息格式（.proto文件），生产者和消费者在消息格式上紧耦合。当消息格式发生变化的时候，消费者必须重新编译才能理解新格式。有没有可能解除这种耦合，让消费者能动态的适应消息格式的变换？从原理上进行分析的话发现是可行的。即生产者把定义消息格式的.proto文件和消息作为一个完整的消息序列化保存，完整保存的消息我称之为Wrapper message，原来的消息称之为payload message。消费者把wrapper message反序列化，先得到payload message的消息类型，然后根据类型信息得到payload message，最后通过反射机制来使用该消息。通过这种方式消费者只需要了解这一种wrapper message的格式就能够适应各种payload message的格式。这也是PB官网给出的解决方案：<a href="https://developers.google.com/protocol-buffers/docs/techniques?hl=zh-CN#self-description">Self-describing Messages</a>
<br>
wrapper message的定义如下所示，第一个字段保存payload message的类型信息（由于message可以内嵌message，而.proto文件可以import 其他.proto，所以这里使用FileDescriptorSet），第二个字段是payload message的类型名字符串，第三个字段是payload message序列化后的数据。</p>
<pre>
message SelfDescribingMessage {
  // Set of .proto files which define the type.
  required FileDescriptorSet proto_files = 1;

  // Name of the message type.  Must be defined by one of the files in proto_files.
  required string type_name = 2;

  // The message data.
  required bytes message_data = 3;
}
</pre><h2>2.2 实现</h2>
<p>下面通过改造tutorial例子程序，演示自描述消息的实现方式。</p>
<h3>生产者：add_person.cc</h3>
<p>1.	使用 protoc生成代码时加上参数–descriptor_set_out，输出类型信息(即SelfDescribingMessage的第一个字段内容)到一个文件，这里假设文件名为desc.set，
<br>
protoc –cpp_out=.  –descriptor_set_out=desc.set addressbook.proto
<br>
2.	payload message使用方式不需要修改
<br>
tutorial::AddressBook address_book;
<br>
PromptForAddress(address_book.add_person());//这个函数不需要任何修改
<br>
3.	在保存时使用文件desc.set内容填充SelfDescribingMessage的第一个字段，使用AddressBook
<br>
AddressBook的full name填充SelfDescribingMessage的第二个字段，AddressBook序列化后的数据填充第三个字段。最后序列化SelfDescribingMessage保存到文件中。</p>
<pre>
tutorial::SelfDescribingMessage sdmessage;
fstream desc(argv[2], ios::in | ios::binary);
sdmessage. mutable_proto_files()-&gt;ParseFromIstream(&amp;desc)；
sdmessage.set_type_name((address_book.GetDescriptor())-&gt;full_name());
sdmessage.clear_message_data();
address_book.SerializeToString(sdmessage.mutable_message_data());
fstream output(argv[1], ios::out | ios::trunc | ios::binary);
sdmessage.SerializeToOstream(&amp;output))；
</pre><h3>消费者：list_people.cc</h3>
<p>List_people.cc编译时需要知道SelfDescribingMessage，不需要知道AddressBook，运行时可以正常操作AddressBook消息。
<br>
1.	首先反序列化SelfDescribingMessage</p>
<pre>
tutorial::SelfDescribingMessage sdmessage;
fstream input(argv[1], ios::in | ios::binary);
sdmessage.ParseFromIstream(&amp;input))；
</pre><p>2.	通过第一个字段得到FileDescriptorSet，通过第二个字段取得消息的类型名，使用DescriptorPool得到payload message的类型信息Descriptor</p>
<pre>
SimpleDescriptorDatabase db;
for(int i=0;i&lt;sdmessage.proto_files().file_size();i++)
{    db.Add(sdmessage.proto_files().file(i));  }
DescriptorPool pool(&amp;db);
const Descriptor *descriptor = pool.FindMessageTypeByName(sdmessage.type_name());
</pre><p>3.	使用DynamicMessage new出这个类型的一个空对象，从第三个字段反序列化得到原来的message对象</p>
<pre>
DynamicMessageFactory factory(&amp;pool);
Message *msg = factory.GetPrototype(descriptor)-&gt;New();
msg-&gt;ParseFromString(sdmessage.message_data());
</pre><p>4.	通过Message的reflection接口操作message的各个字段</p>
<h1>3. 动态消息</h1>
<h2>3.1 分析</h2>
<p>自描述消息解放了消费者，那么生产者呢？能否在运行时确定消息格式，动态生成消息呢？从原理上分析发现也是可以的。自描述消息的消费者是从文件中读取消息格式信息，我们只要在运行时构建这样的内容就可以实现动态消息。下面以代码说明，本章节的内容由剑豪提供。
<br>
最终动态生成的消息格式定义如下所示：</p>
<pre>
message pair {
  required string key = 1;
  required uint32 value = 2;
  }
</pre><h2>3.2 实现</h2>
<p>1. 动态定义消息，生成类型信息</p>
<pre>
    FileDescriptorProto file_proto;
    file_proto.set_name(&quot;foo.proto&quot;);

    // create dynamic message proto names &quot;Pair&quot;
    DescriptorProto *message_proto = file_proto.add_message_type();
    message_proto-&gt;set_name(&quot;Pair&quot;);

    FieldDescriptorProto *field_proto = NULL;

    field_proto = message_proto-&gt;add_field();
    field_proto-&gt;set_name(&quot;key&quot;);
    field_proto-&gt;set_type(FieldDescriptorProto::TYPE_STRING);
    field_proto-&gt;set_number(1);
    field_proto-&gt;set_label(FieldDescriptorProto::LABEL_REQUIRED);

    field_proto = message_proto-&gt;add_field();
    field_proto-&gt;set_name(&quot;value&quot;);
    field_proto-&gt;set_type(FieldDescriptorProto::TYPE_UINT32);
    field_proto-&gt;set_number(2);
    field_proto-&gt;set_label(FieldDescriptorProto::LABEL_REQUIRED);
    DescriptorPool pool;
    const FileDescriptor *file_descriptor = pool.BuildFile(file_proto);
    const Descriptor *descriptor = file_descriptor-&gt;FindMessageTypeByName(&quot;Pair&quot;);
    </pre><p>2. 根据类型信息使用DynamicMessage new出这个类型的一个空对象</p>
<pre>
    // build a dynamic message by &quot;Pair&quot; proto
    DynamicMessageFactory factory(&amp;pool);
    const Message *message = factory.GetPrototype(descriptor);

    // create a real instance of &quot;Pair&quot;
    Message *pair = message-&gt;New();
    </pre><p>3.	通过Message的reflection操作message的各个字段</p>
<pre>
    // write the &quot;Pair&quot; instance by reflection
    const Reflection *reflection = pair-&gt;GetReflection();

    const FieldDescriptor *field = NULL;
    field = descriptor-&gt;FindFieldByName(&quot;key&quot;);
    reflection-&gt;SetString(pair, field, &quot;my key&quot;);
    field = descriptor-&gt;FindFieldByName(&quot;value&quot;);
    reflection-&gt;SetUInt32(pair, field, 1234);
    </pre><p>此时动态生成的pair对象内容为</p>
<pre>
key: &quot;my key&quot;
value: 1234
</pre><h2>3.3 代码</h2>
<p>完整代码也不多，直接贴上：</p>
<pre>
#include &lt;iostream&gt;
#include &lt;google/protobuf/descriptor.h&gt;
#include &lt;google/protobuf/descriptor.pb.h&gt;
#include &lt;google/protobuf/dynamic_message.h&gt;

using namespace std;
using namespace google::protobuf;

int main(int argc, const char *argv[])
{
    FileDescriptorProto file_proto;
    file_proto.set_name(&quot;foo.proto&quot;);

    // create dynamic message proto names &quot;Pair&quot;
    DescriptorProto *message_proto = file_proto.add_message_type();
    message_proto-&gt;set_name(&quot;Pair&quot;);

    FieldDescriptorProto *field_proto = NULL;

    field_proto = message_proto-&gt;add_field();
    field_proto-&gt;set_name(&quot;key&quot;);
    field_proto-&gt;set_type(FieldDescriptorProto::TYPE_STRING);
    field_proto-&gt;set_number(1);
    field_proto-&gt;set_label(FieldDescriptorProto::LABEL_REQUIRED);

    field_proto = message_proto-&gt;add_field();
    field_proto-&gt;set_name(&quot;value&quot;);
    field_proto-&gt;set_type(FieldDescriptorProto::TYPE_UINT32);
    field_proto-&gt;set_number(2);
    field_proto-&gt;set_label(FieldDescriptorProto::LABEL_REQUIRED);

    // add the &quot;Pair&quot; message proto to file proto and build it
    DescriptorPool pool;
    const FileDescriptor *file_descriptor = pool.BuildFile(file_proto);
    const Descriptor *descriptor = file_descriptor-&gt;FindMessageTypeByName(&quot;Pair&quot;);
    cout &lt;&lt; descriptor-&gt;DebugString();

    // build a dynamic message by &quot;Pair&quot; proto
    DynamicMessageFactory factory(&amp;pool);
    const Message *message = factory.GetPrototype(descriptor);
    // create a real instance of &quot;Pair&quot;
    Message *pair = message-&gt;New();

    // write the &quot;Pair&quot; instance by reflection
    const Reflection *reflection = pair-&gt;GetReflection();
    const FieldDescriptor *field = NULL;
    field = descriptor-&gt;FindFieldByName(&quot;key&quot;);
    reflection-&gt;SetString(pair, field, &quot;my key&quot;);
    field = descriptor-&gt;FindFieldByName(&quot;value&quot;);
    reflection-&gt;SetUInt32(pair, field, 1234);

    cout &lt;&lt; pair-&gt;DebugString();
    delete pair;
    return 0;
}
</pre><h2>3.4 另一种实现方式：动态编译</h2>
<p>上面是动态消息的一种方式，我们还可以使用PB 提供的 google::protobuf::compiler 包在运行时动态编译指定的.proto 文件来使用其中的 Message。这样就可以通过修改.proto文件实现动态消息，有点类似配置文件的用法。完成这个工作主要的类叫做 importer，定义在 importer.h 中。
<br>
Foo.proto内容如下：</p>
<pre>
message Pair {
    required string key = 1;
    required uint32 value = 2;

}
</pre><p>下面的代码实现同样的动态消息：</p>
<pre>
#include &lt;iostream&gt;
#include &lt;google/protobuf/descriptor.h&gt;
#include &lt;google/protobuf/descriptor.pb.h&gt;
#include &lt;google/protobuf/dynamic_message.h&gt;
#include &lt;google/protobuf/compiler/importer.h&gt;

using namespace std;
using namespace google::protobuf;
using namespace google::protobuf::compiler;

int main(int argc, const char *argv[])
{
    DiskSourceTree sourceTree;
    //look up .proto file in current directory
    sourceTree.MapPath(&quot;&quot;, &quot;./&quot;);
    Importer importer(&amp;sourceTree, NULL);
    //runtime compile foo.proto
    importer.Import(&quot;foo.proto&quot;);

    const Descriptor *descriptor = importer.pool()-&gt;FindMessageTypeByName(&quot;Pair&quot;);
    cout &lt;&lt; descriptor-&gt;DebugString();

    // build a dynamic message by &quot;Pair&quot; proto
    DynamicMessageFactory factory;
    const Message *message = factory.GetPrototype(descriptor);
    // create a real instance of &quot;Pair&quot;
    Message *pair = message-&gt;New();

    // write the &quot;Pair&quot; instance by reflection
    const Reflection *reflection = pair-&gt;GetReflection();

    const FieldDescriptor *field = NULL;
    field = descriptor-&gt;FindFieldByName(&quot;key&quot;);
    reflection-&gt;SetString(pair, field, &quot;my key&quot;);
    field = descriptor-&gt;FindFieldByName(&quot;value&quot;);
    reflection-&gt;SetUInt32(pair, field, 1111);

    cout &lt;&lt; pair-&gt;DebugString();

    delete pair;

    return 0;
}
</pre><h1>4. 动态自描述消息</h1>
<h2>4.1 分析</h2>
<p>好了，到此为止我们已经可以通过自描述消息解放消费者，通过动态消息解放生产者。最后介绍的大杀器是两者的结合：动态自描述消息，彻底解放生产者和消费者。
<br>
仍以上面的消息为例说明：</p>
<pre>
message pair {
  required string key = 1;
  required uint32 value = 2;
  }
</pre><p>这次我们不使用第二章介绍的wrapper message方式，改为通过文件格式约定实现自描述，网络通信协议可参考这种方式。
<br>
生产者和消费者商定文件格式如下：
<br>
<a href="http://www.searchtb.com/wp-content/uploads/2012/09/%E5%8A%A8%E6%80%81%E8%87%AA%E6%8F%8F%E8%BF%B0PB%E4%BF%A1%E6%81%AF0.jpg"><img src="http://www.searchtb.com/wp-content/uploads/2012/09/%E5%8A%A8%E6%80%81%E8%87%AA%E6%8F%8F%E8%BF%B0PB%E4%BF%A1%E6%81%AF0.jpg" alt="动态自描述PB信息"></a></p>
<h2>4.2 实现</h2>
<h3>生产者</h3>
<p>1.	动态定义消息，生成类型信息;根据类型信息生成一个空的message对象;通过Message的reflection操作message的各个字段。这些和动态消息处理一致，这里就不赘述了。
<br>
2.	使用CodedOutputStream写文件，依次保存如下信息：
<br>
a)	MAGCI_NUM, 消费者可以用来验证文件格式是否一致或者格式是否错误。
<br>
b)	FileDescriptorProto序列化后数据的size
<br>
c)	序列化的FileDescriptorProto数据
<br>
d)	Payload message序列化后数据的size
<br>
e)	序列化的Payload message数据
<br>
代码如下：</p>
<pre>
    const unsigned int MAGIC_NUM=2988;
    int fd = open(&quot;dpb.msg&quot;, O_WRONLY|O_CREAT,0666);
    ZeroCopyOutputStream* raw_output = new FileOutputStream(fd);
    CodedOutputStream* coded_output = new CodedOutputStream(raw_output);

    coded_output-&gt;WriteLittleEndian32(MAGIC_NUM);
    string data;
    file_proto.SerializeToString(&amp;data);
    coded_output-&gt;WriteVarint32(data.size());
    coded_output-&gt;WriteString(data);

    data.clear();
    pair-&gt;SerializeToString(&amp;data);
    coded_output-&gt;WriteVarint32(data.size());
    coded_output-&gt;WriteString(data);;

    delete coded_output;
    delete raw_output;
    close(fd);
    </pre><h3>消费者</h3>
<p>1.	使用CodedInputStream读取文件，先通过MAGIC_NUM判断文件格式是否正确，然后反序列化FileDescriptorProto，得到payload message的类型信息</p>
<pre>
    FileDescriptorProto file_proto;
    int fd = open(&quot;dpb.msg&quot;, O_RDONLY);
    ZeroCopyInputStream* raw_input = new FileInputStream(fd);
    CodedInputStream* coded_input = new CodedInputStream(raw_input);
    unsigned int magic_number;
    coded_input-&gt;ReadLittleEndian32(&amp;magic_number);
    if (magic_number != MAGIC_NUM) {
            cerr &lt;&lt; &quot;File not in expected format.&quot; &lt;&lt; endl;
            return 1;
    }

    uint32 size;
    coded_input-&gt;ReadVarint32(&amp;size);

    char* text = new char[size + 1];
    coded_input-&gt;ReadRaw(text, size);
    text[size] = &#39;\0&#39;;
    file_proto.ParseFromString(text);
    DescriptorPool pool;
    const FileDescriptor *file_descriptor = pool.BuildFile(file_proto);
    const Descriptor *descriptor = file_descriptor-&gt;FindMessageTypeByName(&quot;Pair&quot;);
    </pre><p>2.	使用DynamicMessage new出这个类型的一个空对象，从文件中读取messagedata反序列化得到原来的message
<br>
DynamicMessageFactory factory(&amp;pool);</p>
<pre>
   const Message *message = factory.GetPrototype(descriptor);

    // create a real instance of &quot;Pair&quot;
    Message *pair = message-&gt;New();
    coded_input-&gt;ReadVarint32(&amp;size);
    text = new char[size + 1];
    coded_input-&gt;ReadRaw(text, size);
    text[size] = &#39;\0&#39;;
    pair-&gt;ParseFromString(text);
    </pre><p>3.	通过Message的reflection即可操作message的各个字段</p>
<h1>5. 天下没有免费的午餐</h1>
<p>自描述和动态生成得到的灵活性不是免费的午餐，那么下面我们就以文中的例子来分析一下动态自描述消息相对静态消息在空间和时间上的变化。
<br>
1.	空间：由于PB主要用于数据存储和通讯协议，下面分别分析：</p>
<p>以Tutorial中的AddressBook为例分析数据存储的使用场景，添加如下两条记录：</p>
<pre>
Person ID: 1
  Name: Peter
  E-mail address: peter@gmail.com
  Home phone #: 13777777777
  Work phone #: 13788888888
  Mobile phone #: 13799999999
Person ID: 2
  Name: Tom
  E-mail address: tom@gmail.com
   Mobile phone #: 13888888888
</pre><table border="1"><tbody><tr><td>使用方式</td>
<td>内容</td>
<td>字节数</td>
</tr>
<tr><td>静态消息</td>
<td>AddressBook</td>
<td>120</td>
</tr>
<tr><td>第二章自描述消息</td>
<td>FileDescriptorSet（3+302）
<br>
type_name（2+20）
<br>
message_data(2+120)</td>
<td>449</td>
</tr>
</tbody>
</table>
<p>这里需要注意的是表面上看数据量增加了274%，实际上增加的是固定的329字节，即当文件越来越大的时候这部分开销是不会增加的。</p>
<p>以第四章动态自描述消息为例分析在通讯协议中使用PB的应用场景</p>
<pre>
pair消息内容为：
key: &quot;jianhao&quot;
value: 8888
</pre><table border="1"><tbody><tr><td>使用方式</td>
<td>内容</td>
<td>字节数</td>
</tr>
<tr><td>静态消息</td>
<td>Pair</td>
<td>12</td>
</tr>
<tr><td>动态自描述消息</td>
<td>MAGIC_NUM
<br>
FileDescriptorProto length
<br>
FileDescriptorProto
<br>
Message length
<br>
Pair</td>
<td>64</td>
</tr>
</tbody>
</table>
<p>注意：在网络通讯中由于一次通讯需要传输一次完整的类型信息，所以消息越大越划算。
<br>
2.	时间：通过测试对比静态消息和动态自描述消息在日常的使用场景下的效率。
<br>
测试中的消息类型如下：</p>
<pre>
message Pair {
    required string key = 1;
    required uint32 value = 2;

}
</pre><p>生产者：
<br>
静态消息使用方式：</p>
<pre>
    pair.set_key(&quot;my key&quot;);
    pair.set_value(i);
    pair.SerializeToArray(buffer,100);
</pre><p>动态消息使用方式：</p>
<pre>
    const Reflection *reflection = pair-&gt;GetReflection();
    const FieldDescriptor *field = NULL;
    field = descriptor-&gt;FindFieldByName(&quot;key&quot;);
    reflection-&gt;SetString(pair, field, &quot;my key&quot;);
    field = descriptor-&gt;FindFieldByName(&quot;value&quot;);
    reflection-&gt;SetUInt32(pair, field, i);
    pair-&gt;SerializeToArray(buffer,100);
</pre><table border="1"><tbody><tr><td>消息使用方式</td>
<td>循环1M时间消耗</td>
<td>循环10M消耗时间</td>
</tr>
<tr><td>静态消息</td>
<td>0.37s</td>
<td>3.64s</td>
</tr>
<tr><td>动态消息</td>
<td>1.65s</td>
<td>16.51s</td>
</tr>
</tbody>
</table>
<p>由于绝对时间和机器环境有关，所以相对值更有意义。从上面的测试可知动态消息的赋值和序列化时间是静态消息的赋值和序列化的4倍。</p>
<p>消费者：
<br>
静态消息使用方式：</p>
<pre>
pair.ParseFromArray(buffer,100);
key=pair.key();
value=pair.value()+i;
</pre><p>动态自描述消息有两种使用方式：
<br>
1.仅反序列化&amp;操作payload message，常用于数据存储</p>
<pre>
  pair-&gt;ParseFromArray(buffer,100);
  const Reflection *reflection = pair-&gt;GetReflection();

  const FieldDescriptor *field = NULL;
  field = descriptor-&gt;FindFieldByName(&quot;key&quot;);
  key=reflection-&gt;GetString(*pair, field);
  field = descriptor-&gt;FindFieldByName(&quot;value&quot;);
  value=reflection-&gt;GetUInt32(*pair, field)+i;
  </pre><p>2.先反序列化payload message的类型信息，然后动态生成一个空的该类型对象，然后反序列化并操作该对象，常用于通讯协议</p>
<pre>
FileDescriptorProto file_proto;
file_proto.ParseFromArray(descbuffer,300);
DescriptorPool pool;
const FileDescriptor *file_descriptor = pool.BuildFile(file_proto);
const Descriptor *descriptor = file_descriptor-&gt;FindMessageTypeByName(&quot;Pair&quot;);

// build a dynamic message by &quot;Pair&quot; proto
DynamicMessageFactory factory;
const Message *message = factory.GetPrototype(descriptor);
Message *pair = message-&gt;New();
pair-&gt;ParseFromArray(buffer,100);
const Reflection *reflection = pair-&gt;GetReflection();

const FieldDescriptor *field = NULL;
field = descriptor-&gt;FindFieldByName(&quot;key&quot;);
key=reflection-&gt;GetString(*pair, field);
field = descriptor-&gt;FindFieldByName(&quot;value&quot;);
value=reflection-&gt;GetUInt32(*pair, field)+i;
</pre><table border="1"><tbody><tr><td>消息使用方式</td>
<td>循环1M时间消耗</td>
<td>循环10M消耗时间</td>
</tr>
<tr><td>静态消息</td>
<td>0.48s</td>
<td>4.85s</td>
</tr>
<tr><td>动态自描述消息（存储方式）</td>
<td>2.01s</td>
<td>17.28s</td>
</tr>
<tr><td>动态自描述消息（通讯方式）</td>
<td>28.24s</td>
<td>283.98s</td>
</tr>
</tbody>
</table>
<p>从上面的测试可知动态自描述消息的反序列化和操作时间是静态消息的反序列化和操作的4倍左右。但是如果加上对类型信息的反序列化得化则性能急剧下降到静态消息的接近60倍。</p>
<h1>6. 参考资料</h1>
<p>https://developers.google.com/protocol-buffers/?hl=zh-CN</p>
<p>http://www.ibm.com/developerworks/cn/linux/l-cn-gpb/?ca=drs-tp4608</p>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/8132.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/8132.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/8132.html#reviews">评论: 1</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>