# Creating an HTTP triggered function

Now that the application is created, let's add a Function. To do this, follow the steps:

1. Right click on the project in the Solution Explorer and select Add, New Item from the context menu.
2. In the Add New Item dialog, select Azure Function. Give it a name and press Add.

![Add New Item](/Doc/Img/creating-testing-functions/2017-09-19_22-15-03.png)

## HTTP trigger without parameter

You can select an HTTP trigger with or without parameters. Let's start without parameters first.

3. In the New Azure Function dialog, select Http trigger.

![New HTTP Trigger](/Doc/Img/creating-testing-functions/2017-09-19_22-22-58.png)

Selecting "Http trigger" only will answer to a URL of the following form:

*http://myserver.com/FunctionName?param=value*

You can pass parameters to the Function by sending a GET request with a query string. Accessing the parameter in the function will be done by using the [GetQueryNameValuePairs method](https://msdn.microsoft.com/en-us/library/system.net.http.httprequestmessageextensions.getquerynamevaluepairs.aspx) on the [HttpRequestMessage object](https://msdn.microsoft.com/en-us/library/system.net.http.httprequestmessage.aspx). For example:

```CS
// parse query parameter
string name = req.GetQueryNameValuePairs()
    .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
    .Value;
```

Another way to pass parameters to the Function is to use a POST request. This can be used for more complex parameters, such as streams for images, videos, etc. Then you can access the parameter with:

```CS
// Get request body
dynamic data = await req.Content.ReadAsAsync<object>();
```

## HTTP trigger with parameter

If you are handling a small number of simple parameters, you might prefer a URL like the following. That is what you will get it you select "Http trigger with parameter".

*http://myserver.com/FunctionName/param/value*

3. In the New Azure Function dialog, select Http trigger with parameters.

![New HTTP Trigger with parameters](/Doc/Img/creating-testing-functions/2017-09-21_10-25-15.png)

Selecting "Http trigger with parameters" will answer to a URL of the following form:

*http://myserver.com/FunctionName/param/value*

This type of URL is more elegant than the one with the query string. Accessing the parameter is quite easy: Simply declare it in the Function's signature:

```CS
public static class HttpParams
{
    [FunctionName("HttpParams")]
    public static HttpResponseMessage Run(
        [HttpTrigger(
            AuthorizationLevel.Function, 
            "get", 
            "post", 
            Route = "HttpTriggerCSharp/name/{name}")]
        HttpRequestMessage req, 
        string name, 
        TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");

        // Fetching the name from the path parameter in the request URL
        return req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
    }
}
```

In the code above (which is automatically created when you create the Function), notice the Route. This will answer to a URL of the form:

*http://myserver.com/api/HttpTriggerCSharp/name/{name}*

where {name} is the value of the name parameter.

You can modify the route if you prefer. For instance:

```
Route = "hello/name/{name}"
```

In this case the URL will be:

*http://myserver.com/api/hello/name/{name}*

Here to you can use a POST request. This can be used for more complex parameters, such as streams for images, videos, etc and access the parameter with:

```CS
// Get request body
dynamic data = await req.Content.ReadAsAsync<object>();
```

# Running and testing the HTTP triggered function locally

Once the Function is created, you can easily test it. In Visual Studio, place a breakpoint on the first line of the function you want to debug. Then press F5 to build and run your application in debug mode.

![Debugging an Azure Function locally](/Doc/Img/creating-testing-functions/2017-09-21_17-05-44.png)

In the Azure Function console, find the local URL at the bottom.

![Debugging an Azure Function locally](/Doc/Img/creating-testing-functions/2017-09-21_17-07-17.png)

Open your favorite web browser and copy this URL into the location bar. You need to replace the *{name}* with a real name, for instance Laurent. The breakpoint in your function should be hit and you can then step into your code and inspect it.

Step through the code. When the function is done executing, you should see the following in your favorite web browser:

![Function result in the web browser](/Doc/Img/creating-testing-functions/2017-09-21_17-09-07.png)