
# Post \#72460627 [Link](https://stackoverflow.com/questions/72460627/)

## How to deserialize json array with nulls to non-nullable int list with JSON.NET?

**Vote**: 2 (631/702) **Views**: 930 (663/702) 

**Internal ID** \#2-6-414

Created at 2022-06-01 10:55:00

Tags: `c#` `json` `json.net`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#` `json`)

----------

**Notepad**


----------

I have the following part of json that gives me trouble:
```
"edges":["5","","5",""]}
```

Which im trying to deserialize to the following property:
```
public class Redacted
{
    ...

    [JsonProperty("edges", NullValueHandling = NullValueHandling.Ignore)]
    public List<int> Edges { get; set; } = new();

    ...
}
```

This gives me the followin error however:
```
Newtonsoft.Json.JsonSerializationException
  HResult=0x80131500
  Message=Error converting value {null} to type 'System.Int32'. Path 'edges[1]', line 1, position 186.
  Source=Newtonsoft.Json
  StackTrace:
   at Newtonsoft.Json.Serialization.JsonSerializerInternalReader.EnsureType(JsonReader reader, Object value, CultureInfo culture, JsonContract contract, Type targetType)
   at Newtonsoft.Json.Serialization.JsonSerializerInternalReader.CreateValueInternal(JsonReader reader, Type objectType, JsonContract contract, JsonProperty member, JsonContainerContract containerContract, JsonProperty containerMember, Object existingValue)
   at Newtonsoft.Json.Serialization.JsonSerializerInternalReader.PopulateList(IList list, JsonReader reader, JsonArrayContract contract, JsonProperty containerProperty, String id)
   at Newtonsoft.Json.Serialization.JsonSerializerInternalReader.CreateList(JsonReader reader, Type objectType, JsonContract contract, JsonProperty member, Object existingValue, String id)
   at Newtonsoft.Json.Serialization.JsonSerializerInternalReader.CreateValueInternal(JsonReader reader, Type objectType, JsonContract contract, JsonProperty member, JsonContainerContract containerContract, JsonProperty containerMember, Object existingValue)
   at Newtonsoft.Json.Serialization.JsonSerializerInternalReader.SetPropertyValue(JsonProperty property, JsonConverter propertyConverter, JsonContainerContract containerContract, JsonProperty containerProperty, JsonReader reader, Object target)
   at Newtonsoft.Json.Serialization.JsonSerializerInternalReader.PopulateObject(Object newObject, JsonReader reader, JsonObjectContract contract, JsonProperty member, String id)
   at Newtonsoft.Json.Serialization.JsonSerializerInternalReader.Populate(JsonReader reader, Object target)
   at REDACTED.ReadJson(JsonReader reader, Type objectType, Object existingValue, JsonSerializer serializer) in REDACTED.cs:line 45

  This exception was originally thrown at this call stack:
    System.Convert.ChangeType(object, System.Type, System.IFormatProvider)
    Newtonsoft.Json.Serialization.JsonSerializerInternalReader.EnsureType(Newtonsoft.Json.JsonReader, object, System.Globalization.CultureInfo, Newtonsoft.Json.Serialization.JsonContract, System.Type)

Inner Exception 1:
InvalidCastException: Null object cannot be converted to a value type.
```

I tried to specify `NullValueHandling.Ignore` in my model but this seems to only get applied to the property and not to the items in the list.
I prefer to not have to set `NullValueHandling.Ignore` in the settings, it should only apply to this specific property.
How can I deserialize this json to an `List<int>`?


----------
        
## Answer \#0

**Accepted** Vote: 3

Created at 2022-06-01 11:57:41

------------

Since you are using Json.NET, you can build a custom converter to handle empty `string`s and convert them to `int`. Here's a start:
```
public class NullableStringToIntConverter : JsonConverter
{
    public override bool CanConvert(Type objectType) => typeof(List<string>) == objectType;

    public override object ReadJson(JsonReader reader, Type objectType,
        object existingValue, JsonSerializer serializer)
    {
        var items = serializer.Deserialize<List<string>>(reader);
        
        return items
            .Where(s => !string.IsNullOrEmpty(s))
            .Select(int.Parse)
            .ToList();
            
        throw new NotImplementedException();
    }

    public override void WriteJson(JsonWriter writer, object value, 
        JsonSerializer serializer)
    {
        throw new NotImplementedException();
    }
}
```

And your class to deserialise into would look something like this:
```
public class Thing
{
    [JsonConverter(typeof(NullableStringToIntConverter))]
    public List<int> Edges { get; set; }
}
```

And finally to deserialise:
```
var thing = JsonConvert.DeserializeObject<Thing>(json);
```



------------
    
    
## Answer \#1

 Vote: 2

Created at 2022-06-01 11:58:02

------------

you can add edges property as a constructor input parameter instead of creating of a custom converter (you don't need to add all properties to  a constructor)
```
Redacted redacted=JsonConvert.DeserializeObject<Redacted>(json);

public class Redacted
{
     [JsonProperty("edges")]
    public List<int> Edges { get; set; }

    [JsonConstructor]
    public Redacted(List<string> edges)
    {
        Edges=edges.Where(e=>!string.IsNullOrEmpty(e)).Select(e =>  Convert.ToInt32(e)).ToList();
    }
    public Redacted()
    {
        
    }
}
```



------------
    
    
## Answer \#2

 Vote: 2

Created at 2022-06-01 12:32:11

------------

Another starting point for converter for list of int-like values (allows empty strings, nulls and integer values):
```
class SkipEmptyIntValuesListConverter : JsonConverter
{
    public override bool CanConvert(Type objectType) => false; 

    public override object ReadJson(
        JsonReader reader, Type objectType,
        object existingValue, JsonSerializer serializer)
    {
        var list = new List<int>();

        if (reader.TokenType == JsonToken.StartArray)
        {
            var value = reader.ReadAsInt32();
            while (reader.TokenType != JsonToken.EndArray)
            {
                if (value.HasValue)
                {
                    list.Add(value.Value);
                }
                value = reader.ReadAsInt32();
            } 

        }
        return list;
    }

    public override void WriteJson(
        JsonWriter writer, object value, JsonSerializer serializer)
    {
        serializer.Serialize(writer, value);
    }
}
```

Usage:
```
var json = "{\"edges\":[\"5\",\"\", 42, null]}";
    var r = JsonConvert.DeserializeObject<Redacted>(json);
    Console.Write(JsonConvert.SerializeObject(r)); // {"edges":[5,42]}


// Define other methods and classes here
public class Redacted
{
    [JsonProperty("edges")]
    [JsonConverter(typeof(SkipEmptyIntValuesListConverter))]
    public List<int> Edges { get; set; }
}
```



------------
    
    