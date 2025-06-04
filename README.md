using System;
using System.IO;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json;

class Program
{
    static void Main()
    {
        var file1Path = "file1.json";
        var file2Path = "file2.json";

        var json1 = File.ReadAllText(file1Path);
        var json2 = File.ReadAllText(file2Path);

        var j1 = JToken.Parse(json1);
        var j2 = JToken.Parse(json2);

        Console.WriteLine("Differences found:\n");
        FindDifferences(j1, j2, "$");
    }

    static void FindDifferences(JToken token1, JToken token2, string path)
    {
        if (token1 == null && token2 == null)
            return;

        if (token1 == null || token2 == null || token1.Type != token2.Type)
        {
            Console.WriteLine($"Difference at path {path}");
            Console.WriteLine($"File1: {token1}");
            Console.WriteLine($"File2: {token2}\n");
            return;
        }

        switch (token1.Type)
        {
            case JTokenType.Object:
                foreach (var prop in ((JObject)token1).Properties())
                {
                    var nextPath = $"{path}.{prop.Name}";
                    var val2 = ((JObject)token2)[prop.Name];
                    FindDifferences(prop.Value, val2, nextPath);
                }
                break;

            case JTokenType.Array:
                var arr1 = (JArray)token1;
                var arr2 = (JArray)token2;
                int max = Math.Max(arr1.Count, arr2.Count);

                for (int i = 0; i < max; i++)
                {
                    var item1 = i < arr1.Count ? arr1[i] : null;
                    var item2 = i < arr2.Count ? arr2[i] : null;
                    var nextPath = $"{path}[{i}]";
                    FindDifferences(item1, item2, nextPath);
                }
                break;

            default:
                if (!JToken.DeepEquals(token1, token2))
                {
                    Console.WriteLine($"Difference at path {path}");
                    Console.WriteLine($"File1: {token1}");
                    Console.WriteLine($"File2: {token2}\n");
                }
                break;
        }
    }
}
