
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

### Composite Key Supported

 ```java
 config.ensureTable(Item.class, "Item", "UID", "Type")
 ```
  
![](images/compositekey.png)


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

### Transaction Huggers

```cs
  //Three transactions committed together
  long huggersBuffer = 1024L * 1024L * 32L;                   
  box1.Commit(huggersBuffer);
  box2.Commit(huggersBuffer);
  box3.Commit(); 
 ```

### Weak Object 

```java
 //Strongly typed
box.select("from Table where Id > ?" , 1L);
//Weak Object
box.select("from Table where Id > ?" , new Variant("1"));  
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
UpdateIncrement | non-primary key | insert/update | long 
AutoIncrement | primary key | insert | number 


&nbsp; | Value From | Generator Time
------ | ---------- | --------------
UpdateIncrement | Database NewId(MaxPos,1) | Serializable Transaction Commit 
AutoIncrement | Table Max(ID)+1 | Data Insert, Differed from Commit order 


### Selecting Tracer

&nbsp; | Thread | Usage 
------ | ------ | -----
Tracer | non-blocked | read/write different records  
Locker | blocked | read/write same record  


### IO

IO |  
-- |
BoxFileStreamConfig | 
BoxMemoryStreamConfig |
ReadonlyStreamConfig |
CacheStreamConfig |


### Database Path
#### C# & JAVA,  place outside IDE working directory can get better performance 

```cs
//different path has hugely different writing speed on VM.
IBoxDB.LocalServer.DB.Root("/data/") 
```

#### ASP.NET Cross Platform 

```cs
DB.Root(MapPath("~/App_Data/"))
```

#### Xamarin DB.Root(System.Environment.GetFolderPath( 

```cs
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

 #### Schemata 

```java
auto.getDatabase().getSchemata() 
```

### Automatic Object Mapping 

 Method | Column
 ------ | ------
 *.Name | Name
 *.name | name
 *.Name(val) | Name
 *.setName(val) | Name
 *.name(val) | name
 *.setname(val) | name


### Install

```sh
.NET: Add NETDB/iBoxDB.DLL  to Project
```

```sh
Java: Add JavaDB/iboxdb.jar to Project
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

[Version 3.0 With MySQL Java Maven](https://sourceforge.net/p/teadb/code/)


[Version 3.0 With SQLite C#](https://sourceforge.net/p/benchmarksql3/code/)


[Version 2.X With MySQL Java Maven](https://github.com/iboxdb/teadb)

                
[Version 2.X With SQLite C#](https://github.com/iboxdb/benchmarksql)



### Cross-Platform

![](images/p3.png)



## Links

[Full Text Search Java](https://github.com/iboxdb/ftserver)

[Full Text Search C#](https://github.com/iboxdb/ftserver-cs)

[Blazor Wasm Client](https://github.com/iboxdb/aspnet-cross-platform-db/blob/master/blazorwasm/IApp.cs)

[Gradle Demo jMonkeyEngine](https://github.com/iboxdb/jmegradle)

[Nuget Package](https://www.nuget.org/packages/iBoxDB/)

[.NET Linq, integrating with SQL ORM -XPO](https://sourceforge.net/p/datastorexpo/code/)
            


## Examples 2

### The DB Life Cycle 

```java
import iboxdb.localserver.*;

public class App{
   public static AutoBox auto;
   public static Box cube(){
     return auto.cube();
   }
}                
synchronized static void onApplicationStart() { 
  DB.root(path);
  DB db = new DB(1); 

  db.getConfig()
    .ensureTable(Member.class, "Table", "Id") 
    .ensureIndex(Member.class, "Table", "Name"); 

  //Tree->Parts 
  db.getConfig()
    .ensureTable(Node.class, "Node", "Id") 
    .ensureIndex(Node.class, "Node", "Parent"); 

  db.getConfig()
    .ensureTable(Part.class, "Part", "Id") 
    .ensureIndex(Part.class, "Part", "Node");

  DatabaseConfig cfg = db.getConfig();
  cfg.CacheLength = cfg.mb(512L);
  cfg.FileIncSize = (int)cfg.mb(4);

  App.auto = db.open(); 
}        
                         
void anywhere1() { 
  App.auto.insert("Table", new Member()); 
} 

void anywhere2() { 
  try (Box box = App.cube()) { 
    box.d("Table").insert(new Member()); 
    CommitResult cr = box.commit(); 
  } 
} 

boolean exists(Box box) { 
  return box.count("from Table limit 0,1") > 0; 
} 

<V> Iterable<V> startsWith(Box box, Class<V> vClass, String prefix) { 
  String begin = prefix; 
  char[] cs = prefix.toCharArray(); 
  cs[cs.length - 1] = (char) ((short) cs[cs.length - 1] + 1); 
  String end = new String(cs); 
  return box.select(vClass,"from Table where Name>=? & Name<?",begin,end); 
}

void treeJoin(Box box, long nodeRoot, int maxLevel, StringBuilder result) {
  for (Node node : 
         box.select(Node.class, "from Node where Parent==?", nodeRoot)) { 
           result.append(" NODE:").append(node.Id); 
    for (Part part : 
       	   box.select(Part.class, "from Part where Node==?", node.Id)) { 
      	     result.append(" PART:").append(part.Id); 
    } 
    treeJoin(box, node.Id, maxLevel + 1, result); 
  } 
}

synchronized static void onApplicationClose() {
  //Wait for all background/async tasks to finished.
  App.waitfor();  
  App.auto.getDatabase().close(); 
} 
```


### Use DatabaseServer to Manage Configs 

```cs
using IBoxDB.LocalServer;
using IBoxDB.LocalServer.IO;

public class AppServer : LocalDatabaseServer 
{ 
  private static AppServer server = new AppServer(); 
  public static IDatabase DB(long address) 
  { 
    return server.GetInstance(address); 
  }

  abstract class Config : BoxFileStreamConfig 
  { 
    public Config() : base() 
    { 
      CacheLength = MB(512); //1024L*1024L*512L
      FileIncSize = (int)MB(4); 
      //Thread, default is 4
      ReadStreamCount = 8; 
      //set equal to 1 in small device.
      //ReadStreamCount = 1; 
    } 
  } 
                
  class MainConfig : Config 
  { 
    public MainConfig() : base() 
    { 
      EnsureTable<Member>("Member", "Id"); 
      //set Max-Length = 20, String default is 32
      EnsureIndex<Member>("Member", "Name(20)");
      EnsureTable<Product>("Product", "Type", "UID"); 
    }
  }
  
  class InventoryConfig : Config 
  { 
    public InventoryConfig() : base() 
    { 
      EnsureTable<Item>("Item", "Id"); 
    } 
  } 
                
  protected override DatabaseConfig BuildDatabaseConfig(long address) 
  { 
    switch (address) 
    { 
      case 1L: return new MainConfig(); 
      case 2L: return new InventoryConfig(); 
      default: return null; 
    } 
  } 
} 
                
void Anywhere1() 
{ 
  var auto = AppServer.DB(1L).Get(); 
  auto.Insert("Member", new Member()); 
} 
                
void Anywhere2() 
{ 
  var db = AppServer.DB(2L); 
  using (var box = db.Cube()) 
  { 
    box["Item"].Insert(new Item()); 
    CommitResult cr = box.Commit(); 
  } 
}

void OnApplicationClose() 
{ 
  AppServer.server.Dispose(); 
}
```

### JSP Config 

```html
//db.jsp
<%@page contentType="text/html" pageEncoding="UTF-8" session="false" %>
<%@page import="iboxdb.localserver.*" %>
<%!
    public static AutoBox auto() {
        synchronized (DB.class) {
            if (DB.Tag != null) {
                return (AutoBox) DB.Tag;
            }

            String root = System.getProperty("user.home") + "/jsp_data/";
            new java.io.File(root).mkdirs();
            DB.root(root);
            DB db = new DB(1);
            db.getConfig().ensureTable(JSPUser.class, "JSPUser", "id");

            DB.Tag = db.open();
            return (AutoBox) DB.Tag;
        }
    }

    public static class JSPUser {
        public long id;
        public String name;
    }
%>

//index.jsp
<%@page contentType="text/html" pageEncoding="UTF-8" session="false"%>
<%@include file="db.jsp" %>

<%
    long id = auto().newId();

    JSPUser user = new JSPUser();
    user.id = id;
    user.name = "iBoxDB on JSP -" + user.id;
    auto().insert("JSPUser", user);
%>
<html>
    <body>
        <h1><%=auto().get(JSPUser.class, "JSPUser", id).name%> </h1>
    </body>
</html>
```

### Save DB as String 

```cs
Start(){
    byte[] data = Convert.FromBase64String(PlayerPrefs.GetString("DB","")); 
    db = new DB(data);
    db.MinConfig().FileIncSize = 1; 
    ...
    auto = db.Open();
}  
Save() { 
    byte[] data = db.GetBuffer(); 
    //data = Zip(data); = 1/5
    PlayerPrefs.SetString("DB",Convert.ToBase64String(data)); 
    PlayerPrefs.Save(); 
}
```

### Unity3D WebGL Save 

```cs
if (GUI.Button(new Rect(0, 50, Screen.width / 2, 50), "Save")) { 
   Application.ExternalEval("FS.syncfs(false, function (err) {});"); 
}
```

### Cache Only & Heat Up 

```cs
DB db = new DB(DB.CacheOnlyArg);

//Using Thread to heat engine up Before Application.Start()
//System.Threading.Tasks.Task.Run((Action)Hot);
new Thread(() => { Hot(); }).Start();
static AutoBox auto;
public static void Hot() { 
  var db = new DB(); 
  db.GetConfig().EnsureTable<Item>("Item", "Time", "Gid"); 
  auto = db.Open(); 
  auto.Insert("Item", new Item()); 
  auto.Select("from Item");  
}
```

### Complex Search, Region Control 

```cs
//use a simple query to control search region
box.Select<User>("from User where Version>? limit 0,1000", last) 
   //with a complex query to filter data
   .Where((u) => { 
       if ( u... )
        if ( u... )
          return u.Time > dt ;
   });
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

### Big Cache 

```java
 DatabaseConfig cfg = db.getConfig();
 cfg.CacheLength = cfg.mb(1024L*3L);
 //or
 cfg.CacheLength = 1024L*1024L*1024L*3L
 //Overflow int
 //1024*1024*1024*3
```

### Dataset Combiner 

```cs
//combining dataset
public class Combiner<T>
{
    HashSet<object> ids;
    Func<T, object> getid;
    List<T> results;
    public Combiner(Func<T, object> getid)
    {
        this.getid = getid;
        this.ids = new HashSet<object>();
        this.results = new List<T>();
    }
    public void Add(IEnumerable<T> result)
    {
        foreach (var t in result)
        {
            if (getid != null)
            {
                var id = getid(t);
                if (!ids.Add(id)) { continue; }
            }
            results.Add(t);
        }
    }
    public List<T> Select()
    {
        return results;
    }
    public IEnumerable<T> OrderBy<TKey>(Func<T, TKey> keySelector)
    {
        return Enumerable.OrderBy(results, keySelector);
    }
    public IEnumerable<IGrouping<TKey, T>> 
    		GroupBy<TKey>(Func<T, TKey> keySelector)
    {
        return Enumerable.GroupBy(results, keySelector);
    }
}

class Item
{
    public long Id;
    public string Name;
    public string Location;
}

static void Main(string[] args)
{
    DB db = new DB();
    db.GetConfig().EnsureTable<Item>("Item", "Id")
     .EnsureIndex<Item>("Item", "Name")
     .EnsureIndex<Item>("Item", "Location");

    AutoBox auto = db.Open();
      
    using(Box box = auto.Cube())
    {
        Combiner<Item> combiner = new Combiner<Item>(t => t.Id);
        combiner.Add(box.Select<Item>("from Item where Name==?", "Name4"));
        combiner.Add(box.Select<Item>("from Item where Id==?", 1L));
        combiner.Add(box.Select<Item>("from Item where Id==?", 2L));
        combiner.Add(
         box.Select<Item>("from Item where Location==?", "Location6"));
        foreach (var t in combiner.OrderBy(t => t.Id))
        {
            Console.WriteLine(DB.ToString(t));
        }
    }
}   
```



### Supported Types

 .NET | 
 ---- |
 bool |
 char |
 byte |
 sbyte |
 short | 
 ushort |
 int |
 uint |
 long |
 ulong |              
 float |
 double |        
 decimal |
 DateTime |
 Guid |
 &nbsp; |   
 bool? | 
 char? | 
 byte? | 
 sbyte? | 
 short? |
 ushort? | 
 int? | 
 uint? | 
 long? | 
 ulong? |      
 float? | 
 double? | 
 decimal? |
 DateTime? |
 Guid? | 
 string | 
 &nbsp; |
 //non-indexable |
 Dictionary<string, object> |
 MemoryStream |
 byte[] |
 string[] |

 JAVA | 
 ---- |
 boolean | 
 Boolean |
 byte | 
 Byte |
 char | 
 Character |
 short | 
 Short |
 int | 
 Integer |
 long | 
 Long |
 float |
 Float |
 double |
 Double |
 UUID |
 Date |
 &nbsp; |
 //dynamic length |
 BigDecimal |
 BigInteger |
 String |
 &nbsp; |
 //non-indexable |
 HashMap<String,Object> |
 MemoryStream |
 byte[] |
 Object[] |

