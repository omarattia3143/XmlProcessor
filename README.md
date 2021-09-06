# XmlProcessor
code snippet for Xml processor that can process xml into model using generics or JsonResult

1. takes any class (model) and deserialize xml to that given class
   ```csharp

 private static async Task<T> ProcessResponseTo<T>(HttpResponseMessage httpResponseMessage)
        {
            var stream = await httpResponseMessage.Content.ReadAsStringAsync();

            var code = (int) httpResponseMessage.StatusCode;

            //todo: if code is not 200 do some action

            var serializer = new XmlSerializer(typeof(T));
            using TextReader reader = new StringReader(stream);
            return (T) serializer.Deserialize(reader);
        }
  ```
  and here an example for model to be used:
    ```csharp
 
  [XmlRoot("Response")] //the name of the root xml node
    public class SendSmsRes
    {
        [XmlElement] public int property1 { get; set; }
        [XmlElement] public string property2 { get; set; }
    }
  ```
==========================================================================================================================================================
  
  2. takes xml and process to JsonResult
    ```csharp

   private static async Task<JsonResult> ProcessResponseToJsonWrapper<T>(HttpResponseMessage httpResponseMessage)
        {
            var stream = await httpResponseMessage.Content.ReadAsStringAsync();

            var code = (int) httpResponseMessage.StatusCode;

            //todo: if code is not 200 do some action

            var serializer = new XmlSerializer(typeof(T));
            using TextReader reader = new StringReader(stream);


            var data = serializer.Deserialize(reader);

            var response = new ResponseModel
            {
                data = data,
                error = !httpResponseMessage.IsSuccessStatusCode,
                statusCode = code,
                message = httpResponseMessage.ReasonPhrase
            };

            return new JsonResult(response)
            {
                StatusCode = code
            };
        }
```
