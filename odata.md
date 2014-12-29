#Extend metadata

```C#
      var model = builder.GetEdmModel();
      var edmType = model.FindDeclaredType("DerivcoApps.Alerter.OData.Models.Entities.Incident");

      const string namespaceName = "http://my.org/schema";

      // this registers a "canvas" namespace on the model
    model.SetNamespacePrefixMappings(new [] { new KeyValuePair<string, string>("canvas", namespaceName), });

    // set a simple string as the value of the "MyCustomAttribute" annotation on the "RevisionDate" property
    var stringType = EdmCoreModel.Instance.GetString(true);
    var value = new EdmStringConstant(stringType, "Bonjour");
    model.SetAnnotationValue(
      ((IEdmEntityType)model.FindType( "DerivcoApps.Alerter.OData.Models.Entities.Incident")).FindProperty("summary"),                            
      namespaceName, 
      "attrName", 
      value);


      // property
    model.SetAnnotationValue(edmType, namespaceName, "remedyEntityType", value);
```
