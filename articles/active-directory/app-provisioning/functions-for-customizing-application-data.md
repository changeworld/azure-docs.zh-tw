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
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77612150"
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>在 Azure Active Directory 中撰寫屬性對應的運算式
當您設定佈建到 SaaS 應用程式時，您可以指定的其中一種屬性對應類型是運算式對應。 您必須撰寫類似指令碼的運算式，以便讓您將使用者的資料轉換成 SaaS 應用程式更能接受的格式。

## <a name="syntax-overview"></a>語法概觀
屬性對應的運算式語法是 Visual Basic for Applications (VBA) 函式。

* 整個運算式必須以函式定義，由函式名稱後面接著以括號括住的引數組成： <br>
  *FunctionName(`<<argument 1>>`,`<<argument N>>`)*
* 您可以在函式內互相巢狀函式。 例如： <br> *FunctionOne(FunctionTwo(`<<argument1>>`))*
* 您可以將三種不同類型的引數傳入函式：
  
  1. 屬性，必須以方括弧括住。 例如：[attributeName]
  2. 字串常數，必須以雙引號括住。 例如："United States"
  3. 其他函式。 例如： FunctionOne （`<<argument1>>`、FunctionTwo （`<<argument2>>`））
* 對於字串常數，如果您在字串中需要反斜線 ( \ ) 或引號 ( " ) ，則必須使用反斜線 ( \ ) 符號逸出。 例如： "Company name： \\" Contoso\\""

## <a name="list-of-functions"></a>函式的清單
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [BitAnd](#bitand) &nbsp;&nbsp;&nbsp;&nbsp; [CBool](#cbool) &nbsp;&nbsp;&nbsp;&nbsp; [Coalesce](#coalesce) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToBase64](#converttobase64) &nbsp;&nbsp;&nbsp;&nbsp; [ConvertToUTF8Hex](#converttoutf8hex) &nbsp;&nbsp;&nbsp;&nbsp; [Count](#count) &nbsp;&nbsp;&nbsp;&nbsp; [CStr](#cstr) &nbsp;&nbsp;&nbsp;&nbsp; [DateFromNum](#datefromnum) &nbsp;[FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Guid](#guid) &nbsp;&nbsp;&nbsp;&nbsp; [IIF](#iif) &nbsp;&nbsp;&nbsp;&nbsp;[InStr](#instr) &nbsp;&nbsp;&nbsp;&nbsp; [IsNull](#isnull) &nbsp;&nbsp;&nbsp;&nbsp; [IsNullOrEmpty](#isnullorempty) &nbsp;&nbsp;&nbsp;&nbsp; [IsPresent](#ispresent) &nbsp;&nbsp;&nbsp;&nbsp; [IsString](#isstring) &nbsp;&nbsp;&nbsp;&nbsp; [Item](#item) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Left](#left) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp; [NormalizeDiacritics](#normalizediacritics) [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [RemoveDuplicates](#removeduplicates) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SelectUniqueValue](#selectuniquevalue)&nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [Split](#split)&nbsp;&nbsp;&nbsp;&nbsp;[StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)&nbsp;&nbsp;&nbsp;&nbsp; [ToLower](#tolower)&nbsp;&nbsp;&nbsp;&nbsp; [ToUpper](#toupper)&nbsp;&nbsp;&nbsp;&nbsp; [Word](#word)

---
### <a name="append"></a>附加
**函式：**<br> Append(source, suffix)

**說明：**<br> 取出 source 字串值並在結尾附加尾碼。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **來源** |必要 |String |通常為 source 物件的屬性名稱。 |
| **suffix** |必要 |String |您想要附加至 source 值結尾的字串。 |

---
### <a name="bitand"></a>BitAnd
**函式：**<br> BitAnd （value1，value2）

**說明：**<br> 此函式會將這兩個參數轉換為二進位表示法，並將某一個位元設為：

0-如果 value1 和 value2 中的一個或兩個對應位都是0                                                  
1 - 如果這兩個對應位元都是 1。                                    

換句話說，除非這兩個參數的對應位元都是 1，否則在所有情況下都會傳回 0。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **value1** |必要 |num |應該使用 value2 AND'ed 的數值|
| **value2** |必要 |num |應該使用 value1 AND'ed 的數值|

**範例︰**<br>
BitAnd(&HF, &HF7)                                                                                
11110111和 00000111 = 00000111，因此 BitAnd 會傳回7，其二進位值為00000111

---
### <a name="cbool"></a>CBool
**函式：**<br> CBool （運算式）

**說明：**<br> CBool 會根據評估的運算式傳回布林值（boolean）。 如果運算式評估為非零值，則 CBool 會傳回 True，否則會傳回 False。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **expression** |必要 | expression | 任何有效的運算式 |

**範例︰**<br>
CBool （[attribute1] = [attribute2]）                                                                    
如果這兩個屬性的值相同，即會傳回 True。

---
### <a name="coalesce"></a>Coalesce
**函式：**<br> 聯合（source1.rc，source2，...，defaultValue）

**說明：**<br> 傳回第一個非 Null 的來源值。 如果所有引數都是 Null，而 defaultValue 存在，則會傳回 defaultValue。 如果所有引數都是 Null，而 defaultValue 不存在，則聯合會傳回 Null。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **source1  … sourceN** | 必要 | String |必要，可變次數。 通常為 source 物件的屬性名稱。 |
| **defaultValue** | 選用 | String | 當所有來源值都是 Null 時，所要使用的預設值。 可以是空字串 ("")。

---
### <a name="converttobase64"></a>ConvertToBase64
**函式：**<br> ConvertToBase64 （來源）

**說明：**<br> ConvertToBase64 函式會將字串轉換為 Unicode Base64 字串。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **來源** |必要 |String |要轉換成基底64的字串|

**範例︰**<br>
ConvertToBase64("Hello world!")                                                                                                        
傳回 "SABlAGwAbABvACAAdwBvAHIAbABkACEA"

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**函式：**<br> ConvertToUTF8Hex （來源）

**說明：**<br> ConvertToUTF8Hex 函式會將字串轉換為 UTF8 十六進位編碼值。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **來源** |必要 |String |要轉換成 UTF8 十六進位的字串|

**範例︰**<br>
ConvertToUTF8Hex("Hello world!")                                                                                                         
傳回 48656C6C6F20776F726C6421

---
### <a name="count"></a>Count
**函式：**<br> Count （屬性）

**說明：**<br> Count 函式會傳回多重值屬性中的元素個數

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **特性** |必要 |屬性 |會計算元素的多重值屬性|

---
### <a name="cstr"></a>CStr
**函式：**<br> CStr （值）

**說明：**<br> CStr 函數會將值轉換成字串資料類型。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **value** |必要 | 數值、參考或布林值 | 可以是數值、參考屬性或布林值。 |

**範例︰**<br>
CStr([dn])                                                            
傳回 "cn = Joe，dc = contoso，dc = com"

---
### <a name="datefromnum"></a>DateFromNum
**函式：**<br> DateFromNum （值）

**說明：**<br> DateFromNum 函式會將 AD 日期格式的值轉換為 DateTime 類型。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **value** |必要 | Date | 要轉換成日期時間類型的 AD 日期 |

**範例︰**<br>
DateFromNum([lastLogonTimestamp])                                                                                                   
DateFromNum(129699324000000000)                                                            
傳回代表 2012-01-01 23:00:00 的 DateTime

---
### <a name="formatdatetime"></a>FormatDateTime
**函式：**<br> FormatDateTime(source, inputFormat, outputFormat)

**說明：**<br> 從一種格式取出日期字串，並將它轉換成不同的格式。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **來源** |必要 |String |通常為 source 物件的屬性名稱。 |
| **inputFormat** |必要 |String |source 值的預期格式。 如需支援的格式，請參閱[https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)。 |
| **outputFormat** |必要 |String |輸出日期的格式。 |

---
### <a name="guid"></a>Guid
**函式：**<br> Guid()

**說明：**<br> 函式 GUID 會產生新的隨機 GUID

---
### <a name="iif"></a>IIF
**函式：**<br> IIF （condition，valueIfTrue，valueIfFalse）

**說明：**<br> IIF 函式會根據指定的條件傳回其中一組可能值。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **條件** |必要 |變數或運算式 |可以評估為 true 或 false 的任何值或運算式。 |
| **valueIfTrue** |必要 |變數或字串 | 條件評估為 True 時所傳回的值。 |
| **valueIfFalse** |必要 |變數或字串 |條件評估為 False 時所傳回的值。|

**範例︰**<br>
IIF （[country] = "USA"，[country]，[部門]）

---
### <a name="instr"></a>InStr
**函式：**<br> InStr （value1，value2，start，compareType）

**說明：**<br> InStr 函式會在字串中尋找第一個出現的子字串

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **value1** |必要 |String |要搜尋的字串 |
| **value2** |必要 |String |要尋找的字串 |
| **start** |選用 |整數 |尋找子字串的開始位置|
| **compareType** |選用 |例舉 |可以是 vbTextCompare 或 vbBinaryCompare |

**範例︰**<br>
InStr("The quick brown fox","quick")                                                                             
評估為 5

InStr("repEated","e",3,vbBinaryCompare)                                                                                  
評估為 7

---
### <a name="isnull"></a>IsNull
**函式：**<br> IsNull （運算式）

**說明：**<br> 如果運算式評估為 Null，則 IsNull 函式會傳回 True。 針對屬性，Null 表示該屬性不存在。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **expression** |必要 |expression |要評估的運算式 |

**範例︰**<br>
IsNull([displayName])                                                                                                
如果屬性不存在，則傳回 True

---
### <a name="isnullorempty"></a>IsNullorEmpty
**函式：**<br> IsNullOrEmpty （運算式）

**說明：**<br> 如果運算式為 Null 或空字串，則 IsNullOrEmpty 函式會傳回 True。 針對屬性，如果屬性不存在，或存在但為空字串，即會評估為 True。
此函式的相反函式名稱為 IsPresent。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **expression** |必要 |expression |要評估的運算式 |

**範例︰**<br>
IsNullOrEmpty （[displayName]）                                               
如果屬性不存在或為空字串，則傳回 True

---
### <a name="ispresent"></a>IsPresent
**函式：**<br> IsPresent （運算式）

**說明：**<br> 如果運算式評估為非 Null 且不是空字串，則 IsPresent 函式會傳回 True。 這個函式的相反函式名稱為 IsNullOrEmpty。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **expression** |必要 |expression |要評估的運算式 |

**範例︰**<br>
Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])

---
### <a name="isstring"></a>IsString
**函式：**<br> IsString （運算式）

**說明：**<br> 如果運算式可評估為字串類型，則 IsString 函式會評估為 True。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **expression** |必要 |expression |要評估的運算式 |

---
### <a name="item"></a>Item
**函式：**<br> Item （屬性、索引）

**說明：**<br> Item 函式會從多重值字串/屬性傳回一個項目。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **特性** |必要 |屬性 |要搜尋的多重值屬性 |
| **index** |必要 |整數 | 多重值字串中的專案索引|

**範例︰**<br>
Item （[proxyAddresses]，1）

---
### <a name="join"></a>Join
**函式：**<br> Join(separator, source1, source2, …)

**說明：**<br> Join() 類似 Append()，不過前者可以將多個 **source** 字串值合併成單一字串，且每個值會以 **separator** 字串分隔。

如果其中一個 source 值是多重值屬性，則該屬性中的每個值會聯結在一起，並以分隔符號值分隔。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **separator** |必要 |String |用來分隔串連成一個字串的 source 值的字串。 如果不需要分隔符號，可以是 ""。 |
| **source1  … sourceN** |必要，變動次數 |String |要聯結在一起的字串值。 |

---
### <a name="left"></a>Left
**函式：**<br> Left （String，NumChars）

**說明：**<br> Left 函式會從字串左邊傳回指定的字元數。 如果 numChars = 0，會傳回空字串。
如果 numChars < 0，會傳回輸入字串。
如果 string 為 Null，會傳回空字串。
如果 string 包含的字元數比 numChars 中指定的數目少，即會傳回與 string 完全相同的字串 (也就是，包含參數 1 中的所有字元)。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **String** |必要 |屬性 | 要從中傳回字元的字串 |
| **NumChars** |必要 |整數 | 數位，識別要從字串的開頭（左邊）傳回的字元數。|

**範例︰**<br>
Left （"John Doe"，3）                                                            
傳回 "Joh"

---
### <a name="mid"></a>Mid
**函式：**<br> Mid(source, start, length)

**說明：**<br> 傳回 source 值的子字串。 子字串是只包含 source 字串某些字元的字串。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **來源** |必要 |String |通常為屬性的名稱。 |
| **start** |必要 |integer |子字串在 **source** 字串中起始位置的索引。 字串第一個字元的索引為 1，第二個字元的索引為 2，依此類推。 |
| **length** |必要 |integer |子字串的長度。 如果長度超出 **source** 字串結尾，函式會傳回從 **start** 索引一直到 **source** 字串結尾的子字串。 |

---
### <a name="normalizediacritics"></a>NormalizeDiacritics
**函式：**<br> NormalizeDiacritics(source)

**說明：**<br> 需要一個字串引數。 傳回字串，但是當中所有的變音符號字元皆被同等的非變音符號字元取代。 通常用於將包含變音符號字元 (重音符號) 的名字和姓氏，轉換成可用於如使用者主體名稱、SAM 帳戶名稱，與電子郵件地址等各種使用者識別碼中的有效值。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **來源** |必要 |String | 通常是名字或姓氏屬性。 |

---
### <a name="not"></a>Not
**函式：**<br> Not(source)

**說明：**<br> 翻轉 **source** 的布林值。 如果 **source** 值為 "*True*"，則傳回 "*False*"。 反之則傳回 "*True*"。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **來源** |必要 |Boolean String |預期的 **source** 值為 "True" 或 "False"。 |

---
### <a name="numfromdate"></a>NumFromDate
**函式：**<br> NumFromDate （值）

**說明：**<br> NumFromDate 函數會將日期時間值轉換成設定屬性（例如[accountExpires](https://docs.microsoft.com/windows/win32/adschema/a-accountexpires)）所需的 Active Directory 格式。 使用此函式，將從雲端 HR 應用程式（例如 Workday 和 SuccessFactors）接收的日期時間值轉換為其對等的廣告表示。 

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **value** |必要 | String | 支援格式的日期時間字串。 如需支援的格式，請參閱 https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx。 |

**範例︰**<br>
* Workday 範例 <br>
  假設您想要將從 Workday *ContractEndDate*的屬性對應至 AD 中的 [ *2020-12-31-08:00*到*accountExpires* ] 欄位，以下是您可以如何使用此函式，並將時區位移變更為符合您的地區設定。 
  `NumFromDate(Join("", FormatDateTime([ContractEndDate], "yyyy-MM-ddzzz", "yyyy-MM-dd"), "T23:59:59-08:00"))`

* SuccessFactors 範例 <br>
  假設您想要將來自 SuccessFactors 的屬性*結束*日期（格式為*M/d/yyyy hh： mm： ss tt* ）對應到 AD 中的*accountExpires*欄位，以下是您可以如何使用此函式並變更時區時差以符合您的地區設定。
  `NumFromDate(Join("",FormatDateTime([endDate],"M/d/yyyy hh:mm:ss tt","yyyy-MM-dd"),"T23:59:59-08:00"))`


---
### <a name="removeduplicates"></a>RemoveDuplicates
**函式：**<br> RemoveDuplicates （屬性）

**說明：**<br> RemoveDuplicates 函式會接受多重值的字串，並確定每個值都是唯一的。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **特性** |必要 |多重值屬性 |會移除重複專案的多重值屬性|

**範例︰**<br>
RemoveDuplicates([proxyAddresses])                                                                                                       
傳回已清理的 proxyAddress 屬性，其中已移除所有重複的值

---
### <a name="replace"></a>Replace
**函式：**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**說明：**<br>
取代字串內的值。 根據提供的參數而以不同的方式運作：

* 提供 **oldValue** 和 **replacementValue** 時：
  
  * 以 **replacementValue** 取代 **source** 中所有的 **oldValue** 項目
* 提供 **oldValue** 和 **template** 時：
  
  * 以 **source** 值取代 **template** 中所有的 **oldValue** 項目
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
| **來源** |必要 |String |通常為 **source** 物件的屬性名稱。 |
| **oldValue** |選用 |String |在 **source** 或 **template** 中要被取代的值。 |
| **regexPattern** |選用 |String |在 **source**中要被取代的值的規則運算式模式。 或者，如果使用了 **replacementPropertyName**，則為從 **replacementPropertyName** 擷取值的模式。 |
| **regexGroupName** |選用 |String |**regexPattern**內的群組名稱。 只有在使用了 **replacementPropertyName** 時，我們才會從 **replacementPropertyName** 擷取此群組的值作為 **replacementValue**。 |
| **replacementValue** |選用 |String |要取代舊值的新值。 |
| **replacementAttributeName** |選用 |String |要用於取代值的屬性名稱 |
| **template** |選用 |String |提供 **template** 值時，我們會尋找範本內的 **oldValue**，並將其取代為 **source** 值。 |

---
### <a name="selectuniquevalue"></a>SelectUniqueValue
**函式：**<br> SelectUniqueValue(uniqueValueRule1, uniqueValueRule2, uniqueValueRule3, …)

**說明：**<br> 至少需要兩個引數，也就是使用運算式定義的唯一值產生規則。 此函式會評估每個規則，接著檢查所產生的值在目標應用程式/目錄中的唯一性。 將會傳回所找到的第一個唯一值。 如果所有值都已存在於目標中，則項目會進行委付且原因會記錄於稽核記錄中。 可提供的引數數目沒有上限。

> [!NOTE]
> - 這是最上層函式，無法巢狀處理。
> - 此函式無法套用至具有相符優先順序的屬性。  
> - 此函式只能用於建立項目。 搭配屬性使用此函式時，請將 [套用對應] 屬性設定為 [僅限物件建立期間]。
> - 此函式目前僅支援「Workday 到 Active Directory 的使用者佈建」。 您無法將其與其他佈建應用程式搭配使用。 


**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **uniqueValueRule1  … uniqueValueRuleN** |至少需要 2 個，沒有上限 |String | 要評估的唯一值產生規則清單。 |


---
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**函式：**<br> SingleAppRoleAssignment([appRoleAssignments])

**說明：**<br> 從針對特定應用程式指派給使用者的所有 appRoleAssignment 清單中傳回單一 appRoleAssignment。 需有此函式才能將 appRoleAssignments 物件轉換成單一角色名稱字串。 請注意，最佳做法是確定一次只有一個 appRoleAssignment 會指派給一位使用者，如果多個角色受到指派，則傳回的角色字串可能不可預測。 

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |必要 |String |**[appRoleAssignments]** 物件。 |

---
### <a name="split"></a>Split
**函式：**<br> Split(source, delimiter)

**說明：**<br> 使用指定的分隔符號字元將字串分割成多重值陣列。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **來源** |必要 |String |**source** 值。 |
| **delimiter** |必要 |String |指定將用來分割字串的字元 (範例：",") |

---
### <a name="stripspaces"></a>StripSpaces
**函式：**<br> StripSpaces(source)

**說明：**<br> 移除 source 字串中的所有空格 (" ") 字元。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **來源** |必要 |String |**source** 值。 |

---
### <a name="switch"></a>Switch
**函式：**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**說明：**<br> 當 **source** 值符合某個 **key** 時，傳回該 **key** 的 **value**。 如果 **source** 值不符合任何 key，則傳回 **defaultValue**。  **key** 和 **value** 參數必須永遠成對出現。 函式必須要有偶數數目的參數。 函數不應用於參考屬性，例如管理員。 

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **來源** |必要 |String |**Source** 值。 |
| **defaultValue** |選用 |String |當 source 不符合任何 key 時要使用的預設值。 可以是空字串 ("")。 |
| **key** |必要 |String |要與 **source** 值比較的 **key**。 |
| **value** |必要 |String |符合 key 的 **source** 的取代值。 |

---
### <a name="tolower"></a>ToLower
**函式：**<br> ToLower(source, culture)

**說明：**<br> 採用 *source* 字串值，並使用所指定的文化特性 (Culture) 規則將其轉換成小寫。 如果未指定任何 *culture* 資訊，則會使用不因文化特性而異。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **來源** |必要 |String |通常為 source 物件的屬性名稱 |
| **culture** |選用 |String |根據 RFC 4646，文化特性 (Culture) 名稱的格式為 *languagecode2-country/regioncode2*，其中 *languagecode2* 是兩個字母的語言代碼，而 *country/regioncode2* 則是兩個字母的子文化特性代碼。 範例包括 ja-JP 代表日文 (日本)，en-US 代表英文 (美國)。 如果沒有兩個字母的語言代碼可供使用，則會使用衍生自 ISO 639-2 的三個字母代碼。|

---
### <a name="toupper"></a>ToUpper
**函式：**<br> ToUpper(source, culture)

**說明：**<br> 採用 *source* 字串值，並使用所指定的文化特性 (Culture) 規則將其轉換成大寫。 如果未指定任何 *culture* 資訊，則會使用不因文化特性而異。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **來源** |必要 |String |通常為 source 物件的屬性名稱。 |
| **culture** |選用 |String |根據 RFC 4646，文化特性 (Culture) 名稱的格式為 *languagecode2-country/regioncode2*，其中 *languagecode2* 是兩個字母的語言代碼，而 *country/regioncode2* 則是兩個字母的子文化特性代碼。 範例包括 ja-JP 代表日文 (日本)，en-US 代表英文 (美國)。 如果沒有兩個字母的語言代碼可供使用，則會使用衍生自 ISO 639-2 的三個字母代碼。|

---
### <a name="word"></a>Word
**函式：**<br> Word （String，WordNumber，分隔符號）

**說明：**<br> Word 函式會根據描述要使用之分隔符號及要傳回之字數的參數，傳回字串內含的單字。 string 內以 delimiters 其中一個字元來分隔之字元的每個字串，都會被識別為單字：

如果 number < 1，會傳回空字串。
如果 string 為 Null，會傳回空字串。
如果 string 所含的字數少於 number 個字，或者 string 不包含任何 delimeters 所識別的單字，就會傳回空字串。

**參數：**<br> 

| 名稱 | 必要 / 重複 | 類型 | 注意 |
| --- | --- | --- | --- |
| **String** |必要 |多重值屬性 |要從中傳回單字的字串。|
| **WordNumber** |必要 | 整數 | 識別應傳回的文字數目|
| **限定詞** |必要 |String| 字串，代表應該用來識別單字的分隔符號|

**範例︰**<br>
Word （"quick 棕色 fox"，3，""）                                                                                       
傳回 "brown"

Word （"This，string！ has & 多個分隔符號"，3，"，！ & #"）                                                                       
傳回 "has"

---

## <a name="examples"></a>範例
### <a name="strip-known-domain-name"></a>刪去已知的網域名稱
您必須從使用者的電子郵件刪去已知的網域名稱，得到使用者名稱。 <br>
例如，如果網域是 "contoso.com"，您可以使用下列運算式：

**運算式：** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**範例輸入/輸出：** <br>

* **輸入** (mail)："john.doe@contoso.com"
* **輸出**："john.doe"

### <a name="append-constant-suffix-to-user-name"></a>附加常數尾碼到使用者名稱
如果您使用 Salesforce 沙箱，您可能需要附加額外的尾碼到您所有的使用者名稱，才能進行同步處理。

**運算式：** <br>
`Append([userPrincipalName], ".test")`

**範例輸入/輸出：** <br>

* **輸入**：(userPrincipalName)："John.Doe@contoso.com"
* **輸出**："John.Doe@contoso.com.test"

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>串連部分名字和姓氏產生使用者別名
您必須取出使用者名字的前 3 個字母和使用者姓氏的前 5 個字母來產生使用者別名。

**運算式：** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**範例輸入/輸出：** <br>

* **輸入** (givenName)："John"
* **輸入** (surname)："Doe"
* **輸出**："JohDoe"

### <a name="remove-diacritics-from-a-string"></a>移除字串中的變音符號
您必須以不含重音符號的同等字元取代含重音符號的字元。

**運算式：** <br>
NormalizeDiacritics([givenName])

**範例輸入/輸出：** <br>

* **輸入** (givenName)： "Zoë"
* **輸出**：「Zoe」

### <a name="split-a-string-into-a-multi-valued-array"></a>將字串分割成多重值陣列
您必須採用以逗號分隔的字串清單，然後將其分割成可插入到多重值屬性 (例如 Salesforce 的 PermissionSets 屬性) 的陣列。 在此範例中，已在 Azure AD 的 extensionAttribute5 中填入權限集合清單。

**運算式：** <br>
Split([extensionAttribute5], ",")

**範例輸入/輸出：** <br>

* **輸入**（extensionAttribute5）： "PermissionSetOne，PermisionSetTwo"
* **輸出**：["PermissionSetOne", "PermissionSetTwo"]

### <a name="output-date-as-a-string-in-a-certain-format"></a>以特定格式將日期輸出為字串
您想要以特定格式傳送日期到 SaaS 應用程式。 <br>
例如，您要格式化 ServiceNow 的日期。

**運算式：** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**範例輸入/輸出：**

* **輸入** (extensionAttribute1)："20150123105347.1Z"
* **輸出**："2015-01-23"

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>根據預先定義的一組選項取代值

您必須根據儲存在 Azure AD 中的狀態碼定義使用者的時區。 <br>
如果狀態碼不符合任何預先定義的選項，則使用 "Australia/Sydney" 的預設值。

**運算式：** <br>
`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**範例輸入/輸出：**

* **輸入** (state)："QLD"
* **輸出**："Australia/Brisbane"

### <a name="replace-characters-using-a-regular-expression"></a>使用規則運算式來取代字元
您需要尋找符合規則運算式的字元，然後將它們移除。

**運算式：** <br>

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
* **輸出**："john.smith@contoso.com"

### <a name="generate-unique-value-for-userprincipalname-upn-attribute"></a>產生 userPrincipalName (UPN) 屬性的唯一值
根據使用者的名字、中間名和姓氏，您必須先產生 UPN 屬性的值並檢查其在目標 AD 目錄中的唯一性，再將此值指派給 UPN 屬性。

**運算式：** <br>

    SelectUniqueValue( 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  [PreferredFirstName], [PreferredLastName]))), "contoso.com"), 
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 1), [PreferredLastName]))), "contoso.com"),
        Join("@", NormalizeDiacritics(StripSpaces(Join(".",  Mid([PreferredFirstName], 1, 2), [PreferredLastName]))), "contoso.com")
    )

**範例輸入/輸出：**

* **輸入** (PreferredFirstName)："John"
* **輸入** (PreferredLastName)："Smith"
* **輸出**：如果 John.Smith@contoso.com 的 UPN 值尚未存在於目錄中，則為 "John.Smith@contoso.com"
* **輸出**：如果 J.Smith@contoso.com 的 UPN 值已存在於目錄中，則為 "John.Smith@contoso.com"
* **輸出**：如果以上兩個 UPN 值已存在於目錄中，則為 "Jo.Smith@contoso.com"

### <a name="flow-mail-value-if-not-null-otherwise-flow-userprincipalname"></a>如果不是 Null，則為 flow 郵件值，否則為流程 userPrincipalName
您想要傳送 mail 屬性（如果有的話）。 如果不是，您想要改為傳送 userPrincipalName 的值。

**運算式：** <br>
`Coalesce([mail],[userPrincipalName])`

**範例輸入/輸出：** <br>

* **輸入**（郵件）： Null
* **輸入**（userPrincipalName）： "John.Doe@contoso.com"
* **輸出**："John.Doe@contoso.com"

## <a name="related-articles"></a>相關文章
* [自動化 SaaS 應用程式使用者佈建/解除佈建](../app-provisioning/user-provisioning.md)
* [自訂使用者佈建的屬性對應](../app-provisioning/customize-application-attributes.md)
* [適用於使用者佈建的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
* [使用 SCIM 以啟用從 Azure Active Directory 到應用程式的使用者和群組自動佈建](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [帳戶佈建通知](../app-provisioning/user-provisioning.md)
* [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)
