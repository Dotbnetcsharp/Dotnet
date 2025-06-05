using System;
using System.IO;
using Newtonsoft.Json.Linq;

class Program
{
    static void Main()
    {
        string file1 = "file1.json";
        string file2 = "file2.json";

        var json1 = File.ReadAllText(file1);
        var json2 = File.ReadAllText(file2);

        var j1 = JToken.Parse(json1);
        var j2 = JToken.Parse(json2);

        Console.WriteLine("🔍 Only value differences:\n");
        CompareValuesOnly(j1, j2, "$");
    }

    static void CompareValuesOnly(JToken t1, JToken t2, string path)
    {
        if (t1 == null || t2 == null)
            return;

        if (t1.Type != t2.Type)
            return;

        if (t1.Type == JTokenType.Object)
        {
            foreach (var prop in ((JObject)t1).Properties())
            {
                if (((JObject)t2).TryGetValue(prop.Name, out var val2))
                {
                    CompareValuesOnly(prop.Value, val2, $"{path}.{prop.Name}");
                }
            }
        }
        else if (t1.Type == JTokenType.Array)
        {
            var arr1 = (JArray)t1;
            var arr2 = (JArray)t2;
            int minLen = Math.Min(arr1.Count, arr2.Count);

            for (int i = 0; i < minLen; i++)
            {
                CompareValuesOnly(arr1[i], arr2[i], $"{path}[{i}]");
            }
        }
        else
        {
            if (!JToken.DeepEquals(t1, t2))
            {
                Console.WriteLine($"Difference at path {path}");
                Console.WriteLine($"File1: {t1}");
                Console.WriteLine($"File2: {t2}\n");
            }
        }
    }
}
