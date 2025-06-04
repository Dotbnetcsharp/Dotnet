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

        var diff = GetExactDifference(j1, j2);

        Console.WriteLine("Only changed/missing values from file1:");
        Console.WriteLine(diff?.ToString(Formatting.Indented) ?? "No difference.");
    }

    static JToken GetExactDifference(JToken file1, JToken file2)
    {
        if (JToken.DeepEquals(file1, file2))
            return null;

        if (file1.Type != file2?.Type)
            return file1;

        if (file1.Type == JTokenType.Object)
        {
            var obj1 = (JObject)file1;
            var obj2 = (JObject)file2;

            var diffObj = new JObject();

            foreach (var prop in obj1.Properties())
            {
                var name = prop.Name;
                var val1 = prop.Value;
                var val2 = obj2.TryGetValue(name, out var cmpVal) ? cmpVal : null;

                var diff = GetExactDifference(val1, val2);
                if (diff != null)
                    diffObj[name] = diff;
            }

            return diffObj.HasValues ? diffObj : null;
        }

        if (file1.Type == JTokenType.Array)
        {
            var arr1 = (JArray)file1;
            var arr2 = (JArray)file2;

            var diffArr = new JArray();

            foreach (var item in arr1)
            {
                bool matched = false;
                foreach (var cmpItem in arr2)
                {
                    if (JToken.DeepEquals(item, cmpItem))
                    {
                        matched = true;
                        break;
                    }
                }

                if (!matched)
                    diffArr.Add(item);
            }

            return diffArr.Count > 0 ? diffArr : null;
        }

        // Primitive value changed
        return file1;
    }
}
