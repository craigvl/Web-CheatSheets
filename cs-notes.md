Accept arrays of primitive values in webapi action querystring  
==============================================================

***Web APi controller***
```
...
void Get([FromUri] IEnumerable<string> values)
{
  ...
}
...
```

***Request Query String***
```
https://.../Get?values[]=v1&values[]=v2&values[]=v3
```

***Angular request example***
```
 $http({
   method: 'GET',
   url: 'Get',
   params :{
     'values[]' : ['v1','v2','v3'] }
 })
```

Enable CORS on IIS
==================

***Web.Config***

- remove all reference to WebDAV
- add specific headers


```
 <system.webServer>
    <modules>
      <remove name="WebDAVModule" />
    </modules>
      <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="*" />
        <add name="Access-Control-Allow-Methods" value="GET, POST, PUT, DELETE, OPTIONS" />
        <add name="Access-Control-Allow-Headers" value="Authorization, Origin, X-Requested-With, Content-Type, Accept" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
```


Camel Case Support For JSON
===========================


WebApi
------

***Global.asax***

```
    protected void Application_Start()
    {
      ...
      
      var json = GlobalConfiguration.Configuration.Formatters.JsonFormatter;
      json.SerializerSettings.ContractResolver = new CamelCasePropertyNamesContractResolver();
      
      ...
    }

```

OData
-----

***WebApiConfig.cs***

```
    public static void Register(HttpConfiguration config)
    {
      var builder = new ODataConventionModelBuilder();
      builder.EnableLowerCamelCase();
      
      config.MapODataServiceRoute(
          routeName: "ODataRoute",
          routePrefix: null,
          model: builder.GetEdmModel());
    }

```

SignalR
-------

***FilteredCamelCasePropertyNamesContractResolver.cs***

```
public class FilteredCamelCasePropertyNamesContractResolver : DefaultContractResolver
  {
    public FilteredCamelCasePropertyNamesContractResolver()
    {
      AssembliesToInclude = new HashSet<Assembly>();
      TypesToInclude = new HashSet<Type>();
    }
    // Identifies assemblies to include in camel-casing
    public HashSet<Assembly> AssembliesToInclude { get; set; }
    // Identifies types to include in camel-casing
    public HashSet<Type> TypesToInclude { get; set; }
    protected override JsonProperty CreateProperty(MemberInfo member, MemberSerialization memberSerialization)
    {
      var jsonProperty = base.CreateProperty(member, memberSerialization);
      Type declaringType = member.DeclaringType;
      if (
          TypesToInclude.Contains(declaringType)
          || AssembliesToInclude.Contains(declaringType.Assembly))
      {
        jsonProperty.PropertyName = jsonProperty.PropertyName.ToCamelCase();
      }
      return jsonProperty;
    }
  }   
```

***Global.asax***

```
    protected void Application_Start()
    {
      ...
      
      var json = GlobalConfiguration.Configuration.Formatters.JsonFormatter;
      GlobalHost.DependencyResolver.Register(typeof(JsonSerializer),() => JsonSerializerFactory.Value);
      ...
    }

```
      



