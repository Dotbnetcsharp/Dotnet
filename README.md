using System;
using System.IO;
using Newtonsoft.Json.Linq;
using Newtonsoft.Json;

class Program
{
    static void Main()
    {
        string file1Path = "file1.json";
        string file2Path = "file2.json";

        var json1 = File.ReadAllText(file1Path);
        var json2 = File.ReadAllText(file2Path);

        var jToken1 = JToken.Parse(json1);
        var jToken2 = JToken.Parse(json2);

        var diff = ExtractDifferences(jToken1, jToken2);

        Console.WriteLine("=== RED PART DIFF ===");
        Console.WriteLine(diff?.ToString(Formatting.Indented) ?? "No Difference Found");
    }

    static JToken ExtractDifferences(JToken source, JToken target)
    {
        if (JToken.DeepEquals(source, target))
            return null;

        if (source.Type != target?.Type)
            return source;

        if (source.Type == JTokenType.Object)
        {
            JObject diff = new JObject();
            var srcObj = (JObject)source;
            var tgtObj = (JObject)target;

            foreach (var prop in srcObj.Properties())
            {
                var srcVal = prop.Value;
                var tgtVal = tgtObj.TryGetValue(prop.Name, out JToken targetVal) ? targetVal : null;

                var nestedDiff = ExtractDifferences(srcVal, tgtVal);
                if (nestedDiff != null)
                {
                    diff[prop.Name] = nestedDiff;
                }
            }

            return diff.HasValues ? diff : null;
        }

        if (source.Type == JTokenType.Array)
        {
            var srcArray = (JArray)source;
            var tgtArray = (JArray)target;

            var diffArray = new JArray();

            foreach (var item in srcArray)
            {
                bool matched = false;
                foreach (var cmp in tgtArray)
                {
                    if (JToken.DeepEquals(item, cmp))
                    {
                        matched = true;
                        break;
                    }
                }

                if (!matched)
                    diffArray.Add(item);
            }

            return diffArray.HasValues ? diffArray : null;
        }

        // Primitive type (string, number, bool, null): return source only if value changed
        return !JToken.DeepEquals(source, target) ? source : null;
    }
}
