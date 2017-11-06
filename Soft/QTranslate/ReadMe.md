## 说明
Qtranslate6.4.zip 基于 [Qtranslate6.4](http://quest-app.appspot.com/home) 修改配置文件得来。
- 修改 ./data/Option.json
```js
{
 "Advanced": {
      "GoogleDomain": "cn"
   }
}
```

- 修改 ./Services/Common.js
```js
function strFormatLen(str, length) {
	var offset = length - str.length;
	while( offset > 0){
		str += " ";
		offset--;
	}
	return str;
}
```

- 修改 ./Services/Baidu/Service.js
```js
function serviceTranslateResponse(showContent, response, fromLanguage, toLanguage) {
    var contentJson = parseJSON(response);
    var i, j;
    showContent = "";
    if (contentJson) {
        if (contentJson.trans_result) {
            fromLanguage = contentJson.trans_result.data;
            for (i = 0; i < fromLanguage.length; i++) showContent += fromLanguage[i].dst + Const.NL;
            fromLanguage = languageFromCode(contentJson.trans_result.from);
            toLanguage = languageFromCode(contentJson.trans_result.to)
        }
        if (contentJson.dict_result && contentJson.dict_result.simple_means) {
            var meanData = contentJson.dict_result.simple_means;
            showContent += Const.NL;
            showContent += meanData.word_name;
            if (meanData && meanData.symbols)
                for (i = 0; i < meanData.symbols.length; i++) {
                    var resultList = meanData.symbols[i];
                    if (resultList && resultList.parts) {
                        if (resultList.word_symbol)  {
                            //音标(拼音)
                            showContent += " [" + resultList.word_symbol + "]";
                            for (j = 0; j < resultList.parts.length; j++) {
                                var data = resultList.parts[j];
                                showContent += Const.NL;
                                data.part_name && (showContent += "[" + data.part_name + "] ");
                                showContent += data.means.map(function (a) {
                                    return a.word_mean
                                }).join("; ")
                            }
                        } else {
                            for (j = 0; j < resultList.parts.length; j++) {
                                var data = resultList.parts[j];
                                showContent += Const.NL;
                                showContent += strFormatLen(data.part,6);
                                showContent += data.means.toString()+"; "
                            }
                        }
                    }
                }
        }
    }
    return new ResponseData(showContent, fromLanguage, toLanguage)
}
```