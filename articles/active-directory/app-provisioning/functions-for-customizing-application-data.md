---
title: 在 Azure Active Directory 中撰寫屬性對應之運算式的參考
description: 了解在 Azure Active Directory 中自動化佈建 SaaS 應用程式物件的期間，如何使用運算式對應將屬性值轉換成可接受的格式。 包含函式的參考清單。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 02/05/2020
ms.author: kenwith
ms.custom: contperf-fy21q2
ms.openlocfilehash: 462f60bbae9fd7b61993a2ccccd40fa5ca4ccc28
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012962"
---
# <a name="reference-for-writing-expressions-for-attribute-mappings-in-azure-ad"></a>在 Azure AD 中撰寫屬性對應之運算式的參考

當您設定佈建到 SaaS 應用程式時，您可以指定的其中一種屬性對應類型是運算式對應。 您必須撰寫類似指令碼的運算式，以便讓您將使用者的資料轉換成 SaaS 應用程式更能接受的格式。

## <a name="syntax-overview"></a>語法概觀

屬性對應的運算式語法是 Visual Basic for Applications (VBA) 函式。

* 整個運算式必須以函式來定義，其中包含名稱後面接著以括弧括住的引數： *FunctionName (`<<argument 1>>` ， `<<argument N>>`)*
* 您可以在函式內互相巢狀函式。 例如：  *FunctionOne (FunctionTwo (`<<argument1>>`) # B3*
* 您可以將三種不同類型的引數傳入函式：
  
  1. 屬性，必須以方括弧括住。 例如：[attributeName]
  2. 字串常數，必須以雙引號括住。 例如："United States"
  3. 其他函式。 例如： FunctionOne (`<<argument1>>` ，FunctionTwo (`<<argument2>>`) # A3
* 對於字串常數，如果您在字串中需要反斜線 ( \ ) 或引號 ( " ) ，則必須使用反斜線 ( \ ) 符號逸出。 例如： "Company name： \\ " Contoso \\ ""
* 語法會區分大小寫，當您在函式中輸入字串時，必須考慮這種語法，而將它們直接複製到此處。 

## <a name="list-of-functions"></a>函式的清單

[](#append) &nbsp; &nbsp; 附加 &nbsp; &nbsp;[](#bitand) &nbsp; &nbsp; BitAnd &nbsp; &nbsp;[](#cbool) &nbsp; &nbsp; CBool &nbsp; &nbsp;[](#coalesce) &nbsp; &nbsp; 聯合 &nbsp; &nbsp;[](#converttobase64) &nbsp; &nbsp; ConvertToBase64 &nbsp; &nbsp;[](#converttoutf8hex) &nbsp; &nbsp; ConvertToUTF8Hex &nbsp; &nbsp;[](#count) &nbsp; &nbsp; 計數 &nbsp; &nbsp;[](#cstr) &nbsp; &nbsp; CStr &nbsp; &nbsp;[DateFromNum](#datefromnum) &nbsp;[](#formatdatetime) &nbsp; &nbsp; FormatDateTime &nbsp; &nbsp;[](#guid) &nbsp; &nbsp; Guid &nbsp; &nbsp;[](#iif) &nbsp; &nbsp; IIF &nbsp; &nbsp;[](#instr) &nbsp; &nbsp; InStr &nbsp; &nbsp;[](#isnull) &nbsp; &nbsp; IsNull &nbsp; &nbsp;[](#isnullorempty) &nbsp; &nbsp; IsNullOrEmpty &nbsp; &nbsp;[](#ispresent) &nbsp; &nbsp; IsPresent &nbsp; &nbsp;[](#isstring) &nbsp; &nbsp; IsString &nbsp; &nbsp;[](#item) &nbsp; &nbsp; 專案 &nbsp; &nbsp;[](#join) &nbsp; &nbsp; 聯結 &nbsp; &nbsp;[](#left) &nbsp; &nbsp; 左方 &nbsp; &nbsp;[Mid](#mid) &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp; &nbsp; &nbsp; &nbsp; [RemoveDuplicates](#removeduplicates) &nbsp; &nbsp; &nbsp; &nbsp; [Replace](#replace) &nbsp; &nbsp; &nbsp; &nbsp; [SelectUniqueValue](#selectuniquevalue) &nbsp; &nbsp; &nbsp; &nbsp; [SingleAppRoleAssignment](#singleapproleassignment) &nbsp; &nbsp; &nbsp; &nbsp; [Split](#split) &nbsp; &nbsp; &nbsp; &nbsp; [StripSpaces](#stripspaces) &nbsp; &nbsp; &nbsp; &nbsp; [Switch](#switch) &nbsp; &nbsp; &nbsp; &nbsp; [ToLower](#tolower) &nbsp; &nbsp; &nbsp; &nbsp; [ToUpper](#toupper) &nbsp; &nbsp; &nbsp; &nbsp; [Word](#word)

---
### <a name="append"></a>附加

**函數：** 附加 (來源，尾碼) 

**描述：** 採用來源字串值，並將尾碼附加至它的結尾。

**參數：**

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |String |通常為 source 物件的屬性名稱。 |
| **尾碼** |必要 |String |您想要附加至 source 值結尾的字串。 |

---
### <a name="bitand"></a>BitAnd
**函數：** BitAnd (value1，value2) 

**描述：** 此函式會將兩個參數都轉換成二進位標記法，並將位設為：

- 0 - 如果 value1 和 value2 中有一或兩個對應位元為 0
- 1 - 如果這兩個對應位元都是 1。

換句話說，除非這兩個參數的對應位元都是 1，否則在所有情況下都會傳回 0。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **value1** |必要 |num |應以 value2 AND'ed 的數值|
| **value2** |必要 |num |應使用 value1 AND'ed 的數值|

**例子：**
`BitAnd(&HF, &HF7)`

11110111和 00000111 = 00000111，因此傳回 `BitAnd` 7，二進位值為00000111。

---
### <a name="cbool"></a>CBool
**功能：** 
`CBool(Expression)`

**描述：**  
 `CBool`根據評估的運算式傳回布林值。 如果運算式評估為非零值，則傳回 `CBool` *True*，否則會傳回 *False*。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **expression** |必要 | 運算式 | 任何有效的運算式 |

**範例：** 
`CBool([attribute1] = [attribute2])`                                                                    
如果這兩個屬性的值相同，即會傳回 True。

---
### <a name="coalesce"></a>Coalesce
**函數：** 聯合 (source1.rc、>source2、...、defaultValue) 

**描述：** 傳回非 Null 的第一個來源值。 如果所有引數都是 Null，且 defaultValue 存在，將會傳回 defaultValue。 如果所有引數都是 Null，且不存在 defaultValue，則聯合會傳回 Null。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source1.rc .。。sourceN** | 必要 | String |必要、可變次數。 通常為 source 物件的屬性名稱。 |
| **defaultValue** | 選用 | String | 當所有來源值都是 Null 時，所要使用的預設值。 可以是空字串 ("")。

---
### <a name="converttobase64"></a>ConvertToBase64
**函數：** ConvertToBase64 (來源) 

**描述：** ConvertToBase64 函式會將字串轉換成 Unicode base64 字串。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |String |要轉換成基底64的字串|

**例子：**
`ConvertToBase64("Hello world!")`

 傳回 "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**函數：** ConvertToUTF8Hex (來源) 

**描述：** ConvertToUTF8Hex 函式會將字串轉換成 UTF8 十六進位編碼值。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |String |要轉換成 UTF8 十六進位的字串|

**例子：**
`ConvertToUTF8Hex("Hello world!")`

 傳回 48656C6C6F20776F726C6421

---
### <a name="count"></a>計數
**函數：** Count (屬性) 

**描述：** Count 函數會傳回多重值屬性中的元素數目。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **屬性** |必要 |屬性 |會計算元素的多重值屬性|

---
### <a name="cstr"></a>CStr
**函數：** CStr (值) 

**描述：** CStr 函數會將值轉換成字串資料類型。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **value** |必要 | 數值、參考或布林值 | 可以是數值、參考屬性或布林值。 |

**例子：**
`CStr([dn])`

傳回 "cn = Joe，dc = contoso，dc = com"

---
### <a name="datefromnum"></a>DateFromNum
**函數：** DateFromNum (值) 

**描述：** DateFromNum 函式會將 AD 日期格式的值轉換成日期時間類型。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **value** |必要 | Date | 要轉換為日期時間類型的 AD 日期 |

**例子：**
`DateFromNum([lastLogonTimestamp])`

`DateFromNum(129699324000000000)`

傳回代表2012年1月1日（11：00）的日期時間。

---
### <a name="formatdatetime"></a>FormatDateTime
**函數：** FormatDateTime (source，inputFormat，>outputformat) 

**描述：** 接受一種格式的日期字串，並將其轉換成不同的格式。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |String |通常為 source 物件的屬性名稱。 |
| **inputFormat** |必要 |String |source 值的預期格式。 如需支援的格式，請參閱 [/dotnet/standard/base-types/custom-date-and-time-format-strings](/dotnet/standard/base-types/custom-date-and-time-format-strings)。 |
| **>outputformat** |必要 |String |輸出日期的格式。 |

---
### <a name="guid"></a>Guid
**函數：** Guid ( # A1

**描述：** 函式 Guid 會產生新的隨機 GUID

---
### <a name="iif"></a>IIF
**函數：** IIF (condition、valueIfTrue、valueIfFalse) 

**描述：** IIF 函數會根據指定的條件傳回一組可能值的其中一個。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **條件** |必要 |變數或運算式 |可評估為 true 或 false 的任何值或運算式。 |
| **valueIfTrue** |必要 |變數或字串 | 條件評估為 True 時所傳回的值。 |
| **valueIfFalse** |必要 |變數或字串 |條件評估為 False 時所傳回的值。|

**例子：**
`IIF([country]="USA",[country],[department])`

---
### <a name="instr"></a>InStr
**函數：** InStr (value1、value2、start、compareType) 

**描述：** InStr 函式會在字串中尋找第一個出現的子字串

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **value1** |必要 |String |要搜尋的字串 |
| **value2** |必要 |String |要尋找的字串 |
| **開始** |選用 |整數 |尋找子字串的起始位置|
| **compareType** |選擇性 |列舉 |可以是 vbTextCompare 或 vbBinaryCompare |

**例子：**
`InStr("The quick brown fox","quick")`

評估為5

`InStr("repEated","e",3,vbBinaryCompare)`

 評估為 7

---
### <a name="isnull"></a>IsNull
**函數：** IsNull (運算式) 

**描述：** 如果運算式評估為 Null，則 IsNull 函數會傳回 true。 針對屬性，Null 表示該屬性不存在。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **expression** |必要 |運算式 |要評估的運算式 |

**例子：**
`IsNull([displayName])`

如果屬性不存在，則傳回 True。

---
### <a name="isnullorempty"></a>IsNullorEmpty
**函數：** IsNullOrEmpty (運算式) 

**描述：** 如果運算式為 null 或空字串，則 IsNullOrEmpty 函數會傳回 true。 針對屬性，如果屬性不存在，或存在但為空字串，即會評估為 True。
此函式的相反函式名稱為 IsPresent。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **expression** |必要 |運算式 |要評估的運算式 |

**例子：**
`IsNullOrEmpty([displayName])`

如果屬性不存在或為空字串，則傳回 True。

---
### <a name="ispresent"></a>IsPresent
**函數：** IsPresent (運算式) 

**描述：** 如果運算式評估為非 Null 且不是空的字串，則 IsPresent 函數會傳回 true。 這個函式的相反函式名稱為 IsNullOrEmpty。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **expression** |必要 |運算式 |要評估的運算式 |

**例子：**
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="isstring"></a>IsString
**函數：** IsString (運算式) 

**描述：** 如果運算式可以評估為字串類型，則 IsString 函數會評估為 True。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **expression** |必要 |運算式 |要評估的運算式 |

---
### <a name="item"></a>Item
**函數：** 專案 (屬性，索引) 

**描述：** Item 函數會從多重值字串/屬性傳回一個專案。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **屬性** |必要 |屬性 |要搜尋的多重值屬性 |
| **index** |必要 |整數 | 多重值字串中專案的索引|

**範例：** 
 `Item([proxyAddresses], 1)`傳回多重值屬性中的第二個專案。

---
### <a name="join"></a>Join
**函數：** Join (分隔符號、source1.rc、>source2、... ) 

**描述：** Join ( # A1 與 Append ( # A3 不同，不同之處在于它可以將多個 **來源** 字串值結合成單一字串，而每個值會以 **分隔符號** 字串分隔。

如果其中一個 source 值是多重值屬性，則該屬性中的每個值會聯結在一起，並以分隔符號值分隔。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **separator** |必要 |String |用來分隔串連成一個字串的 source 值的字串。 如果不需要分隔符號，可以是 ""。 |
| **source1.rc .。。sourceN** |必要，變動次數 |String |要聯結在一起的字串值。 |

---
### <a name="left"></a>Left
**函數：** Left (字串，NumChars) 

**描述：** Left 函數會從字串的左邊傳回指定的字元數。 如果 numChars = 0，會傳回空字串。
如果 numChars < 0，會傳回輸入字串。
如果 string 為 Null，會傳回空字串。
如果 string 包含的字元數比 numChars 中指定的數目少，即會傳回與 string 完全相同的字串 (也就是，包含參數 1 中的所有字元)。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **String** |必要 |屬性 | 要從中傳回字元的字串 |
| **NumChars** |必要 |整數 | 數位，識別要從字串的開頭 (左) 傳回的字元數|

**例子：**
`Left("John Doe", 3)`

 傳回 "Joh"。

---
### <a name="mid"></a>Mid
**函數：** Mid (source、start、length) 

**描述：** 傳回來源值的子字串。 子字串是只包含 source 字串某些字元的字串。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |String |通常為屬性的名稱。 |
| **開始** |必要 |integer |**來源** 字串中的索引，代表子字串的開頭。 字串第一個字元的索引為 1，第二個字元的索引為 2，依此類推。 |
| **length** (長度) |必要 |integer |子字串的長度。 如果長度超出 **source** 字串結尾，函式會傳回從 **start** 索引一直到 **source** 字串結尾的子字串。 |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**函數：** NormalizeDiacritics (來源) 

**描述：** 需要一個字串引數。 傳回字串，但是當中所有的變音符號字元皆被同等的非變音符號字元取代。 通常用於將包含變音符號字元 (重音符號) 的名字和姓氏，轉換成可用於如使用者主體名稱、SAM 帳戶名稱，與電子郵件地址等各種使用者識別碼中的有效值。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |String | 通常是名字或姓氏屬性。 |

---
### <a name="not"></a>Not
**函數：** 不 (來源) 

**描述：** 翻轉 **來源** 的布林值。 如果 **來源** 值為 True，則傳回 False。 否則，會傳回 True。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |Boolean String |預期的 **來源** 值為 "True" 或 "False"。 |

---
### <a name="numfromdate"></a>NumFromDate
**函數：** NumFromDate (值) 

**描述：** NumFromDate 函式會將日期時間值轉換成設定屬性（例如 [accountExpires](/windows/win32/adschema/a-accountexpires)）所需的 Active Directory 格式。 您可以使用此函式，將從 Workday 和 SuccessFactors 等雲端 HR 應用程式收到的日期時間值轉換為其對等的廣告標記法。 

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **value** |必要 | String | 支援格式的日期時間字串。 如需支援的格式，請參閱https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx。 |

**範例︰**
* Workday 範例假設您想要將 *ContractEndDate* 自 Workday 的屬性（格式為 *2020-12-31-08:00* 的格式）對應到 AD 中的 *accountExpires* 欄位，以下是您可以如何使用此函式，並將時區位移變更為符合您的地區設定。 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors 範例假設您想要將 SuccessFactors 的屬性 *結束* 項從（格式為 *M/d/yyyy hh： mm： ss tt* ）對應到 AD 中的 *accountExpires* 欄位，以下是您可以如何使用此函式，並將時區位移變更為符合您的地區設定。
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**函數：** RemoveDuplicates (屬性) 

**描述：** RemoveDuplicates 函數會採用多重值字串，並確定每個值都是唯一的。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **屬性** |必要 |多重值屬性 |將移除重複專案的多重值屬性|

**範例：** 
 `RemoveDuplicates([proxyAddresses])`傳回已清理的 proxyAddress 屬性，其中已移除所有重複的值。

---
### <a name="replace"></a>取代
**函數：** 取代 (source、oldValue、>RegExpattern、>RegExgroupname、replacementValue、>replacementattributename、template) 

**描述：** 以區分大小寫的方式取代字串中的值。 函數會根據提供的參數而有不同的行為：

* 若提供 **oldValue** 及 **replacementValue**：
  
  * 使用 **replacementValue** 取代 **來源** 中所有出現的 **oldValue**
* 若提供 **oldValue** 及 **範本**：
  
  * 將 **範本** 中全部出現的 **oldValue** 取代為 **來源** 值
* 提供 **regexPattern** 和 **replacementValue** 時：

  * 函式會將 **regexPattern** 套用至 **source** 字串，而您可以使用 regex 群組名稱來建構 **replacementValue** 的字串
* 提供 **regexPattern**、**regexGroupName**、**replacementValue** 時：
  
  * 函式會將 **regexPattern** 套用至 **source** 字串，並將所有符合 **regexGroupName** 的值取代為 **replacementValue**
* 提供 **regexPattern**、**regexGroupName**、**replacementAttributeName** 時：
  
  * 如果 **source** 沒有值，則傳回 **source**
  * 如果 **source** 具有值，則函式會將 **regexPattern** 套用至 **source** 字串，並將所有符合 **regexGroupName** 的值取代為與 **replacementAttributeName** 相關聯的值

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |String |通常是來自 **來源** 物件的屬性名稱。 |
| **oldValue** |選用 |String |在 **source** 或 **template** 中要被取代的值。 |
| **regexPattern** |選用 |String |用於比對 **來源** 中要取代之值的 Regex 模式。 或者，如果使用了 **replacementPropertyName**，則為從 **replacementPropertyName** 擷取值的模式。 |
| **regexGroupName** |選用 |String |**regexPattern** 內的群組名稱。 只有在使用了 **replacementPropertyName** 時，我們才會從 **replacementPropertyName** 擷取此群組的值作為 **replacementValue**。 |
| **replacementValue** |選用 |String |要取代舊值的新值。 |
| **replacementAttributeName** |選用 |String |要用於取代值的屬性名稱 |
| **範本** |選用 |String |提供 **範本** 值時，我們會尋找範本內部的 **oldValue** ，並以 **來源** 值取代。 |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**函數：** SelectUniqueValue (uniqueValueRule1、uniqueValueRule2、uniqueValueRule3、... ) 

**描述：** 至少需要兩個引數，這是使用運算式所定義的唯一值產生規則。 此函式會評估每個規則，接著檢查所產生的值在目標應用程式/目錄中的唯一性。 將會傳回所找到的第一個唯一值。 如果所有值都已存在於目標中，則項目會進行委付且原因會記錄於稽核記錄中。 可提供的引數數目沒有上限。


 - 這是最上層函式，無法巢狀處理。
 - 此函式無法套用至具有相符優先順序的屬性。   
 - 此函式只能用於建立項目。 搭配屬性使用此函式時，請將 [套用對應] 屬性設定為 [僅限物件建立期間]。
 - 此函式目前僅支援「Workday 到 Active Directory 的使用者佈建」。 您無法將其與其他佈建應用程式搭配使用。 


**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |至少需要 2 個，沒有上限 |String | 要評估的唯一值產生規則清單。 |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**函數：** SingleAppRoleAssignment ( [appRoleAssignments] ) 

**描述：** 從指派給指定應用程式之使用者的所有 appRoleAssignments 清單傳回單一 appRoleAssignment。 需有此函式才能將 appRoleAssignments 物件轉換成單一角色名稱字串。 請注意，最佳做法是確定一次只有一個 appRoleAssignment 會指派給一位使用者，如果多個角色受到指派，則傳回的角色字串可能不可預測。 

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |必要 |String |**[appRoleAssignments]** 物件。 |

---
### <a name="split"></a>分割
**函數：** 分割 (來源、分隔符號) 

**描述：** 使用指定的分隔符號，將字串分割成多重值陣列。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |String |要更新的 **來源** 值。 |
| **分隔符號** |必要 |String |指定將用來分割字串的字元 (範例：",") |

---
### <a name="stripspaces"></a>StripSpaces
**函數：** StripSpaces (來源) 

**描述：** 從來源字串中移除所有空間 ( "" ) 個字元。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |String |要更新的 **來源** 值。 |

---
### <a name="switch"></a>參數
**函數：** Switch (source、defaultValue、key1、value1、key2、value2、... ) 

**描述：** 當 **來源** 值符合索引 **鍵** 時，會傳回該索引 **鍵** 的 **值**。 若 **來源** 值不符合任何 key，則傳回 **defaultValue**.。  **key** 和 **value** 參數必須永遠成對出現。 函式必須要有偶數數目的參數。 函數不能用於參考屬性，例如 manager。 

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |String |**Source** 值。 |
| **defaultValue** |選用 |String |當 source 不符合任何 key 時要使用的預設值。 可以是空字串 ("")。 |
| **key** |必要 |String |要與 **source** 值比較的 **key**。 |
| **value** |必要 |String |符合 key 的 **source** 的取代值。 |

---
### <a name="tolower"></a>ToLower
**函數：** ToLower (來源，文化特性) 

**描述：** 採用 *來源* 字串值，並使用指定的文化特性規則將其轉換為小寫。 如果未指定任何 *culture* 資訊，則會使用不因文化特性而異。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |String |通常為 source 物件的屬性名稱 |
| **culture** |選用 |String |根據 RFC 4646，文化特性 (Culture) 名稱的格式為 *languagecode2-country/regioncode2*，其中 *languagecode2* 是兩個字母的語言代碼，而 *country/regioncode2* 則是兩個字母的子文化特性代碼。 範例包括 ja-JP 代表日文 (日本)，en-US 代表英文 (美國)。 如果沒有兩個字母的語言代碼可供使用，則會使用衍生自 ISO 639-2 的三個字母代碼。|

---
### <a name="toupper"></a>ToUpper
**函數：** ToUpper (來源，文化特性) 

**描述：** 採用 *來源* 字串值，並使用指定的文化特性規則將其轉換成大寫。 如果未指定任何 *culture* 資訊，則會使用不因文化特性而異。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source** |必要 |String |通常為 source 物件的屬性名稱。 |
| **culture** |選用 |String |根據 RFC 4646，文化特性 (Culture) 名稱的格式為 *languagecode2-country/regioncode2*，其中 *languagecode2* 是兩個字母的語言代碼，而 *country/regioncode2* 則是兩個字母的子文化特性代碼。 範例包括 ja-JP 代表日文 (日本)，en-US 代表英文 (美國)。 如果沒有兩個字母的語言代碼可供使用，則會使用衍生自 ISO 639-2 的三個字母代碼。|

---
### <a name="word"></a>Word
**函數：** Word (字串、WordNumber、分隔符號) 

**描述：** Word 函式會根據描述要使用之分隔符號的參數和要傳回的文字數目，傳回包含在字串中的單字。 string 內以 delimiters 其中一個字元來分隔之字元的每個字串，都會被識別為單字：

如果 number < 1，會傳回空字串。
如果 string 為 Null，會傳回空字串。
如果 string 所含的字數少於 number 個字，或者 string 不包含任何 delimeters 所識別的單字，就會傳回空字串。

**參數：** 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **String** |必要 |多重值屬性 |要從中傳回單字的字串。|
| **WordNumber** |必要 | 整數 | 識別應該傳回的文字數目的數位|
| **分隔符號** |必要 |String| 字串，代表應該用來識別單字的分隔符號 (s) |

**例子：**
`Word("The quick brown fox",3," ")`

傳回「棕色」。

`Word("This,string!has&many separators",3,",!&#")`

傳回 "has"。

---

## <a name="examples"></a>範例
### <a name="strip-known-domain-name"></a>刪去已知的網域名稱
您必須從使用者的電子郵件刪去已知的網域名稱，得到使用者名稱。 例如，如果網域是 "contoso.com"，您可以使用下列運算式：

**表達：** 
`Replace([mail], "@contoso.com", , ,"", ,)`

**範例輸入/輸出：** 

* **輸入** (mail)："john.doe@contoso.com"
* **輸出**："john.doe"

### <a name="append-constant-suffix-to-user-name"></a>附加常數尾碼到使用者名稱
如果您使用 Salesforce 沙箱，您可能需要附加額外的尾碼到您所有的使用者名稱，才能進行同步處理。

**表達：** 
`Append([userPrincipalName], ".test")`

**範例輸入/輸出：** 

* **輸入**：(userPrincipalName)："John.Doe@contoso.com"
* **輸出**： " John.Doe@contoso.com.test "

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>串連部分名字和姓氏產生使用者別名
您必須取出使用者名字的前 3 個字母和使用者姓氏的前 5 個字母來產生使用者別名。

**表達：** 
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**範例輸入/輸出：** 

* **輸入** (givenName)："John"
* **輸入** (surname)："Doe"
* **輸出**： "JohDoe"

### <a name="remove-diacritics-from-a-string"></a>移除字串中的變音符號
您必須以不含重音符號的同等字元取代含重音符號的字元。

**運算式：** NormalizeDiacritics ( [givenName] ) 

**範例輸入/輸出：** 

* **輸入** (givenName)： "Zoë"
* **輸出**： "Zoe"

### <a name="split-a-string-into-a-multi-valued-array"></a>將字串分割成多重值陣列
您必須採用以逗號分隔的字串清單，然後將其分割成可插入到多重值屬性 (例如 Salesforce 的 PermissionSets 屬性) 的陣列。 在此範例中，已在 Azure AD 的 extensionAttribute5 中填入權限集合清單。

**運算式：** Split ( [extensionAttribute5]、") 

**範例輸入/輸出：** 

* **輸入** (extensionAttribute5) ： "PermissionSetOne，PermissionSetTwo"
* **輸出**：["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>以特定格式將日期輸出為字串
您想要以特定格式傳送日期到 SaaS 應用程式。 例如，您要格式化 ServiceNow 的日期。

**表達：** 

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**範例輸入/輸出：**

* **輸入** (extensionAttribute1)："20150123105347.1Z"
* **輸出**： "2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>根據預先定義的一組選項取代值

您必須根據儲存在 Azure AD 中的狀態碼定義使用者的時區。 如果狀態碼不符合任何預先定義的選項，則使用 "Australia/Sydney" 的預設值。

**表達：** 
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**範例輸入/輸出：**

* **輸入** (state)："QLD"
* **輸出**："Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>使用規則運算式來取代字元
您需要尋找符合規則運算式的字元，然後將它們移除。

**表達：** 

Replace([mailNickname], , "[a-zA-Z_]*", , "", , )

**範例輸入/輸出：**

* **輸入** (mailNickname: "john_doe72"
* **輸出**： "72"

### <a name="convert-generated-userprincipalname-upn-value-to-lower-case"></a>將產生的 userPrincipalName (UPN) 值轉換成小寫
在以下範例中，會將 PreferredFirstName 與 PreferredLastName 來源欄位串連來產生 UPN 值，然後 ToLower 函式會對產生的字串進行操作以將所有字元轉換成小寫。 

`ToLower(Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"))`

**範例輸入/輸出：**

* **輸入** (PreferredFirstName)："John"
* **輸入** (PreferredLastName)："Smith"
* **輸出**： " john.smith@contoso.com "

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>產生 userPrincipalName (UPN) 屬性的唯一值
根據使用者的名字、中間名和姓氏，您必須先產生 UPN 屬性的值並檢查其在目標 AD 目錄中的唯一性，再將此值指派給 UPN 屬性。

**表達：** 

```ad-attr-mapping-expr
    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )
```

**範例輸入/輸出：**

* **輸入** (PreferredFirstName)："John"
* **輸入** (PreferredLastName)："Smith"
* **輸出**： " John.Smith@contoso.com " 如果的 UPN 值 John.Smith@contoso.com 尚未存在於目錄中
* **輸出**： " J.Smith@contoso.com " 如果的 UPN 值 John.Smith@contoso.com 已存在於目錄中
* **輸出**： Jo.Smith@contoso.com 如果上述兩個 UPN 值已存在於目錄中，則為 ""

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>如果不是 Null，則為 flow 郵件值，否則為 flow userPrincipalName
您想要傳送郵件屬性（如果有的話）。 如果不是，則您想要改為傳送 userPrincipalName 值。

**表達：** 
`Coalesce([mail],[userPrincipalName])`

**範例輸入/輸出：** 

* **輸入** (mail) ： Null
* **輸入** (userPrincipalName) ： " John.Doe@contoso.com "
* **輸出**： " John.Doe@contoso.com "

## <a name="related-articles"></a>相關文章
* [自動化 SaaS 應用程式使用者佈建/解除佈建](../app-provisioning/user-provisioning.md)
* [自訂使用者布建的屬性對應](../app-provisioning/customize-application-attributes.md)
* [適用於使用者佈建的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
* [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [帳戶布建通知](../app-provisioning/user-provisioning.md)
* [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)
