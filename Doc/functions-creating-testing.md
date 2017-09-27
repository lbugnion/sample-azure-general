# Creating and testing Azure Functions in Visual Studio

This article will show you how to create a new Azure Function in Visual Studio 2017, run it and test it locally.

> Note that you can also create and run a function in the Azure Portal directly, from your web browser. However coding the Function in Visual Studio has advantages, such as the possibility to debug the function before publishing it, unit testing the function, etc.

What's really cool is that the Azure SDK installs the full Azure Function runtime locally. It is not an emulator! This way you are as close as possible from the real deal. So you can easily test the function thoroughly before even publishing it.

## Installing the SDK

In Visual Studio, you need to install the Azure SDK in order to get the Azure Functions template. In the Visual Studio installer, make sure that you select this workload:

![Installing the Azure development workload](/Doc/Img/creating-testing-functions/2017-09-19_19-20-42.png)

After you are done, verify if the templates are properly installed by following these steps:

1. In Visual Studio 2017, select File, New, Project.
2. Under Visual C#, select the Cloud category.
3. Make sure that Azure Functions shows up in the templates area.

![Checking the template](/Doc/Img/creating-testing-functions/2017-09-19_19-26-28.png)

### In case the Functions don't show up...

Some installations of Visual Studio have a bug where, even after installing the Azure SDK, you don't see the Azure Function template in the File, New, Project dialog. If that happens to you, follow the steps:

1. Select Tools, Extensions and Updates.
2. Check the Updates category.
3. If you see "Azure Functions and Web Job Tools" in the list of updates, make sure that you perform the update.
4. After the update is performed, try again to check the steps above.

## Creating the Azure Functions application

You can create a new Azure Functions application in Visual Studio 2017 with these steps:

1. In Visual Studio 2017, select File, New, Project.
2. Under Visual C#, select the Cloud category.
3. Select the Azure Functions template as shown in the image above.
4. Give a name to your application and press OK.

After a short wait, your function is created. You should see the following files in the Solution Explorer:

![Function app in the Solution Explorer](/Doc/Img/creating-testing-functions/2017-09-19_22-08-00.png)

Note that a Functions app is a normal .NET based application, and so it can take advantage of the whole .NET framework, as well as any libraries supporting .NET such as portable class libraries, .NET Standard libraries, etc.

After you create the application, you can add the following functions:

*Follow the links for a tutorial showing you how to create and test such functions locally.*

* [HTTP Triggered function](functions-http.md)

* Timer Triggered function

* Blog Triggered function

* And more...
