using System;
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

        var diff = GetRedOnlyDiff(token1, token2);

        Console.WriteLine("Red Side Difference (Only in file1):");
        Console.WriteLine(diff?.ToString(Formatting.Indented) ?? "No difference.");
    }

    static JToken GetRedOnlyDiff(JToken file1, JToken file2)
    {
        if (file1 == null)
            return null;

        if (file2 == null || file1.Type != file2.Type)
            return file1;

        if (file1.Type == JTokenType.Object)
        {
            var diffObj = new JObject();
            foreach (var prop in file1.Children<JProperty>())
            {
                var otherProp = file2[prop.Name];
                var childDiff = GetRedOnlyDiff(prop.Value, otherProp);
                if (childDiff != null)
                {
                    diffObj[prop.Name] = childDiff;
                }
            }
            return diffObj.HasValues ? diffObj : null;
        }

        if (file1.Type == JTokenType.Array)
        {
            var arr1 = file1 as JArray;
            var arr2 = file2 as JArray;
            var diffArr = new JArray();

            foreach (var item in arr1)
            {
                bool exists = false;
                foreach (var item2 in arr2)
                {
                    if (JToken.DeepEquals(item, item2))
                    {
                        exists = true;
                        break;
                    }
                }
                if (!exists)
                    diffArr.Add(item);
            }

            return diffArr.Count > 0 ? diffArr : null;
        }

        // For primitive values
        return !JToken.DeepEquals(file1, file2) ? file1 : null;
    }
}
