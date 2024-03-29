### 1:下载Thrift
Thrift 是一个cs架构的 支持list/set/Map 使用socket传输的RPC框架
使用HomeBrew 安装 thrift
这里有个坑，那就是版本匹配，
gradle版本为0.12.0以上的才会匹配你编译起0.12.0版本，所以，切记用
thrift --version 先确定你homebrew安装的thrift版本，然后在项目中引用符合编译器的版本。
```
// https://mvnrepository.com/artifact/org.apache.thrift/libthrift
compile group: 'org.apache.thrift', name: 'libthrift', version: '0.12.0'

```
### 2:idl文件
```
namespace java thrift.generated
typedef i16 short
typedef i32 int
typedef i64 long
typedef bool boolean
typedef string String


struct Person {
  1: optional String name;
  2: optional boolean isMerried;
}

exception DataException {
  1: optional String message;
  2: optional String callback;
  3: optional String date;
}

service PersonService {
  Person getPersonByName(1:required String name)throws (1: DataException dataException);
  void savePerson(1: required Person person) throws (1:DataException dataException);
}
```
生成代码：`thrift  --gen java src/main/java/thrift/MyThrift.thrift `
### 3:客户端，服务端，服务代码编写
```
服务器端服务代码编写
public class PersonServiceImpl implements PersonService.Iface {

  @Override
  public Person getPersonByName(String name) throws DataException, TException {
    Person person = new Person();
    person.setName("Starrk");
    person.setIsMerried(false);
    return person;
  }

  @Override
  public void savePerson(Person person) throws DataException, TException {
    System.out.println("----------------");
    System.out.println(person.getName());
    System.out.println(person.isIsMerried());

  }
}
```
```
服务器编写
public class ThriftServer {

  public static void main(String[] args) throws  Exception {
    TNonblockingServerSocket socket = new TNonblockingServerSocket(8899);
    THsHaServer.Args arg = new Args(socket).minWorkerThreads(2).maxWorkerThreads(4);
    PersonService.Processor<PersonServiceImpl> processor = new Processor<>(new PersonServiceImpl());
    arg.protocolFactory(new Factory());
    arg.transportFactory(new TFramedTransport.Factory());
    arg.processorFactory(new TProcessorFactory(processor));
    TServer server = new THsHaServer(arg);
    System.out.println("--------server starting -------");
    server.serve();

  }

}
```
```
客户端编写
public class ThriftClient {

  public static void main(String[] args) {
    TTransport tTransport = new TFramedTransport(new TSocket("localhost",8899),600);
    TProtocol protocol = new TCompactProtocol(tTransport);
    PersonService.Client client = new PersonService.Client(protocol);
    try {

      tTransport.open();
      Person person = client.getPersonByName("zxc");
      System.out.println(person.getName()+person.isIsMerried());

      Person person1 = new Person();
      person1.setIsMerried(false);
      person1.setName("Json");
      client.savePerson(person1);
    }catch (Exception ex){
      throw new RuntimeException(ex.getMessage(),ex);
    }finally {
      tTransport.close();
    }
  }

}
```

### 4:传输协议
```
1. TBinaryProtocal --二进制格式
2. TCompactProtocal --压缩格式
3. TJSONProtocal  --JSON格式
4. TSimpleJSONProtocal  --提供JSON只写协议，生成的文件很容易通过脚本语言解析。
5. TDebugProtocal  --使用易懂的可读的文本格式，以便于debug
```
### 5:服务模型
```
TSimpleServer  --简单的线程服务模型，常用于测试。
TThreadPoolServer  --多线程服务模型，使用标准的阻塞式IO
TNonblockingServer  --多线程服务模型，使用非阻塞式IO（需使用TFramedTransport数据传输方式）
THsHaServer  --THsHa引入了线程池去处理，其模型把读写任务放到线程池去处理，Half-sync/Half-async的处理模式，Half-async是在处理IO事件上（accept/read/write io），Half-sync用于handler对rpc的同步处理
```
### 6:传输方式
```
TSocket   --阻塞式Socket
TFramedTransport  --以Frame为单位进行传输，非阻塞式服务中使用。
TFileTransport --以文件形式进行传输。
TMemoryTransport  --将内存用于I/O. Java实现时内部实际使用了简单的ByteArrayOutputStream
TZlibTransport --使用zlib进行压缩，与其他传输方式联合使用，当前无JAVA实现。
```

### 7:生产方案

TCompactProtocol + TFramedTransport + THsHaServer 较优方案


