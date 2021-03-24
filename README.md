
![](images/ico2.png)

# iBoxDB

## Fast ACID Table-Style Document NoSQL Database


## Project Description


iBoxDB is a fast acid table-style document NoSQL Embedded Database, easily store objects and documents, traditional table with unstructured data, zero configuration, pure JAVA and DotNET engines, no dependencies.



iBoxDB has a well designed interface with great performance and capability for agile development. You can create applications with database services embedded and deploy it on mobiles, desktops, servers, to locally persist your data from anywhere. 



**Green**, low carbon emission!



Dual Core Application Database 



 For &nbsp;&nbsp;  JAVA,   C#,   Android,   Unity,   Xamarin,   Mono,   Linux,   Windows... 




[Download Assemblies](https://sourceforge.net/settings/mirror_choices?projectname=application-database&filename=iBoxDBv30_30.zip&selected=pilotfiber)


[Download Mirror](https://github.com/iboxdb/hijk/raw/dependencies/kits/iBoxDBv30_30.zip)


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
* Scalable hot MasterMaster and MasterSlave replications
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
     CommitResult cr = box.Commit();
  }
```

```java
  try(Box box = auto.cube()){
     ...
     CommitResult cr = box.commit();
  }  
```

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
  //load to memory first, startswith '*'
  box.Select("*from Member");
  //selecting tracer, startswith '!'
  box.Select("!from Member")  
~~~

### Index, make Select hundred times faster in average

```cs
  config.EnsureIndex<Member>("Member", "Field1","Field2")
  config.ensureIndex(Member.class, "Member", isUnique,"Field1","Field2")
  box.Select("from Member where Field1 == ? & Field2 == ?",arg1,arg2)
  //Id on the Left would faster in many cases
  box.Select("from Member where Id == ? & Field == ?",id,arg)
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

### Prototype Columns

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


### Mirror Copy

```cs
  auto.GetDatabase().CopyTo(new Mirror(bakAddr, bakRoot), buffer) 
```

### Directly Select

```cs
  //Select records from a samll file directly
  //without initializing database                   
  IEnumerable records = DB.Select(fileAddress) 
```

#### Transaction Huggers

```cs
  //Three transactions committed together
  long huggersBuffer = 1024L * 1024L * 32L;                   
  box1.Commit(huggersBuffer);
  box2.Commit(huggersBuffer);
  box3.Commit(); 
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


[Benchmark with MySQL -Java](https://github.com/iboxdb/teadb)


[Benchmark with MongoDB -Java](https://github.com/iboxdb/leafdb)


[Gradle Example -Java](https://github.com/iboxdb/jmegradle)


[Benchmark with SQLite -CSharp](https://github.com/iboxdb/benchmarksql)

