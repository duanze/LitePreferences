# LitePreferences

[设计解读](http://www.jianshu.com/p/64ef6eb7406f)

### Why use this library?

1.You can simplify the use of SharedPreferences:
```
// read
LitePrefs.getString("preference_key");
// write
LitePrefs.putString("preference_key", "new value");
```
rather than
```
// get it
SharedPreferences p = context.getSharedPreferences("liteprefs", Context.MODE_PRIVATE);
// read
p.getString("preference_key", "default value");
// write
p.edit().putString("preference_key", "new value").commit();
```

As you see,just initialize the LitePrefs once,you can read and write SharedPreferences everywhere.

2.You can just put your preference key in the strings.xml,because you can use them like this:
```
// read
LitePrefs.getString(R.string.cool_key);
// write
LitePrefs.putString(R.string.cool_key,"it is cool");
```

3.It's support config file, just read the followings to get more infomations.

# Document
### 1.Config xml file
Creating the config xml file.
<br>First add a folder called 'xml' to your Resource folder 'res', then create a file called prefs.xml (Or the name you want).
<br>This is a sample file how it should be structured.
```
<?xml version="1.0" encoding="utf-8"?>
<prefs name="liteprefs">
    <pref>
        <key>preference_key</key>
        <def-value>default value</def-value>
        <description>Write some sentences if you want,
        the LitePrefs parser will not parse the tag "description"</description>
    </pref>
    <pref>
        <key>boolean_key</key>
        <def-value>false</def-value>
    </pref>
    <pref>
        <key>int_key</key>
        <def-value>233</def-value>
        <anoter-tag>Any other tag is ok</anoter-tag>
    </pref>
    <pref>
        <key>float_key</key>
        <def-value>3.141592</def-value>
    </pref>
    <pref>
        <key>long_key</key>
        <def-value>4294967296</def-value>
    </pref>
    <pref>
        <key>String_key</key>
        <def-value>this is a String</def-value>
        <attention>LitePrefs don't support the data type:Set&lt;String&gt;</attention>
    </pref>
</prefs>
```
### 2.Initialize LitePrefs from xml
Use the config xml initialize LitePrefs,just do this operation **one time**:
<br>(Pass the **Application Context** or **Main Activity**
      as parameter to avoid your activity
      cannot be recycled by GC.)
```
try {
     LitePrefs.initFromXml(context, R.xml.prefs);
} catch (IOException | XmlPullParserException e) {
     e.printStackTrace()
}

// optional, if don't configure it ,the default is commit()
LitePrefs.setEditMode(LitePrefs.MODE_APPLY);
```
### 3.Use LitePrefs everywhere
Now you can use the SharedPreferences named "liteprefs" easily like this everywhere:
```
// If the preference has no value,the default value defined in config xml will be returned
LitePrefs.getString("preference_key");

// Don't need to call "commit()",LitePrefs has wrapped it in every putXXX() method.
LitePrefs.putFloat("float_key",0.314);

...
```
### 4.Initailize from map
Maybe you prefer to using java codes:
```
// Prepare data source
Map<String,Pref> map = new HashMap<>();
// put <key,pref> in the map
map.put("float_key",new Pref("float_key",0.222f));
map.put("str",new Pref("str","LitePrefs!"));
...
String prefsName = "liteprefs";
LitePrefs.initFromMap(context, prefsName, map);
```
 You can append data via this method:
```
Pref p = new Pref("new_pref",11111);
LitePrefs.putToMap(p.key,p);
```
### 5.Exteneds LitePrefs
LitePrefs use *singleton*,so there is a Special base class to extend:*BaseLitePrefs*, which is not a standard *singleton*.
<br>I use it in my note app [PureNote](https://github.com/duanze/PureNote):
```
public class MyLitePrefs extends BaseLitePrefs {

    public static final String THEME = "choose_theme_key";

    public static void initFromXml(Context context) {
        try {
            initFromXml(context, R.xml.prefs);
        } catch (IOException | XmlPullParserException e) {
            e.printStackTrace();
        }
    }

    public static ThemeUtils.Theme getTheme() {
        return ThemeUtils.Theme.mapValueToTheme(getInt(THEME));
    }

    public static boolean setTheme(int value) {
        return putInt(THEME, value);
    }

}
```

### 6.Shortages that compared with SharedPreferences
- Don't support data type Set&lt;String&gt; and its methods
- Don't support listener
- Don't support apply() and chain put calls together
- Don't have contains(String) and getAll()
<br>Actually,I don't mind the all above at present.

# Sample in app
I use LitePrefs in my note app [PureNote](https://github.com/duanze/PureNote).
# Special Thanks
LarsWerkman,[LicenseView](https://github.com/LarsWerkman/LicenseView)

# License
```
	Copyright 2015 Duanze
	
  	 Licensed under the Apache License, Version 2.0 (the "License");
   	you may not use this file except in compliance with the License.
   	You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   	Unless required by applicable law or agreed to in writing, software
   	distributed under the License is distributed on an "AS IS" BASIS,
   	WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   	See the License for the specific language governing permissions and
   	limitations under the License.
```
