# Creating and testing a blob triggered function

In this sample, we will create a Blob triggered function, and save the output to another blob. Of course the principles here can apply to other scenarios. For example we could have [an HTTP triggered function](./functions-http.md) saving to a Blob by using the BlobAttribute described below.

## Creating the blob storage

In order to get the blob input and to store the output in another blob, we will have to configure the blob storage. This is quite easy to do with [the Microsoft Azure Storage Explorer](./azure-explorer.md). 

As a prerequisite, you have of course to create a storage account in your Azure subscription. [I described the steps here](./trial-account.md). If you already have an Azure subscription, you can jump directly to step 3.1 Creating the storage account.

1. In the Azure Explorer, select the storage account in which you want the blobs to live.

2. In the Properties tab below, select the Primary Connection String.

![Selecting the Primary Connection String](./img/creating-testing-functions/2017-11-14_11-54-15.png)

3. In the Solution Explorer, open the file named "local.settings.json". This file should look like this:

```json
{
    "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "CONNECTIONSTRING",
    "AzureWebJobsDashboard": ""
  }
}
```

4. Set the value of the AzureWebJobsStorage property to the value that you just copied (in the above, this would replace the CONNECTIONSTRING).

5. In the Azure Storage Explorer, expand the storage account and the expand the Blob Containers.

6. Right click on the Blob Containers and select Create Blob Container.

![Creating the new blob container](./img/creating-testing-functions/2017-11-14_12-05-20.png)

7. Enter the blob container's name, for instance ```test-in```.

8. Repeat the operation to create a new blob container named ```test-out```.

Now we have configured the storage and we are ready to create and implement the function itself.

## Creating the function

Like with other triggered functions, creating a blob triggered function in a Functions application happens through the Add, New Item menu.

1. Right click on the Functions application project in the Solution Explorer and select Add, New Item in the context menu.

2. In the Add New Item dialog, search for "function" and select "Azure Function" as the new template.

3. Enter a name for the function and press Add.

4. In the New Azure Function dialog, select Blob trigger.

Now we need to enter a Connection for the input blob. In the previous section, we created a new blob container, and we copied the Connection String to the "local.settings.json" file. This is used to configure the local Azure Functions runtime, but we can also use this value in the definition of the input and output blobs:

4. In the New Azure Function dialog, enter the value "AzureWebJobsStorage" in the Connection property.

5. Enter the name of the blob container you created earlier (in our case, "test-in") in the Path property. Then press OK.

![Creating the Blob triggered function](./img/creating-testing-functions/2017-11-15_07-23-08.png)

The created code should look something like this. Note that the values we entered for Connection and for the blob container path are directly accessible in the code, and can be edited if needed.

```CS
  public static class BlobSample
  {
      [FunctionName("BlobSample")]
      public static void Run(
          [BlobTrigger(
              "test-in/{name}", 
              Connection = "AzureWebJobsStorage")]
          Stream myBlob, 
          string name, 
          TraceWriter log)
      {
          log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
      }
  }
```

## Defining the output

At this stage, and before we perform a test, we will define the output blob where we will save the result of the operation. As mentioned earlier, saving the output as a blob is not an obligation, and we could do any kind of operation with this content. In this super simple sample, we will only copy the input blob as is to the output container, without any modification. Later you can of course modify this function to perform additional operations, for example resizing the image, extracting geotags, [creating a thumbnail](https://github.com/lbugnion/sample-azure-cognitive1), etc.

1. Modify the function as follows. Note how we define the output blob storage directly in the code using the ```BlobAttribute``` class.

```CS
public static class BlobSample
{
    [FunctionName("BlobSample")]
    public static void Run(
        [BlobTrigger(
            "test-in/{name}", 
            Connection = "AzureWebJobsStorage")]
        Stream myBlob, 
        string name, 
        [Blob(
            "test-out/{name}",
            FileAccess.Write,
            Connection = "AzureWebJobsStorage")]
        Stream outBlob,
        TraceWriter log)
    {
        log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");

        myBlob.CopyTo(outBlob);
    }
}
```

## Testing the function locally

As mentioned, the code above is simply taking the input blob as a Stream and copying it as is to the output blob container, with the same name. This is not very exciting, but up to you to perform awesome operations on the stream that you receive. We can already test the operation to see if everything works fine. Note that you need to be online in order to access the Azure blob storage.

1. Place a breakpoint in the first line of the ```Run``` method.

2. Press F5 to start the Azure Function runtime in debug mode.

3. When the runtime is ready, you should see the console, looking like this:

![The Azure Functions runtime console](./img/creating-testing-functions/2017-11-15_08-01-44.png)

4. Open the Microsoft Azure Storage Explorer and expand the Blob Containers under the storage account that you defined earlier.

5. Click on the ```test-in``` container.

6. Select "Upload" on the toolbar and then Upload files.

![Blob container toolbar](./img/creating-testing-functions/2017-11-15_08-46-14.png)

7. In the Upload files dialog, click on the `...` next to the Files field. This will open a file dualog where you can select the file. This can be any file such as an image, a PDF, a text file etc. Leave all the other files unchanged.

![Upload files dialog](./img/creating-testing-functions/2017-11-15_08-49-05.png)

8. Press the Upload button. This will upload the file to the blob storage, and after a short while the debugger should break into the ```Run``` method.

At this point, you can inspect the variables, note that the ```name``` parameter is showing the file's name, and that the ```myBlob``` stream is showing the correct length. Executing the function will copy the blob content as is to the output blob container.

9. After executing the function, use the Microsoft Azure Storage Explorer to inspect the content of the ```test-out``` blob container. If everything went well, you should now see an exact copy of the file that you uploaded.

> Did you know? You can double click on any file in the Azure Storage Explorer to download and open the file in question.
