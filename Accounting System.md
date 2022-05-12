1. 2022/5/12
upload file:
create temporary file in server =>  convert to byte[] to DB

https://stackoverflow.com/questions/41367602/upload-files-and-json-in-asp-net-core-web-api

Method 1: FromForm parameters
```javascript
//front-end:
let formData: FormData = new FormData();
formData.append('File', fileToUpload);
formData.append('jsonString', JSON.stringify(myObject));

//request using a var of type HttpClient
http.post(url, formData);
```

```C#
//controller action
public Upload([FromForm] IFormFile File, [FromForm] string jsonString)
{
    SomeType myObj = JsonConvert.DeserializeObject<SomeType>(jsonString);

    //do stuff with 'File'
    //do stuff with 'myObj'
}

```


Method2: ModelBinder
Apparently there is no built in way to do what I want. So I ended up writing my own ModelBinder to handle this situation. I didn't find any official documentation on custom model binding but I used this post as a reference.

Custom ModelBinder will search for properties decorated with FromJson attribute and deserialize string that came from multipart request to JSON. I wrap my model inside another class (wrapper) that has model and IFormFile properties.

IJsonAttribute.cs:

```C#

public interface IJsonAttribute
{
    object TryConvert(string modelValue, Type targertType, out bool success);
}

```



FromJsonAttribute.cs:

```C#

using Newtonsoft.Json;
[AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
public class FromJsonAttribute : Attribute, IJsonAttribute
{
    public object TryConvert(string modelValue, Type targetType, out bool success)
    {
        var value = JsonConvert.DeserializeObject(modelValue, targetType);
        success = value != null;
        return value;
    }
}
```

JsonModelBinderProvider.cs:
```C#

public class JsonModelBinderProvider : IModelBinderProvider
{
    public IModelBinder GetBinder(ModelBinderProviderContext context)
    {
        if (context == null) throw new ArgumentNullException(nameof(context));

        if (context.Metadata.IsComplexType)
        {
            var propName = context.Metadata.PropertyName;
            var propInfo = context.Metadata.ContainerType?.GetProperty(propName);
            if(propName == null || propInfo == null)
                return null;
            // Look for FromJson attributes
            var attribute = propInfo.GetCustomAttributes(typeof(FromJsonAttribute), false).FirstOrDefault();
            if (attribute != null) 
                return new JsonModelBinder(context.Metadata.ModelType, attribute as IJsonAttribute);
        }
        return null;
    }
}

```
JsonModelBinder.cs:

```C#

public class JsonModelBinder : IModelBinder
{
    private IJsonAttribute _attribute;
    private Type _targetType;

    public JsonModelBinder(Type type, IJsonAttribute attribute)
    {
        if (type == null) throw new ArgumentNullException(nameof(type));
        _attribute = attribute as IJsonAttribute;
        _targetType = type;
    }

    public Task BindModelAsync(ModelBindingContext bindingContext)
    {
        if (bindingContext == null) throw new ArgumentNullException(nameof(bindingContext));
        // Check the value sent in
        var valueProviderResult = bindingContext.ValueProvider.GetValue(bindingContext.ModelName);
        if (valueProviderResult != ValueProviderResult.None)
        {
            bindingContext.ModelState.SetModelValue(bindingContext.ModelName, valueProviderResult);
            // Attempt to convert the input value
            var valueAsString = valueProviderResult.FirstValue;
            bool success;
            var result = _attribute.TryConvert(valueAsString, _targetType, out success);
            if (success)
            {
                bindingContext.Result = ModelBindingResult.Success(result);
                return Task.CompletedTask;
            }
        }
        return Task.CompletedTask;
    }
}

```

Usage:

```C#

public class MyModelWrapper
{
    public IList<IFormFile> Files { get; set; }
    [FromJson]
    public MyModel Model { get; set; } // <-- JSON will be deserialized to this object
}

// Controller action:
public async Task<IActionResult> Upload(MyModelWrapper modelWrapper)
{
}

// Add custom binder provider in Startup.cs ConfigureServices
services.AddMvc(properties => 
{
    properties.ModelBinderProviders.Insert(0, new JsonModelBinderProvider());
});


```





