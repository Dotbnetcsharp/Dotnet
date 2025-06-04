using System;
using System.Collections.Generic;
using System.IO;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

class Program
{
    static void Main()
    {
        var file1Path = "file1.json";
        var file2Path = "file2.json";

        var json1 = File.ReadAllText(file1Path);
        var json2 = File.ReadAllText(file2Path);

        var token1 = JToken.Parse(json1);
        var token2 = JToken.Parse(json2);

        var diff = GetJsonDiff(token1, token2);

        Console.WriteLine("Differences:");
        Console.WriteLine(diff.ToString(Formatting.Indented));
    }

    static JToken GetJsonDiff(JToken token1, JToken token2)
    {
        if (JToken.DeepEquals(token1, token2))
            return null;

        if (token1.Type != token2.Type)
            return token1;

        if (token1.Type == JTokenType.Object)
        {
            var result = new JObject();
            var obj1 = (JObject)token1;
            var obj2 = (JObject)token2;

            foreach (var prop in obj1.Properties())
            {
                var prop2 = obj2.Property(prop.Name);
                var diff = prop2 == null ? prop.Value : GetJsonDiff(prop.Value, prop2.Value);

                if (diff != null)
                    result[prop.Name] = diff;
            }

            return result.HasValues ? result : null;
        }

        if (token1.Type == JTokenType.Array)
        {
            var array1 = token1 as JArray;
            var array2 = token2 as JArray;

            var diffArray = new JArray();

            foreach (var item in array1)
            {
                bool found = false;
                foreach (var item2 in array2)
                {
                    if (JToken.DeepEquals(item, item2))
                    {
                        found = true;
                        break;
                    }
                }

                if (!found)
                    diffArray.Add(item);
            }

            return diffArray.Count > 0 ? diffArray : null;
        }

        // Different primitive value
        return token1;
    }
}
