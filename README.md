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

        var result = new JObject
        {
            ["onlyInFirst"] = GetDifferences(token1, token2),
            ["onlyInSecond"] = GetDifferences(token2, token1)
        };

        Console.WriteLine("Differences:");
        Console.WriteLine(result.ToString(Formatting.Indented));
    }

    static JToken GetDifferences(JToken source, JToken comparison)
    {
        if (source.Type != comparison.Type)
            return source;

        if (source.Type == JTokenType.Object)
        {
            var result = new JObject();
            var srcObj = (JObject)source;
            var cmpObj = (JObject)comparison;

            foreach (var prop in srcObj.Properties())
            {
                if (!cmpObj.TryGetValue(prop.Name, out var cmpValue))
                {
                    result[prop.Name] = prop.Value;
                }
                else
                {
                    var diff = GetDifferences(prop.Value, cmpValue);
                    if (diff != null && !diff.IsNullOrEmpty())
                        result[prop.Name] = diff;
                }
            }

            return result.HasValues ? result : null;
        }

        if (source.Type == JTokenType.Array)
        {
            var result = new JArray();
            var srcArray = (JArray)source;
            var cmpArray = (JArray)comparison;

            foreach (var item in srcArray)
            {
                bool exists = false;
                foreach (var cmpItem in cmpArray)
                {
                    if (JToken.DeepEquals(item, cmpItem))
                    {
                        exists = true;
                        break;
                    }
                }

                if (!exists)
                    result.Add(item);
            }

            return result.Count > 0 ? result : null;
        }

        return !JToken.DeepEquals(source, comparison) ? source : null;
    }
}

static class JTokenExtensions
{
    public static bool IsNullOrEmpty(this JToken token)
    {
        return token == null ||
               (token.Type == JTokenType.Object && !token.HasValues) ||
               (token.Type == JTokenType.Array && !token.HasValues);
    }
}
