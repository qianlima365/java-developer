# json文件示例

## 生成json文件
```json
{
    "name":"张铁柱",
    "age":25,
    "height":"185.5",
    "school":"清华",
    "major":[{
              "job1":"worker",
              "job2":"doctor"
                  },
             {
              "job3":"teacher",
              "job4":"student"
                  }],
    "houseLocation":{
        "x":30,
        "y":30
    }
}
```
实现代码：
```java
// 方式1：java代码生成json文件

import json

String data = {
                "name":"张铁柱",
                "age":25,
                "height":"185.5",
                "school":"清华",
                "major":[{
                        "job1":"worker",
                        "job2":"doctor"
                            },
                        {
                        "job3":"teacher",
                        "job4":"student"
                            }],
                "houseLocation":{
                    "x":30,
                    "y":30
                }
            }

with open('data.json','w') as f:
    json.dump(data,f,indent=4)

// 方式2：通过Json对象生成json文件

String fullPath = filePath + File.separator + fileName + ".json";
//例如：fullPath="D:/myroot/test.json"
// 生成json格式文件
try {
    // 保证创建一个新文件
    File file = new File(fullPath);
    if (!file.getParentFile().exists()) 
    { // 如果父目录不存在，创建父目录
        file.getParentFile().mkdirs();
    }
    if (file.exists()) { // 如果已存在,删除旧文件
        file.delete();
    }
    file.createNewFile();

    //以下创建json格式内容
    //创建一个json对象，相当于一个容器
    JSONObject root =new JSONObject();
    root.put("name","张铁柱")；
    root.put("age",25)
    //假设身高是double，我们取小数点后一位
    double height=185.5345;
    root.put("height",(double)(Math.round(height*10)/10.0));
    JSONArray array=new JSONArray();
    JSONObject major1=new JSONObject();
    major1.put("job1","worker")；
    major1.put("job2","doctor");
    JSONObject major2=new JSONObject();
    major2.put("job3","teacher")；
    major2.put("job4","student");
    array.put( major1);
    array.put( major2);
    root.put("major",array);
    //假设位置x,y都是double型的,现在对他们取整
    double x=30.0045;
    double y=30.1123;
    //创建一个json对象，相当于一个容器
    JSONObject houloc=new JSONObject();
    houloc.put("x",Math.round(x));
    houloc.put("y",Math.round(y));
    root.put("houseLocation",houloc)
    
    // 格式化json字符串
    jsonString = formatJson(root.toString());

    // 将格式化后的字符串写入文件
    Writer write = new OutputStreamWriter(new FileOutputStream(file), "UTF-8");
    write.write(jsonString);
    write.flush();
    write.close();
    } catch (Exception e) {
    e.printStackTrace();
}

// -------格式化函数如下：

/**
 * 单位缩进字符串。
 */
private static String SPACE = "   ";

/**
 * 返回格式化JSON字符串。
 *
 * @param json 未格式化的JSON字符串。
 * @return 格式化的JSON字符串。
 */
public static String formatJson(String json) {
    StringBuffer result = new StringBuffer();

    int length = json.length();
    int number = 0;
    char key = 0;

    // 遍历输入字符串。
    for (int i = 0; i < length; i++) {
        // 1、获取当前字符。
        key = json.charAt(i);

        // 2、如果当前字符是前方括号、前花括号做如下处理：
        if ((key == '[') || (key == '{')) {
            // （1）如果前面还有字符，并且字符为“：”，打印：换行和缩进字符字符串。
            if ((i - 1 > 0) && (json.charAt(i - 1) == ':')) {
                result.append('\n');
                result.append(indent(number));
            }

            // （2）打印：当前字符。
            result.append(key);

            // （3）前方括号、前花括号，的后面必须换行。打印：换行。
            result.append('\n');

            // （4）每出现一次前方括号、前花括号；缩进次数增加一次。打印：新行缩进。
            number++;
            result.append(indent(number));

            // （5）进行下一次循环。
            continue;
        }

        // 3、如果当前字符是后方括号、后花括号做如下处理：
        if ((key == ']') || (key == '}')) {
            // （1）后方括号、后花括号，的前面必须换行。打印：换行。
            result.append('\n');

            // （2）每出现一次后方括号、后花括号；缩进次数减少一次。打印：缩进。
            number--;
            result.append(indent(number));

            // （3）打印：当前字符。
            result.append(key);

            // （4）如果当前字符后面还有字符，并且字符不为“，”，打印：换行。
            if (((i + 1) < length) && (json.charAt(i + 1) != ',')) {
                result.append('\n');
            }

            // （5）继续下一次循环。
            continue;
        }

        // 4、如果当前字符是逗号。逗号后面换行，并缩进，不改变缩进次数。
        if ((key == ',')) {
            result.append(key);
            result.append('\n');
            result.append(indent(number));
            continue;
        }

        // 5、打印：当前字符。
        result.append(key);
    }

    return result.toString();
}

/**
 * 返回指定次数的缩进字符串。每一次缩进三个空格，即SPACE。
 *
 * @param number 缩进次数。
 * @return 指定缩进次数的字符串。
 */
private static String indent(int number) {
    StringBuffer result = new StringBuffer();
    for (int i = 0; i < number; i++) {
        result.append(SPACE);
    }
    return result.toString();
}
```

## 解析json文件

```JSON
{
    "type": "FeatureCollection",
    "features": [
        {
            "type": "Feature",
            "geometry": {
                "type": "Point",
                "coordinates": [
                    121.4672,
                    31.11606
                ]
            },
            "properties": {
                "id": "16N5877",
                "q": 1
            }
        },
        {
            "type": "Feature",
            "geometry": {
                "type": "Point",
                "coordinates": [
                    121.531212,
                    31.3701954
                ]
            }
        }
    ]
}
```
实现代码：
```java
//把一个文件中的内容读取成一个String字符串
public static String getStr(File jsonFile){
    String jsonStr = "";
    try {
        FileReader fileReader = new FileReader(jsonFile);
        Reader reader = new InputStreamReader(new FileInputStream(jsonFile),"utf-8");
        int ch = 0;
        StringBuffer sb = new StringBuffer();
        while ((ch = reader.read()) != -1) {
            sb.append((char) ch);
        }
        fileReader.close();
        reader.close();
        jsonStr = sb.toString();
        return jsonStr;
    } catch (IOException e) {
        e.printStackTrace();
        return null;
    }
}

@Test
public void shuiLing(){
    String json = "E:\\gis\\data\\pd20192021-07-08.json";
    File jsonFile = new File(json);
    //通过上面那个方法获取json文件的内容
    String jsonData = CommonUtil.getJsonStr(jsonFile);
    //转json对象
    JSONObject parse = (JSONObject)JSONObject.parse(jsonData);
    //获取主要数据
    JSONArray features = parse.getJSONArray("features");
    //挨个遍历 
    for (Object feature : features) {
        JSONObject featureObject =(JSONObject)feature;
        JSONObject properties = featureObject.getJSONObject("properties");
        JSONObject geometry = featureObject.getJSONObject("geometry");
        JSONArray coordinates = geometry.getJSONArray("coordinates");
//            System.out.println(coordinates);
        //通过创建对应的实体类去存储对应数据然后存库
        GisDetails gisDetails = new GisDetails();
        gisDetails.setCreateTime(new Date());
        String date = jsonFile.getName();
    gisDetails.setDatetime(date.substring(date.indexOf("2021"),date.indexOf('.')));
        gisDetails.setId(properties.getString("id"));
        gisDetails.setQ(new BigDecimal(properties.getString("q")));
        gisDetails.setLat(new BigDecimal(coordinates.getString(1))); //维度
        gisDetails.setLon(new BigDecimal(coordinates.getString(0))); // 经度
//            System.out.println(properties);
        //如果数据量大不建议这样入库 直接拼接sql 然后插入一次
        int i = gisService.insertGisDetails(gisDetails);
        if (i>=0){
            log.info("==>成功"+gisDetails);
        }else{
            log.info("==》失败"+gisDetails);
        }
    }
}
```

