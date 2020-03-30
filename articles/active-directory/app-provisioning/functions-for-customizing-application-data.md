---
title: 撰寫運算式以在 Azure AD 中進行屬性對應
description: 了解在 Azure Active Directory 中自動化佈建 SaaS 應用程式物件的期間，如何使用運算式對應將屬性值轉換成可接受的格式。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc41a18063202bfefb9ddf7238de17fc691984af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612150"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>在 Azure Active Directory 中撰寫屬性對應的運算式
當您設定佈建到 SaaS 應用程式時，您可以指定的其中一種屬性對應類型是運算式對應。 您必須撰寫類似指令碼的運算式，以便讓您將使用者的資料轉換成 SaaS 應用程式更能接受的格式。

## <a name="syntax-overview"></a>語法概觀
屬性對應的運算式語法是 Visual Basic for Applications (VBA) 函式。

* 整個運算式必須以函式定義，由函式名稱後面接著以括號括住的引數組成： <br>
  *函數名稱（`<<argument 1>>` `<<argument N>>`*
* 您可以在函式內互相巢狀函式。 例如： <br> *功能一（功能二））`<<argument1>>`*
* 您可以將三種不同類型的引數傳入函式：
  
  1. 屬性，必須以方括弧括住。 例如：[attributeName]
  2. 字串常數，必須以雙引號括住。 例如："United States"
  3. 其他函式。 例如：函數一（，`<<argument1>>`功能二））`<<argument2>>`
* 對於字串常數，如果您在字串中需要反斜線 ( \ ) 或引號 ( " ) ，則必須使用反斜線 ( \ ) 符號逸出。 例如："公司名稱："Contoso"" \\ \\

## <a name="list-of-functions"></a>函式的清單
[附屬](#append)&nbsp;&nbsp;[Count](#count)[Coalesce](#coalesce)&nbsp;[BitAnd](#bitand)&nbsp;&nbsp;[CStr](#cstr)[CBool](#cbool)&nbsp;&nbsp;[ConvertToBase64](#converttobase64)&nbsp;[ConvertToUTF8Hex](#converttoutf8hex)&nbsp;位和&nbsp;&nbsp;CBool&nbsp;&nbsp;組合&nbsp;轉換到&nbsp;Base64&nbsp;轉換到UTF8Hex&nbsp;計數 Cstr &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ ](#guid) &nbsp; [ ](#datefromnum) &nbsp; &nbsp; [ ](#iif) &nbsp; [ ](#instr) &nbsp; [ ](#isnull) [ ](#formatdatetime) &nbsp; &nbsp; &nbsp; &nbsp;日期&nbsp;從num&nbsp;&nbsp;[IsNullOrEmpty](#isnullorempty)格式日期吉德&nbsp;&nbsp;IIF &nbsp; instr &nbsp; isnull 是nullor空&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;[是存在](#ispresent)&nbsp;&nbsp;&nbsp;[Mid](#mid)[Join](#join)[Left](#left)[Not](#not)[Item](#item)[IsString](#isstring)&nbsp;[NormalizeDiacritics](#normalizediacritics)是字串&nbsp;專案加入左中&nbsp;正常化符號不&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [ ](#replace) &nbsp; [ ](#split) [ ](#singleapproleassignment) [ ](#removeduplicates) &nbsp; [SelectUniqueValue](#selectuniquevalue)刪除重複替換&nbsp;&nbsp;選擇唯&nbsp;一價值&nbsp;單應用程式分配拆分&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [條帶空格](#stripspaces)&nbsp;&nbsp;&nbsp;&nbsp;[Switch](#switch)&nbsp;[ToLower](#tolower)[Word](#word)[ToUpper](#toupper)切換到&nbsp;"下&nbsp;到&nbsp;上字"&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;

---
### <a name="append"></a>附加
**函式：**<br> Append(source, suffix)

**描述:**<br> 取出 source 字串值並在結尾附加尾碼。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |String |通常為 source 物件的屬性名稱。 |
| **suffix** |必要 |String |您想要附加至 source 值結尾的字串。 |

---
### <a name="bitand"></a>BitAnd
**函式：**<br> 位和（值 1，值 2）

**描述:**<br> 此函式會將這兩個參數轉換為二進位表示法，並將某一個位元設為：

0 - 如果 value1 和 value2 中有一或兩個對應位元為 0                                                  
1 - 如果這兩個對應位元都是 1。                                    

換句話說，除非這兩個參數的對應位元都是 1，否則在所有情況下都會傳回 0。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **值1** |必要 |num |應使用值 2 的數值|
| **值2** |必要 |num |應使用值 1 的 AND 的數值|

**例子：**<br>
BitAnd(&HF, &HF7)                                                                                
11110111 和 0000011 = 00000111 = 00000111 使 BitAnd 返回 7，二進位值 00000111

---
### <a name="cbool"></a>CBool
**函式：**<br> CBool（表達）

**描述:**<br> CBool 返回基於計算運算式的布林。 如果運算式計算為非零值，則 CBool 返回 True，否則返回 False。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **運算式** |必要 | expression | 任何有效的運算式 |

**例子：**<br>
CBool（[屬性1]=[屬性2]）                                                                    
如果這兩個屬性的值相同，即會傳回 True。

---
### <a name="coalesce"></a>Coalesce
**函式：**<br> 合併（源 1，源 2，...，預設值）

**描述:**<br> 返回第一個不是 Null 的源值。 如果所有參數都為 Null，並且存在預設值，則將返回預設值。 如果所有參數都是 Null，並且不存在預設值，則合併返回 Null。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源1 ...源N** | 必要 | String |必需的可變次數。 通常為 source 物件的屬性名稱。 |
| **defaultValue** | 選用 | String | 所有源值為 Null 時使用的預設值。 可以是空字串 ("")。

---
### <a name="converttobase64"></a>ConvertToBase64
**函式：**<br> 轉換到Base64（源）

**描述:**<br> ConvertToBase64 函式會將字串轉換為 Unicode Base64 字串。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |String |要轉換為基 64 的字串|

**例子：**<br>
ConvertToBase64("Hello world!")                                                                                                        
 傳回 "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**函式：**<br> 轉換到UTF8Hex（源）

**描述:**<br> ConvertToUTF8Hex 函式會將字串轉換為 UTF8 十六進位編碼值。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |String |要轉換為 UTF8 十六進位的字串|

**例子：**<br>
ConvertToUTF8Hex("Hello world!")                                                                                                         
 傳回 48656C6C6F20776F726C6421

---
### <a name="count"></a>Count
**函式：**<br> 計數（屬性）

**描述:**<br> Count 函式會傳回多重值屬性中的元素個數

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **屬性** |必要 |屬性 |多值屬性，將元素計數|

---
### <a name="cstr"></a>CStr
**函式：**<br> CStr（值）

**描述:**<br> CStr 函數將值轉換為字串資料類型。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **值** |必要 | 數值、參考或布林 | 可以是數值、參考屬性或布林值。 |

**例子：**<br>
CStr([dn])                                                            
返回"cn=Joe，dc_contoso，dc_com"

---
### <a name="datefromnum"></a>DateFromNum
**函式：**<br> 日期從Num（值）

**描述:**<br> DateFromNum 函式會將 AD 日期格式的值轉換為 DateTime 類型。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **值** |必要 | Date | 要轉換為日期時間類型的 AD 日期 |

**例子：**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
 傳回代表 2012-01-01 23:00:00 的 DateTime

---
### <a name="formatdatetime"></a>FormatDateTime
**函式：**<br>  FormatDateTime(source, inputFormat, outputFormat)

**描述:**<br> 從一種格式取出日期字串，並將它轉換成不同的格式。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |String |通常為 source 物件的屬性名稱。 |
| **inputFormat** |必要 |String |source 值的預期格式。 有關支援的格式，請參閱[https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)。 |
| **outputFormat** |必要 |String |輸出日期的格式。 |

---
### <a name="guid"></a>Guid
**函式：**<br> Guid()

**描述:**<br> 函式 GUID 會產生新的隨機 GUID

---
### <a name="iif"></a>IIF
**函式：**<br> IIF（條件，值如果真，值如果假）

**描述:**<br> IIF 函式會根據指定的條件傳回其中一組可能值。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **條件** |必要 |變數或運算式 |可評估為 true 或 false 的任何值或運算式。 |
| **valueIfTrue** |必要 |變數或字串 | 條件評估為 True 時所傳回的值。 |
| **valueIfFalse** |必要 |變數或字串 |條件評估為 False 時所傳回的值。|

**例子：**<br>
IIF（[國家]"美國"，"國家"，[部門]）

---
### <a name="instr"></a>InStr
**函式：**<br> InStr（值 1，值 2，開始，比較類型）

**描述:**<br> InStr 函式會在字串中尋找第一個出現的子字串

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **值1** |必要 |String |要搜索的字串 |
| **值2** |必要 |String |要找到的字串 |
| **start** |選用 |整數  |起始位置以查找子字串|
| **比較類型** |選用 |例舉 |可以是 vb 文本比較或 vbBinary 比較 |

**例子：**<br>
InStr("The quick brown fox","quick")                                                                             
 評估為 5

InStr("repEated","e",3,vbBinaryCompare)                                                                                  
 評估為 7

---
### <a name="isnull"></a>IsNull
**函式：**<br> IsNull（運算式）

**描述:**<br> 如果運算式評估為 Null，則 IsNull 函式會傳回 True。 針對屬性，Null 表示該屬性不存在。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **運算式** |必要 |expression |要計算的運算式 |

**例子：**<br>
IsNull([displayName])                                                                                                
如果屬性不存在，則返回 True

---
### <a name="isnullorempty"></a>是空的
**函式：**<br> 空為（運算式）

**描述:**<br> 如果運算式為 Null 或空字串，則 IsNullOrEmpty 函式會傳回 True。 針對屬性，如果屬性不存在，或存在但為空字串，即會評估為 True。
此函式的相反函式名稱為 IsPresent。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **運算式** |必要 |expression |要計算的運算式 |

**例子：**<br>
是空（[顯示名稱]）                                               
如果屬性不存在或為空字串，則返回 True

---
### <a name="ispresent"></a>IsPresent
**函式：**<br> 存在（運算式）

**描述:**<br> 如果運算式評估為非 Null 且不是空字串，則 IsPresent 函式會傳回 True。 這個函式的相反函式名稱為 IsNullOrEmpty。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **運算式** |必要 |expression |要計算的運算式 |

**例子：**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])

---
### <a name="isstring"></a>IsString
**函式：**<br> 正弦（運算式）

**描述:**<br> 如果運算式可評估為字串類型，則 IsString 函式會評估為 True。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **運算式** |必要 |expression |要計算的運算式 |

---
### <a name="item"></a>Item
**函式：**<br> 專案（屬性、索引）

**描述:**<br> Item 函式會從多重值字串/屬性傳回一個項目。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **屬性** |必要 |屬性 |要搜索的多值屬性 |
| **指數** |必要 |整數  | 索引到多值字串中的項|

**例子：**<br>
專案（[代理位址]，1）

---
### <a name="join"></a>Join
**函式：**<br> Join(separator, source1, source2, …)

**描述:**<br> Join() 類似於 Append()，除了其在單一字串中結合多個**來源**字串值，以及使用**分隔符號**字串隔開各個值。

如果其中一個 source 值是多重值屬性，則該屬性中的每個值會聯結在一起，並以分隔符號值分隔。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **分離** |必要 |String |用來分隔串連成一個字串的 source 值的字串。 如果不需要分隔符號，可以是 ""。 |
| **源1 ...源N** |必要，變動次數 |String |要聯結在一起的字串值。 |

---
### <a name="left"></a>Left
**函式：**<br> 左側（弦，串）

**描述:**<br> Left 函式會從字串左邊傳回指定的字元數。 如果 numChars = 0，會傳回空字串。
如果 numChars < 0，會傳回輸入字串。
如果 string 為 Null，會傳回空字串。
如果 string 包含的字元數比 numChars 中指定的數目少，即會傳回與 string 完全相同的字串 (也就是，包含參數 1 中的所有字元)。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **字串** |必要 |屬性 | 要從 中返回字元的字串 |
| **納姆查理斯** |必要 |整數  | 標識從字串開頭（左）返回的字元數的數位|

**例子：**<br>
左圖（"約翰·多伊"，3）                                                            
返回"Joh"

---
### <a name="mid"></a>Mid
**函式：**<br> Mid(source, start, length)

**描述:**<br> 傳回 source 值的子字串。 子字串是只包含 source 字串某些字元的字串。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |String |通常為屬性的名稱。 |
| **start** |必要 |integer |**來源**字串中的索引，代表子字串的開頭。 字串第一個字元的索引為 1，第二個字元的索引為 2，依此類推。 |
| **長度** |必要 |integer |子字串的長度。 如果長度超出 **source** 字串結尾，函式會傳回從 **start** 索引一直到 **source** 字串結尾的子字串。 |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**函式：**<br> NormalizeDiacritics(source)

**描述:**<br> 需要一個字串引數。 傳回字串，但是當中所有的變音符號字元皆被同等的非變音符號字元取代。 通常用於將包含變音符號字元 (重音符號) 的名字和姓氏，轉換成可用於如使用者主體名稱、SAM 帳戶名稱，與電子郵件地址等各種使用者識別碼中的有效值。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |String | 通常是名字或姓氏屬性。 |

---
### <a name="not"></a>Not
**函式：**<br> Not(source)

**描述:**<br> 切換**來源**的布林值。 若**來源**值為 "*True*"，則傳回 "*false*"。 反之，則傳回 "*True*"。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |Boolean String |預期的**來源**值為 "True" 或 "False"。 |

---
### <a name="numfromdate"></a>NumFromDate
**函式：**<br> 數位從日期（值）

**描述:**<br> NumFromDate 函數將 DateTime 值轉換為活動目錄格式，該格式是設置[帳戶過期](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires)等屬性所必需的。 使用此函數可將從雲 HR 應用（如工作日和成功因素）收到的 DateTime 值轉換為其等效的 AD 表示形式。 

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **值** |必要 | String | 受支援格式的日期時間字串。 如需支援的格式，請參閱https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx。 |

**例子：**<br>
* 工作日示例 <br>
  假設您要將屬性*ContractEndDate*從工作日（以格式*為 2020-12-31-08：00*映射到 AD 中的*帳戶過期*欄位）進行映射，下面介紹如何使用此函數並更改時區偏移以匹配地區設定。 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* 成功因素示例 <br>
  假設您要映射屬性*endDate*從成功因數這是在格式*M/d/yyyyhhhhh：ss tt*帳戶*過期*欄位在 AD 中， 下面是如何使用此函數和更改時區偏移以匹配您的地區設定.
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**函式：**<br> 刪除重複項（屬性）

**描述:**<br> RemoveDuplicates 函式會接受多重值的字串，並確定每個值都是唯一的。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **屬性** |必要 |多值屬性 |將刪除重複項的多值屬性|

**例子：**<br>
RemoveDuplicates([proxyAddresses])                                                                                                       
返回已刪除所有重複值的已清理代理位址屬性

---
### <a name="replace"></a>Replace
**函式：**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**描述:**<br>
取代字串內的值。 根據提供的參數而以不同的方式運作：

* 若提供 **oldValue** 及 **replacementValue**：
  
  * 將**源**中**所有舊值**事件替換為**替換值**
* 若提供 **oldValue** 及**範本**：
  
  * 將**範本**中全部出現的 **oldValue** 取代為**來源**值
* 提供 **regexPattern** 和 **replacementValue** 時：

  * 函式會將 **regexPattern** 套用至 **source** 字串，而您可以使用 regex 群組名稱來建構 **replacementValue** 的字串
* 提供 **regexPattern**、**regexGroupName**、**replacementValue** 時：
  
  * 函式會將 **regexPattern** 套用至 **source** 字串，並將所有符合 **regexGroupName** 的值取代為 **replacementValue**
* 提供 **regexPattern**、**regexGroupName**、**replacementAttributeName** 時：
  
  * 如果 **source** 沒有值，則傳回 **source**
  * 如果 **source** 具有值，則函式會將 **regexPattern** 套用至 **source** 字串，並將所有符合 **regexGroupName** 的值取代為與 **replacementAttributeName** 相關聯的值

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |String |通常來自**源**物件的屬性的名稱。 |
| **oldValue** |選用 |String |在 **source** 或 **template** 中要被取代的值。 |
| **regexPattern** |選用 |String |用於比對**來源**中要取代之值的 Regex 模式。 或者，如果使用了 **replacementPropertyName**，則為從 **replacementPropertyName** 擷取值的模式。 |
| **regexGroupName** |選用 |String |**regexPattern**內的群組名稱。 只有在使用了 **replacementPropertyName** 時，我們才會從 **replacementPropertyName** 擷取此群組的值作為 **replacementValue**。 |
| **replacementValue** |選用 |String |要取代舊值的新值。 |
| **replacementAttributeName** |選用 |String |要用於取代值的屬性名稱 |
| **範本** |選用 |String |當提供**範本**值時，我們將在範本中查找**舊值**並將其替換為**源**值。 |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**函式：**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**描述:**<br> 至少需要兩個引數，也就是使用運算式定義的唯一值產生規則。 此函式會評估每個規則，接著檢查所產生的值在目標應用程式/目錄中的唯一性。 將會傳回所找到的第一個唯一值。 如果所有值都已存在於目標中，則項目會進行委付且原因會記錄於稽核記錄中。 可提供的引數數目沒有上限。

> [!NOTE]
> - 這是最上層函式，無法巢狀處理。
> - 此函式無法套用至具有相符優先順序的屬性。  
> - 此函式只能用於建立項目。 搭配屬性使用此函式時，請將 [套用對應]**** 屬性設定為 [僅限物件建立期間]****。
> - 此函式目前僅支援「Workday 到 Active Directory 的使用者佈建」。 您無法將其與其他佈建應用程式搭配使用。 


**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |至少需要 2 個，沒有上限 |String | 要評估的唯一值產生規則清單。 |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**函式：**<br> SingleAppRoleAssignment([appRoleAssignments])

**描述:**<br> 從針對特定應用程式指派給使用者的所有 appRoleAssignment 清單中傳回單一 appRoleAssignment。 需有此函式才能將 appRoleAssignments 物件轉換成單一角色名稱字串。 請注意，最佳做法是確定一次只有一個 appRoleAssignment 會指派給一位使用者，如果多個角色受到指派，則傳回的角色字串可能不可預測。 

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |必要 |String |**[appRoleAssignments]** 物件。 |

---
### <a name="split"></a>Split
**函式：**<br> Split(source, delimiter)

**描述:**<br> 使用指定的分隔符號字元將字串分割成多重值陣列。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |String |要更新的**來源**值。 |
| **分隔符號** |必要 |String |指定將用來分割字串的字元 (範例：",") |

---
### <a name="stripspaces"></a>StripSpaces
**函式：**<br> StripSpaces(source)

**描述:**<br> 移除 source 字串中的所有空格 (" ") 字元。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |String |要更新的**來源**值。 |

---
### <a name="switch"></a>Switch
**函式：**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**描述:**<br> 若**來源**值符合 **key**，則傳回該 **key** 的 **value**。 若**來源**值不符合任何 key，則傳回 **defaultValue**.。  **key** 和 **value** 參數必須永遠成對出現。 函式必須要有偶數數目的參數。 該函數不應用於引用屬性（如管理器）。 

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |String |**Source** 值。 |
| **defaultValue** |選用 |String |當 source 不符合任何 key 時要使用的預設值。 可以是空字串 ("")。 |
| **關鍵** |必要 |String |要與 **source** 值比較的 **key**。 |
| **值** |必要 |String |符合 key 的 **source** 的取代值。 |

---
### <a name="tolower"></a>ToLower
**函式：**<br> ToLower(source, culture)

**描述:**<br> 採用 *source* 字串值，並使用所指定的文化特性 (Culture) 規則將其轉換成小寫。 如果未指定任何 *culture* 資訊，則會使用不因文化特性而異。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |String |通常為 source 物件的屬性名稱 |
| **文化** |選用 |String |根據 RFC 4646，文化特性 (Culture) 名稱的格式為 *languagecode2-country/regioncode2*，其中 *languagecode2* 是兩個字母的語言代碼，而 *country/regioncode2* 則是兩個字母的子文化特性代碼。 範例包括 ja-JP 代表日文 (日本)，en-US 代表英文 (美國)。 如果沒有兩個字母的語言代碼可供使用，則會使用衍生自 ISO 639-2 的三個字母代碼。|

---
### <a name="toupper"></a>ToUpper
**函式：**<br> ToUpper(source, culture)

**描述:**<br> 採用 *source* 字串值，並使用所指定的文化特性 (Culture) 規則將其轉換成大寫。 如果未指定任何 *culture* 資訊，則會使用不因文化特性而異。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **源** |必要 |String |通常為 source 物件的屬性名稱。 |
| **文化** |選用 |String |根據 RFC 4646，文化特性 (Culture) 名稱的格式為 *languagecode2-country/regioncode2*，其中 *languagecode2* 是兩個字母的語言代碼，而 *country/regioncode2* 則是兩個字母的子文化特性代碼。 範例包括 ja-JP 代表日文 (日本)，en-US 代表英文 (美國)。 如果沒有兩個字母的語言代碼可供使用，則會使用衍生自 ISO 639-2 的三個字母代碼。|

---
### <a name="word"></a>Word
**函式：**<br> 單詞（字串、字數、分隔符號）

**描述:**<br> Word 函式會根據描述要使用之分隔符號及要傳回之字數的參數，傳回字串內含的單字。 string 內以 delimiters 其中一個字元來分隔之字元的每個字串，都會被識別為單字：

如果 number < 1，會傳回空字串。
如果 string 為 Null，會傳回空字串。
如果 string 所含的字數少於 number 個字，或者 string 不包含任何 delimeters 所識別的單字，就會傳回空字串。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **字串** |必要 |多值屬性 |要返回單詞的字串。|
| **字數** |必要 | 整數  | 標識應返回的字型大小的數位|
| **分隔符號** |必要 |String| 表示應用於標識單詞的分隔符號的字串|

**例子：**<br>
字（"快速棕色狐狸"，3，""）                                                                                       
 傳回 "brown"

單詞（"這個，字串！&許多分隔符號"，"3"，！&"）                                                                       
返回"有"

---

## <a name="examples"></a>範例
### <a name="strip-known-domain-name"></a>刪去已知的網域名稱
您必須從使用者的電子郵件刪去已知的網域名稱，得到使用者名稱。 <br>
例如，如果網域是 "contoso.com"，您可以使用下列運算式：

**表達：** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**範例輸入/輸出：** <br>

* **輸入** (mail)："john.doe@contoso.com"
* **輸出**："john.doe"

### <a name="append-constant-suffix-to-user-name"></a>附加常數尾碼到使用者名稱
如果您使用 Salesforce 沙箱，您可能需要附加額外的尾碼到您所有的使用者名稱，才能進行同步處理。

**表達：** <br>
`Append([userPrincipalName], ".test")`

**範例輸入/輸出：** <br>

* **輸入**：(userPrincipalName)："John.Doe@contoso.com"
* **輸出**："John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>串連部分名字和姓氏產生使用者別名
您必須取出使用者名字的前 3 個字母和使用者姓氏的前 5 個字母來產生使用者別名。

**表達：** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**範例輸入/輸出：** <br>

* **輸入** (givenName)："John"
* **輸入** (surname)："Doe"
* **輸出**："喬伊多"

### <a name="remove-diacritics-from-a-string"></a>移除字串中的變音符號
您必須以不含重音符號的同等字元取代含重音符號的字元。

**表達：** <br>
NormalizeDiacritics([givenName])

**範例輸入/輸出：** <br>

* **輸入** (givenName)： "Zoë"
* **輸出**：" 佐伊"

### <a name="split-a-string-into-a-multi-valued-array"></a>將字串分割成多重值陣列
您必須採用以逗號分隔的字串清單，然後將其分割成可插入到多重值屬性 (例如 Salesforce 的 PermissionSets 屬性) 的陣列。 在此範例中，已在 Azure AD 的 extensionAttribute5 中填入權限集合清單。

**表達：** <br>
Split([extensionAttribute5], ",")

**範例輸入/輸出：** <br>

* **輸入**（擴充屬性5）："許可權集一，滲透集二"
* **輸出**：["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>以特定格式將日期輸出為字串
您想要以特定格式傳送日期到 SaaS 應用程式。 <br>
例如，您要格式化 ServiceNow 的日期。

**表達：** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**範例輸入/輸出：**

* **輸入** (extensionAttribute1)："20150123105347.1Z"
* **輸出**：" 2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>根據預先定義的一組選項取代值

您必須根據儲存在 Azure AD 中的狀態碼定義使用者的時區。 <br>
如果狀態碼不符合任何預先定義的選項，則使用 "Australia/Sydney" 的預設值。

**表達：** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**範例輸入/輸出：**

* **輸入** (state)："QLD"
* **輸出**："Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>使用規則運算式來取代字元
您需要尋找符合規則運算式的字元，然後將它們移除。

**表達：** <br>

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**範例輸入/輸出：**

* **輸入** (mailNickname: "john_doe72"
* **輸出**：" "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>將產生的 userPrincipalName (UPN) 值轉換成小寫
在以下範例中，會將 PreferredFirstName 與 PreferredLastName 來源欄位串連來產生 UPN 值，然後 ToLower 函式會對產生的字串進行操作以將所有字元轉換成小寫。 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**範例輸入/輸出：**

* **輸入** (PreferredFirstName)："John"
* **輸入** (PreferredLastName)："Smith"
* **輸出**："john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>產生 userPrincipalName (UPN) 屬性的唯一值
根據使用者的名字、中間名和姓氏，您必須先產生 UPN 屬性的值並檢查其在目標 AD 目錄中的唯一性，再將此值指派給 UPN 屬性。

**表達：** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**範例輸入/輸出：**

* **輸入** (PreferredFirstName)："John"
* **輸入** (PreferredLastName)："Smith"
* **輸出**："John.Smith@contoso.com如果 目錄中不存在 的John.Smith@contoso.comUPN 值
* **輸出**："J.Smith@contoso.com如果目錄中已存在 的John.Smith@contoso.comUPN 值
* **輸出**Jo.Smith@contoso.com：""如果目錄中已存在上述兩個 UPN 值

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>流郵件值（如果不是 Null），否則流使用者主名稱
如果郵件屬性存在，則希望將其流式傳遞。 如果不是，您希望改為流使用者主體名稱的值。

**表達：** <br>
`Coalesce([mail],[userPrincipalName])`

**範例輸入/輸出：** <br>

* **輸入**（郵件）：空
* **輸入**（使用者主名）：John.Doe@contoso.com"
* **輸出**："John.Doe@contoso.com"

## <a name="related-articles"></a>相關文章
* [自動化 SaaS 應用程式使用者佈建/解除佈建](../app-provisioning/user-provisioning.md)
* [自訂使用者佈建的屬性對應](../app-provisioning/customize-application-attributes.md)
* [適用於使用者佈建的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
* [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [帳戶預配通知](../app-provisioning/user-provisioning.md)
* [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)
