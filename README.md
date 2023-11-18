
![](images/ico2.png)

### e^d = Ne^(Kd)  [2021-11-21];  

### cos(y,d) { (1/2) ( y^(1/d) + (y^(-1/d)) ( 1 - (1-y)^(2/d) ) ) }  [2022-03-11] Bruce Y.; 

### 0^0-e-LD-time-WE-light-HD  [2021-12-26];  e^(d+1) = Ne^(d+ln(e/N)) [2022-01-03]; 

### C = |e^(Xi)|;   time = e^(Xi) - e^(0i)  [2022-01-29]; 

### Ct = P = Ct'; t' = tk, Ctk = P' [2022-03-09];

### Ft = L = F't'; F'=(-F); t'=0-t, t=0-t'; dipolar events. [2023-03-03];

### F' = ma' = m(V1-V0)/1 = m(C-V0) = observer deviation. [2023-11-10];

#### each time one eats, the fate changed a bit. before time were clouds, life was born at first glance.

### F = [Spinning 0^0,LD,WE,HD] = Rainbow Gravity. [2022-03-06];

### Spin = sin(2(pi)/N)N = Cubed Particle. [2023-02-03];

### F = 0 = Space Super High Way = [cos(lr,LD) == cos(hr,HD)];

### delta(m) / delta(t) = one ton electrons in belly coil.

### S'(t)=speed(line); S''(t)=field(circle); S'''(t)=gravity(cone). [2023-05-27];

#### board a plane on the air to another spacetime. [2023-11-15];

### Twins: P = mV = (m0, V0) + (m1, V1) - (m2, -V2). [2023-11-19]; 

#### use Twins to build High-Time and Low-Time, to eliminate Light-Time and Mass. [2023-11-19]; 

# iBoxDB  

## Fast ACID Table-Style Document NoSQL Database


## Project Description


iBoxDB is a fast acid table-style document NoSQL Embedded Database, easily store objects and documents, traditional table with unstructured data, zero configuration, pure JAVA and DotNET engines, no dependencies.



iBoxDB has a well designed interface with great performance and capability for agile development. You can create applications with database services embedded and deploy it on mobiles, desktops, servers, to locally persist your data from anywhere. 



**Green**, low carbon emission!



Dual Core Application Database 



 For &nbsp; JAVA,   C#,   Android,   Unity,   Xamarin,   Mono,   Linux,   Windows... 




[Download Assemblies](https://sourceforge.net/settings/mirror_choices?projectname=application-database&filename=iboxdb392.zip&selected=pilotfiber)


[Download Mirror](https://github.com/iboxdb/hijk/raw/dependencies/kits/iboxdb392.zip)


[Website Mirror https://iboxdb.github.io/](https://iboxdb.github.io/)



## Features

* CRUD and Forward CRUD
* KeyValue and KeyOnly tables
* Unique and non-unique indexes
* Unique and non-unique composite indexes
* SQL like query language
* Transactions support
* Concurrency control, thread safe
* Multiple applications supported
* Memory management
* Programmable replication
* Hot mirror copy
* On disk and in memory databases supported
* Automatically create databases
* Zero configuration, copy and run, purely managed code
* Dynamic columns
* Prototype columns
* Different types with different indexes can be stored in same table
* Persistence + ORM + Cache + Embeddable, ONE STOP solution
* Ason, Array Script Object Notation
* High performance, no dependencies
* Transaction Huggers
* Directly Select
* Update Increment
* Selecting Tracer
* Snapshot-Serializable Isolation
* JAVA6+  &nbsp;&nbsp;  .NET2+

## Examples

### Each box is an ISOLATED data space (transaction-based)

```cs
  using(var box = auto.Cube())
  {                            
     //select, insert, update, delete ...
     //replace()=(insert or update)
     CommitResult cr = box.Commit();
  }
```

```java
  try(Box box = auto.cube()){
     ...
     CommitResult cr = box.commit();
  }  
```

as above, **box** is method's local variable, don't define as global variable. one box for one delivery. **AutoBox** can be defined globally in single application. 


### Normal Object

```cs
  Member m = new Member();  
  m.Id = box.NewId();
  m.Name = "Andy";
  m.Password = EncodePassowrd("123");
  box["Member"].Insert(m);
```

```java
  Member m = new Member();
  m.Id = box.newId();
  m.Name = "Kevin";
  box.d("Member").insert(m); 
```

### Dynamic Object (document database)

```cs
  //Game : Dictionary<string, object>
  game["GameType"] = "ACT";
  box["Product"].Insert(game);
```

```java
  //Game extends HashMap<String,Object>
  game.put("GameType", "ACT");
  box.d("Product").insert(game);  
```

### Key Value Style Query

```cs
  box["Table", 2L].Select<Member>();
   //Composite Key
  box["Table2", 99, "ABC"].Select<Product>();
```

```java
  box.d("Table", 3L).select(Member.class);
  //Composite Key
  box.d("Table2", 88, "ABC").select(Product.class);  
```

### Composite Key Supported

 ```java
 config.ensureTable(Item.class, "Item", "UID", "Type")
 ```
  
![](images/compositekey.png)


### SQL Like

```cs
  /* 
  from TABLE where A>? & B<=? order by C limit 0,10

  from [table] where [condition] 
                      order by [field1] desc,[field2] limit [0,10]
  [Condition:] == != < <= > >= & | ( )
  */ 
  box.Select<Member>("from Member where Name==?", "MyName");
  box.select(Member.class, "from Member where Name==?", "MyName"); 
```

### Query

```cs
  //query
  box.Select("from Member");
```

#### Query Options

```cs
  //load to memory first, startswith '*'.
  //because the OS has read-ahead setting, 
  //normally don't need to load first.  
  box.Select("*from Member");
  //selecting tracer, startswith '!'
  box.Select("!from Member")  
```

### Index, make Select hundred times faster in average

```cs
  config.EnsureIndex<Member>("Member", "Field1","Field2")
  config.ensureIndex(Member.class, "Member", isUnique,"Field1","Field2")
  box.Select("from Member where Field1 == ? & Field2 == ?",arg1,arg2)
```
```cs
  //Id on the Left would faster in many cases
  box.Select("from Member where Id == ? & Field == ?",id,arg)
```
```cs
  //combining Results would faster than OR,
  //depends on the scale of dataset and corresponded Index configure.	
  //where Id == ? | Id == ? | Id == ?
  foreach(var id in [ 1, 2, 3 ])
    combiner.Add( box.Select("from Member where Id == ?",id) )
```

### Custom QueryFunction

```cs
  box.Select<Member>("from Member where [Tags]", new IFunction("Value"))
```

### Custom Object Table Binding

```cs
  public bool Insert(Member m){
    return auto.Insert(nameof(Member), m);
  }
```

### Weak Object 

```java
 //Strongly typed
box.select("from Table where Id > ?" , 1L);
//Weak Object
box.select("from Table where Id > ?" , new Variant("1"));  
```

### Compatible with LINQ (.NET)

```cs
  from o in box.Select<Member>("from Member")
  where o.Text.Contains(text) select o;
```

### Compatible with Stream (Java)

```java
  StreamSupport
   .stream(box.select(Member.class,"from Member").spliterator(), true)
   .collect(groupingBy(m -> m.group, summarizingLong(m -> m.value)))
```


### Ason & Prototype Columns

```java
  //define prototype, Id is Long		
  Ason prototype = new Ason("Id:", 0L, "Name:", "Guest");

  Ason record = prototype.select();
  //set Id as String,
  record.set("Id", "123");
  //will automatically convert to Long as the prototype
  System.out.println("Output: " + record.get("Id").getClass());
  //Output: class java.lang.Long
```

### Hot Mirror Copy

```cs
  auto.GetDatabase().CopyTo(bakAddr, bakRoot, buffer) 
```

### Directly Select

```cs
  //Select records from a samll file directly
  //without initializing database                   
  IEnumerable records = DB.Select(fileAddress) 
```


### Transaction Huggers

```cs
  //Three transactions committed together
  long huggersBuffer = 1024L * 1024L * 32L;                   
  box1.Commit(huggersBuffer);
  box2.Commit(huggersBuffer);
  box3.Commit(); 
 ```

  
### Programmable Replication

```java
  BoxData.masterReplicate()
  BoxData.slaveReplicate()
```


### Forward CRUD 

```java
try (Box box = auto.cube()) { 
   // forward Update()
   Member m = box.d("Table", 100L).update(Member.class); 
   m.setName("Name"); 
   m.setAmount(BigDecimal.TEN); 
   CommitResult cr = box.commit(); 
}
```

### Snapshot-Serializable Transaction


Transaction Step | Isolation Level
---------------- | ---------------
Application Point | Snapshot 
Database Point | Serializable 

```cs
using(var box = auto.Cube()){
  //Snapshot...
  box.Commit( ()=>{
    //Serializable...  
  });                            
}
```

### Update Increment

&nbsp; | Apply To | Trigger | Type 
------ | -------- | ------- | ----
**UpdateIncrement** | non-primary key | insert/update | long 
AutoIncrement | primary key | insert | number 


&nbsp; | Value From | Generator Time
------ | ---------- | --------------
**UpdateIncrement** | Database NewId(MaxPos,1) | Serializable Transaction Commit 
AutoIncrement | Table Max(ID)+1 | Data Insert, Differed from Commit order 


### Selecting Tracer

&nbsp; | Thread | Usage 
------ | ------ | -----
**Tracer** | non-blocked | read/write different records  
Locker | blocked | read/write same record  


### IO

BoxFileStreamConfig |
------------------- |  
BoxMemoryStreamConfig |
ReadonlyStreamConfig |
CacheStreamConfig |


### Database Path
#### C# & JAVA,  place outside IDE working directory can get better performance 

```cs
//different path has hugely different writing speed on VM.
//Create Directory before putting DB inside.
//new File(path).mkdirs(); 
//Directory.CreateDirectory(path)
IBoxDB.LocalServer.DB.Root("/data/") 
```

Add this Database Path as Exclusion into Scan-System to get higher performance,
for example, System Defender.

#### ASP.NET Cross Platform 

```cs
DB.Root(MapPath("~/App_Data/"))
```

#### Xamarin 

```cs
DB.Root(System.Environment.GetFolderPath(
  System.Environment.SpecialFolder.LocalApplicationData/Personal))
```

#### Unity3D 

```cs
DB.Root(Application.persistentDataPath)
```

#### Android 

```java
DB.root(android.os.Environment.getDataDirectory()
       .getAbsolutePath() + "/data/" + packageName + "/") 
```

#### JSP WebApplication

```java
@WebListener()
public class StartListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
       String path = System.getProperty("user.home") + "/data/"; 
       new File(path).mkdirs();   
       DB.root(path); 
    }
} 
```

### Getting Started   C# and Java

```cs
 using IBoxDB.LocalServer;

 var db = new DB();
 db.GetConfig().EnsureTable<Record>("Table", "Id");
 AutoBox auto = db.Open();

 auto.Insert("Table", new Record { Id = 1L, Name = "Andy" });
 var record = auto.Get<Record>("Table", 1L);
 record.Name = "Kelly";
 auto.Update("Table", record);
 auto.Delete("Table", 1L);  
```

```java
 import iboxdb.localserver.*;

 DB db = new DB();
 db.getConfig().ensureTable(Record.class, "Table", "Id");
 AutoBox auto = db.open();

 auto.insert("Table", new Record(1L, "Andy"));
 Record record = auto.get(Record.class, "Table", 1L);
 record.Name = "Kelly";
 auto.update("Table", record);
 auto.delete("Table", 1L);  
 ```

### Memory Usage

```java
 DatabaseConfig cfg = db.getConfig();
 //Cache Memory, set less 1/3 total
 cfg.CacheLength = cfg.mb(1024L);
 //Mobile Device 64MB 
 cfg.CacheLength = cfg.mb(64L);
 //Read Pool, set **8+** to low Cache machine
 cfg.ReadStreamCount = 8;
 //Write Pool, set **4-** to low Cache machine
 cfg.WriteStreamCount = 1;
```

### Schemata 

```java
  auto.getDatabase().getSchemata() 
```


### Changed Values 

```cs
using IBoxDB.LocalServer.Replication;
DB db = new DB();
db.SetBoxRecycler(
  (Socket socket, BoxData outBox, bool normalPoweroff) => {
    if (socket.Actions > 0) {
      foreach (var e in outBox.GetActions()) {
        var actionType = e.ActionType;
        var key = e.Key;
        if (actionType == ActionType.Begin) {
	      continue;
        }

        var tableName = e.TableName;
        var value = e.Select();
        Log (actionType + " '" + tableName + "' at key '" + key 
          + "' \r\n" + DB.ToString (value));
}}});
```

### Multiple Applications 

```java
static DB db = new DB(db_id);
//for LockFile Supported Platform only
db.WaitAnotherApp(1000 * 60 * 5);

using (var auto = db.Open()){
   ...
   //auto.Cube(); Insert(), Update()...
}
```


### Automatic Object Mapping 

 Method | Column
 ------ | ------
 *.Name | Name
 *.name | name
 **Java** | **Column**
 *.Name(val) | Name
 *.setName(val) | Name
 *.name(val) | name
 *.setname(val) | name

 ```java
 DatabaseConfig cfg;
 //Create Table
 ensureTable(Record.class, "Table", "Id");
 //Create Index with length 32
 ensureIndex(Record.class, "Table", "Name(32)");
 ```


### Install

```sh
.NET: Add NETDB/iBoxDB.DLL  to Project
```

```sh
Java: Add JavaDB/iboxdb.jar to Project
```

### .NET AOT Register Types

```cs
public static object Register
   <[DynamicallyAccessedMembers(DynamicallyAccessedMemberTypes.All)] T>()
    where T : new()
{
   return new T();
}
//Trigger DynamicallyAccessedMemberTypes.All
Register<T1>();
EnsureTable<T1>("T1", "Id");
```


### Benchmark with MySQL

```sql
iBoxDB
 Insert: 1,000,000 AVG: 47,016 objects/s 
 Update: 1,000,000 AVG: 25,558 objects/s 
 Delete: 1,000,000 AVG: 42,714 objects/s 

MySQL
 Insert: 1,000,000 AVG: 5,514 objects/s 
 Update: 1,000,000 AVG: 5,109 objects/s 
 Delete: 1,000,000 AVG: 6,044 objects/s 
```

### Benchmark Code

[With MySQL Java Maven](https://sourceforge.net/p/teadb/code/)


[With SQLite C# Dotnet](https://sourceforge.net/p/benchmarksql3/code/)



### Cross-Platform

![](images/p3.png)



## Search Engines

[Full Text Search Engine Java](https://github.com/iboxdb/ftserver)

[Full Text Search Engine .NET](https://github.com/iboxdb/ftserver-cs)

[Self-hosted Search Engine Android](https://sourceforge.net/projects/ftserver-android/)


## Links

[Blazor Wasm Client](https://github.com/iboxdb/aspnet-cross-platform-db/blob/master/blazorwasm/IApp.cs)

[Gradle Demo jMonkeyEngine](https://github.com/iboxdb/jmegradle)

[Nuget Package](https://www.nuget.org/packages/iBoxDB/)

[.NET Linq, integrating with SQL ORM -XPO](https://sourceforge.net/p/datastorexpo/code/)
            

<br />


### More
[More Examples](README2.md)

