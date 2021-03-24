
## More Examples

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

```jsp
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

