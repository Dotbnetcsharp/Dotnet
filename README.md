
using System;
using System.IO;
using Newtonsoft.Json.Linq;

class Program
{
    static void Main(string[] args)
    {
        if (args.Length < 2)
        {
            Console.WriteLine("Usage: JsonDiff <file1.json> <file2.json>");
            return;
        }

        string file1Path = args[0];
        string file2Path = args[1];

        try
        {
            string file1Content = File.ReadAllText(file1Path);
            string file2Content = File.ReadAllText(file2Path);

            JToken json1 = JToken.Parse(file1Content);
            JToken json2 = JToken.Parse(file2Content);

            Console.WriteLine("Differences between the files:");
            FindDifferences(json1, json2, "");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Error: {ex.Message}");
        }
    }

    static void FindDifferences(JToken token1, JToken token2, string path)
    {
        if (JToken.DeepEquals(token1, token2))
        {
            return;
        }

        if (token1 == null || token2 == null)
        {
            Console.WriteLine($"{path}:");
            Console.WriteLine($"  File1: {token1 ?? "null"}");
            Console.WriteLine($"  File2: {token2 ?? "null"}");
            Console.WriteLine();
            return;
        }

        if (token1.GetType() != token2.GetType())
        {
            Console.WriteLine($"{path}:");
            Console.WriteLine($"  Type mismatch: File1 is {token1.Type}, File2 is {token2.Type}");
            Console.WriteLine();
            return;
        }

        switch (token1.Type)
        {
            case JTokenType.Object:
                var obj1 = (JObject)token1;
                var obj2 = (JObject)token2;

                // Find properties only in file1
                foreach (var property in obj1.Properties())
                {
                    if (!obj2.TryGetValue(property.Name, out var token2Value))
                    {
                        Console.WriteLine($"{path}/{property.Name}:");
                        Console.WriteLine($"  File1 has property with value: {property.Value}");
                        Console.WriteLine($"  File2 is missing this property");
                        Console.WriteLine();
                    }
                    else
                    {
                        FindDifferences(property.Value, token2Value, $"{path}/{property.Name}");
                    }
                }

                // Find properties only in file2
                foreach (var property in obj2.Properties())
                {
                    if (!obj1.TryGetValue(property.Name, out var token1Value))
                    {
                        Console.WriteLine($"{path}/{property.Name}:");
                        Console.WriteLine($"  File1 is missing this property");
                        Console.WriteLine($"  File2 has property with value: {property.Value}");
                        Console.WriteLine();
                    }
                }
                break;

            case JTokenType.Array:
                var arr1 = (JArray)token1;
                var arr2 = (JArray)token2;

                if (arr1.Count != arr2.Count)
                {
                    Console.WriteLine($"{path}:");
                    Console.WriteLine($"  Array length differs: File1 has {arr1.Count}, File2 has {arr2.Count}");
                    Console.WriteLine();
                }

                for (int i = 0; i < Math.Min(arr1.Count, arr2.Count); i++)
                {
                    FindDifferences(arr1[i], arr2[i], $"{path}[{i}]");
                }
                break;

            default:
                Console.WriteLine($"{path}:");
                Console.WriteLine($"  File1: {token1}");
                Console.WriteLine($"  File2: {token2}");
                Console.WriteLine();
                break;
        }
    }
}
