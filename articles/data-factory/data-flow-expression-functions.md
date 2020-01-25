---
title: 對應資料流程中的運算式函數
description: 瞭解對應資料流程中的運算式函數。
author: kromerm
ms.author: makromer
manager: anandsub
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2019
ms.openlocfilehash: 68771ee3d2ae2d43245bd217bedcf59b987786f1
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716730"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>對應資料流程中的資料轉換運算式 

## <a name="expression-functions"></a>運算式函數

在 Data Factory 中，使用對應資料流程功能的運算式語言來設定資料轉換。
___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
數位的絕對值。
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算 * ``acos(1) -> 0.0``
___
### <code>add</code>的余弦反值 
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
新增一對字串或數字。 將日期新增至天數。 將持續時間新增至時間戳記。 對某個陣列附加類似類型的另一個陣列。 與 + 運算子相同 * ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>addDays</code><br/><br/>
在日期或時間戳記中增加天數。 與 date * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``
___
### <code>addMonths</code>的 + 運算子相同 
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
將月份新增至日期或時間戳記。 您可以選擇性地傳遞時區 * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
邏輯 AND 運算子。 與 & & * ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算 * ``asin(0) -> 0.0``
___
### <code>atan</code>的反正弦值 
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算 * ``atan(0) -> 0.0``
___
### <code>atan2</code>的反正切值 
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
傳回平面的正 X 軸與座標所指定的點之間的角度（以弧度為單位） * ``atan2(0, 0) -> 0.0``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
依名稱選取資料流程中的資料行值。 您可以傳遞選擇性的資料流程名稱作為第二個引數。 如果有多個相符專案，則會傳回第一個相符專案。 如果不符合，則會傳回 Null 值。 傳回的值必須是由其中一個類型轉換函數（TO_DATE，TO_STRING ...）轉換的類型。 在設計階段已知的資料行名稱，應該只依據其名稱來定址。 不支援計算輸入，但您可以使用參數替換 * ``toString(byName('parent'))``
* ``toLong(byName('income'))``
* ``toBoolean(byName('foster'))``
* ``toLong(byName($debtCol))``
* ``toString(byName('Bogus Column'))``
* ``toString(byName('Bogus Column', 'DeriveStream'))``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
依據資料流程中的相對位置（以1為基礎）來選取資料行值。 如果位置超出範圍，則會傳回 Null 值。 傳回的值必須是由其中一個類型轉換函式轉換的類型（TO_DATE，TO_STRING ...）不支援計算輸入，但您可以使用參數替換 * ``toString(byPosition(1))``
* ``toDecimal(byPosition(2), 10, 2)``
* ``toBoolean(byName(4))``
* ``toString(byName($colName))``
* ``toString(byPosition(1234))``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
根據替代條件套用一個值或另一個值。 如果輸入的數目為偶數，則會針對最後一項條件預設為 Null，* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算數位的 cube 根 * ``cbrt(8) -> 2.0``
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
傳回不小於 * ``ceil(-0.1) -> 0``
___
### <code>coalesce</code>數位的最小整數 
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
從一組輸入傳回第一個非 null 值。 所有輸入都應該是相同的類型 * ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
比較相同類型的兩個值。 如果 value1 < value2，則會傳回負整數，如果 value1 = = value2，則傳回 0; 如果 value1 > value2 * ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>，則會傳回正值 
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
將任意數目的字串串連在一起。 相當於 + 運算子加上字串 * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``
* ``isNull('sql' + null) -> true``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
以分隔符號將任意數目的字串串連在一起。 第一個參數是 * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``
___
### <code>contains</code>的分隔符號 
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
如果提供之陣列中的任何元素在所提供的述詞中評估為 true，則傳回 true。 Contains 需要述詞函式中某個元素的參考，如 #item * ``contains([1, 2, 3, 4], #item == 3) -> true``
* ``contains([1, 2, 3, 4], #item > 5) -> false``
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算 * ``cos(10) -> -0.8390715290764524``
___
### <code>cosh</code>的余弦值 
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算值的雙曲余弦函數 * ``cosh(0) -> 1.0``
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
以指定的位元長度為不同基本資料類型的一組資料行計算 CRC32 雜湊，值只能是 0(256)、224、256、384、512。 它可以用來計算資料列 * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``
___
### <code>currentDate</code>的指紋 
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
在此作業開始執行時取得目前的日期。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 本機時區會當做預設值使用。如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false``
* ``currentDate('PST')  == toDate('2250-12-31') -> false``
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
取得工作開始以當地時區 * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``
___
### <code>currentUTC</code>時的目前時間戳記 
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
取得目前的時間戳記（UTC）。 如果您想要將目前時間轉譯成與您的叢集時區不同的時區，您可以用 ' GMT '、' PST '、' UTC '、' 北美洲/開曼 ' 的格式傳遞選擇性的時區。 預設為目前的時區。 如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html)＞。 若要將 UTC 時間轉換成不同的時區，請使用 fromUTC （） * ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得給定日期 * ``dayOfMonth(toDate('2018-06-08')) -> 8``
___
### <code>dayOfWeek</code>的當月日期 
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得指定日期的星期日期。 1-星期日、2-星期一 ...、7-星期六 * ``dayOfWeek(toDate('2018-06-08')) -> 6``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得給定日期 * ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>days</code>的一年中的日期 
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
* ``days(2) -> 172800000L``
___
### <code>degrees</code>天數的持續時間（以毫秒為單位） 
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
將弧度轉換成度數 * ``degrees(3.141592653589793) -> 180``
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
將數字配對相除。 與/運算子 * ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
___
### <code>endsWith</code>相同 
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
檢查字串是否以提供的字串結尾 * ``endsWith('dumbo', 'mbo') -> true``
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「等於」比較運算子。 與 = = 運算子 * ``equals(12, 24) -> false``
* ``12 == 24 -> false``
* ``'bad' == 'bad' -> true``
* ``isNull('good' == toString(null)) -> true``
* ``isNull(null == null) -> true``
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
忽略大小寫的「等於」比較運算子。 與 < = > 運算子 * ``'abc'<=>'Abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
計算數位的階乘 * ``factorial(5) -> 120``
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
一律傳回 false 值。 如果有名為 ' false ' 的資料行 * ``(10 + 20 > 30) -> false``
* ``(10 + 20 > 30) -> false()``
___
### <code>filter</code>，請使用函數語法（false （）） 
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
篩選出不符合所提供述詞之陣列中的元素。 篩選準則預期述詞函式中某個元素的參考，如 #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
傳回不大於 * ``floor(-0.1) -> -1``
___
### <code>fromBase64</code>數目的最大整數 
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
在 base64 * ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``
___
### <code>fromUTC</code>中，將指定的字串編碼 
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
將轉換成 UTC 時間戳記。 您可以選擇性地以 ' GMT '、' PST '、' UTC '、' 北美洲/開曼 ' 的格式來傳遞時區。 其預設為目前 timezoneRefer JAVA 的 SimpleDateFormat，以取得可用的格式。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「大於」比較運算子。 與 > 運算子相同 * ``greater(12, 24) -> false``
* ``('dumbo' > 'dum') -> true``
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「大於或等於」比較運算子。 與 > = 運算子 * ``greaterOrEqual(12, 12) -> true``
* ``('dumbo' >= 'dum') -> true``
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
當輸入略過 null 值時，傳回值清單中的最大值。 如果所有輸入都是 null * ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(10, toInteger(null), 20) -> 20``
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>hasColumn</code>，則會傳回 null 
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
依名稱檢查資料流程中的資料行值。 您可以傳遞選擇性的資料流程名稱作為第二個引數。  在設計階段已知的資料行名稱，應該只依據其名稱來定址。 不支援計算的輸入，但是您可以 * ``hasColumn('parent')``
___
### <code>hour</code>使用參數替換 
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
取得時間戳記的小時值。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 本機時區會當做預設值使用。如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>

___
### <code>iif</code>* ``hours(2) -> 7200000L``小時數的持續時間（以毫秒為單位） 
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
根據條件套用一個值或另一個值。 如果未指定其他，則會將它視為 Null。 這兩個值都必須相容（數值、字串 ...） * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
檢查值是否不是 Null，並傳回其他傳回替代的。 它會測試所有輸入，直到找到第一個非 null 值 * ``iifNull(10, 20) -> 10``
* ``iifNull(null, 20, 40) -> 20``
* ``iifNull('bojjus', 'bo', 'dumbo') -> 'dumbo'``
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
檢查項目是否在陣列中 * ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
將每個單字的第一個字母轉換成大寫。 系統會將文字識別為以空白字元 * ``initCap('cool iceCREAM') -> 'Cool Icecream'``
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
尋找子字串在字串中的位置 (從 1 開始)。 如果找不到 * ``instr('dumbo', 'mbo') -> 3``
* ``instr('microsoft', 'o') -> 5``
* ``instr('good', 'bad') -> 0``
___
### <code>isDelete</code>，則會傳回 0 
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查資料列是否有刪除標示。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引應為1或2，而預設值為 1 * ``isDelete()``
* ``isDelete(1)``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查資料列是否標示為錯誤。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引應為1或2，而預設值為 1 * ``isError()``
* ``isError(1)``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查資料列是否標示為要忽略。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引應為1或2，而預設值為 1 * ``isIgnore()``
* ``isIgnore(1)``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查資料列是否有插入標示。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引應為1或2，而預設值為 1 * ``isInsert()``
* ``isInsert(1)``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查在查閱時是否比對資料列。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引應為1或2，而預設值為 1 * ``isMatch()``
* ``isMatch(1)``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
檢查值是否為 Null * ``isNull(NULL()) -> true``
* ``isNull('') -> false``
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查資料列是否有更新標示。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引應為1或2，而預設值為 1 * ``isUpdate()``
* ``isUpdate(1)``
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
檢查資料列是否有插入標示。 針對採用多個輸入資料流程的轉換，您可以傳遞資料流程的（以1為基的）索引。 資料流程索引應為1或2，而預設值為 1 * ``isUpsert()``
* ``isUpsert(1)``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
取得給定日期 * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``
___
### <code>least</code>的最後一個月日期 
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
「小於或等於」比較運算子。 與 < = 運算子 * ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
從索引 1 處開始擷取含特定字元數的子字串。 與 SUBSTRING （str，1，n） * ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
傳回字串的長度 * ``length('dumbo') -> 5``
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「小於」比較運算子。 與 < 運算子相同 * ``lesser(12, 24) -> true``
* ``('abcd' < 'abc') -> false``
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「小於或等於」比較運算子。 與 < = 運算子 * ``lesserOrEqual(12, 12) -> true``
* ``('dumbo' <= 'dum') -> false``
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
取得兩個字串之間的 levenshtein 距離 * ``levenshtein('boys', 'girls') -> 4``
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
模式是一種符合字面的字串。 例外狀況有下列特殊符號： _ 符合輸入中的任何一個字元（類似于）。 posix 規則運算式中的 .)。% 會比對出輸入中的零或多個字元 (類似於 posix 規則運算式中的 .*)。
逸出字元為 ''。 如果特殊符號或另一個逸出字元前面有逸出字元，就會逐字比對下列字元。 逸出任何其他字元的動作是無效的。
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
尋找子字串在字串中從特定位置起算的位置 (從 1 開始)。 如果省略位置，則視為字串的開頭。 如果找不到 * ``locate('mbo', 'dumbo') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
___
### <code>log</code>，則會傳回 0 
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
計算對數值。 如果使用 * ``log(100, 10) -> 2``
___
### <code>log10</code>，則可以提供選擇性的基底，否則為歐拉的數位 
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
根據10個基底 * ``log10(100) -> 2``
___
### <code>lower</code>計算記錄值 
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Lowercases 字串 * ``lower('GunChus') -> 'gunchus'``
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
從左側為字串填補提供的填補內容，直到達到特定長度。 如果字串等於或大於長度，則會將其修剪為長度 * ``lpad('dumbo', 10, '-') -> '-----dumbo'``
* ``lpad('dumbo', 4, '-') -> 'dumb'``
* ' ' lpad （' dumbo '，8，' < > '）-> ' < > ltrim<dumbo'``
___
### <code></code><br/><br/>
從左側修剪開頭字元的字串。 若未指定第二個參數，則會修剪空白字元。 否則，它會修剪第二個參數中指定的任何字元 * ``ltrim('  dumbo  ') -> 'dumbo  '``
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
使用提供的運算式，將陣列的每個元素對應至新的專案。 對應預期運算式函式中的一個元素參考為 #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
使用提供的運算式，將陣列的每個元素對應至新的專案。 對應預期運算式函式中的一個元素參考為 #item，而元素索引的參考則為 #index * ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
為不同基本資料類型的一組資料行計算 MD5 摘要，並傳回 32 個字元的十六進位字串。 它可以用來計算資料列 * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``
___
### <code>millisecond</code>的指紋 
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
取得日期的毫秒值。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 本機時區會當做預設值使用。如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>

___
### <code>minus</code>* ``milliseconds(2) -> 2L``毫秒數的持續時間（以毫秒為單位） 
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
減去數字。 從日期中減去天數。 從時間戳記減法的持續時間。 減法兩個時間戳記，以毫秒為單位來取得差異。 與-operator * ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
取得時間戳記的分鐘值。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 本機時區會當做預設值使用。如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>

___
### <code>mod</code>* ``minutes(2) -> 120000L``分鐘數的持續時間（以毫秒為單位） 
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
數字配對的模數。 與% operator * ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
___
### <code>month</code>相同 
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得日期或時間戳記的月份值 * ``month(toDate('2012-8-8')) -> 8``
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
取得兩個日期之間的月數。 您可以從計算中舍入。您可以用 ' GMT '、' PST '、' UTC '、' 北美洲/開曼 ' 的格式傳遞選擇性的時區。 本機時區會當做預設值使用。如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
將數字配對相乘。 與 * 運算子相同 * ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
將數字轉換為負數。 將正數變成負數，反之亦然 * ``negate(13) -> -13``
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
傳回下一個唯一序列。 此數位只會在資料分割內連續，而且前面會加上 partitionId * ``nextSequence() == 12313112 -> false``
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
將字串值正規化，以分隔 * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
___
### <code>not</code>的重音 unicode 字元 
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
邏輯否定運算子 * ``not(true) -> false``
* ``not(10 == 20) -> true``
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
「不等於」比較運算子。 與！ = 運算子 * ``12 != 24 -> true``
* ``'bojjus' != 'bo' + 'jjus' -> false``
___
### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
檢查值是否不是 Null * ``notNull(NULL()) -> false``
* ``notNull('') -> true``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
傳回 NULL 值。 如果有名為 'null' 的資料行，則使用函式 syntax(null())。 使用的任何作業都會導致 Null * ``isNull('dumbo' + null) -> true``
* ``isNull(10 * null) -> true``
* ``isNull('') -> false``
* ``isNull(10 + 20) -> false``
* ``isNull(10/0) -> true``
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
邏輯 OR 運算子。 與 | | 相同* ``or(true, false) -> true``
* ``true || false -> true``
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
數字配對的正模數。
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
傳回輸入資料列在 * ``partitionId()``
___
### <code>power</code>中的目前資料分割識別碼 
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>

___
### <code>reduce</code>另一個 * ``power(10, 2) -> 100``的功能引發一個數位 
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
累加陣列中的元素。 減少預期第一個運算式函式中的累加和一個元素的參考是 #acc 和 #item 而且它預期產生的值會當做第二個運算式函式中所使用的 #result * ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
擷取指定規則運算式模式的相符子字串。 最後一個參數會識別比對群組，如果省略則預設為 1。 使用 '<regex>' （後引號）來比對字串，而不將 * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>的轉義 
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
檢查字串是否符合指定的規則運算式模式。 使用 '<regex>' （後引號）來比對字串，而不將 * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>的轉義 
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
將所有出現的 RegEx 模式取代為指定字串中的另一個子字串使用 '<regex>' （反向引號）來比對字串，而不需將 * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>的轉義 
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
根據 RegEx 分割字串，並傳回字串陣列 * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
將子字串的所有出現專案取代為指定字串中的另一個子字串。 如果省略最後一個參數，則預設為空字串 * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie '``
* ``replace('doggie dog', 'dog') -> 'gie '``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
反轉字串 * ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
從右側擷取含特定字元數的子字串。 與子字串相同（str、長度（str）-n、n） * ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
檢查字串是否符合指定的 RegEx 模式 * ``rlike('200.50', `(\d+).(\d+)`) -> true``
* ``rlike('bogus', `M[0-9]+.*`) -> false``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
根據指定的選擇性比例和選擇性的進位模式來四捨五入數位。 如果省略小數值，則預設為0。  如果省略模式，則預設為 ROUND_HALF_UP （5）。 進位的值包括 1-ROUND_UP 2 ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
從右側為字串填補提供的填補內容，直到達到特定長度。 如果字串等於或大於長度，則會將其修剪為長度 * ``rpad('dumbo', 10, '-') -> 'dumbo-----'``
* ``rpad('dumbo', 4, '-') -> 'dumb'``
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``
___
### <code>rtrim</code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
從右側修剪開頭字元的字串。 若未指定第二個參數，則會修剪空白字元。 否則，它會修剪第二個參數中指定的任何字元 * ``rtrim('  dumbo  ') -> '  dumbo'``
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
取得日期的秒值。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 本機時區會當做預設值使用。如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>

___
### <code>sha1</code>* ``seconds(2) -> 2000L``秒數的持續時間（以毫秒為單位） 
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
為不同基本資料類型的一組資料行計算 SHA-1 摘要，並傳回 40 個字元的十六進位字串。 它可以用來計算資料列 * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``
___
### <code>sha2</code>的指紋 
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
根據指定的位長度，計算不同基本資料類型之一組資料行的 SHA-2 摘要，其值只能是0（256）、224、256、384、512。 它可以用來計算資料列 * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``
___
### <code>sin</code>的指紋 
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算 * ``sin(2) -> 0.9092974268256817``
___
### <code>sinh</code>的正弦值 
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算 * ``sinh(0) -> 0.0``
___
### <code>slice</code>的雙曲正弦值 
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
從某個位置擷取陣列的子集。 位置從 1 開始。 如果省略長度，則預設為字串的結尾 * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
使用提供的述詞函式來排序陣列。 排序需要運算式函式中兩個連續元素的參考做為 #item1，並 #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
取得字串 * ``soundex('genius') -> 'G520'``
___
### <code>split</code>的 soundex 代碼 
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
根據分隔符號分割字串，並傳回字串陣列 * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算數位的平方根 * ``sqrt(9) -> 3``
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
檢查字串是否以提供的字串 * ``startsWith('dumbo', 'du') -> true``
___
### <code>subDays</code>開頭 
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
從日期或時間戳記減去月份。 與 date * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``
___
### <code>subMonths</code>的-operator 相同 
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
從日期或時間戳記減去月份 * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
從某個位置擷取特定長度的子字串。 位置從 1 開始。 如果省略長度，則預設為字串的結尾，* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算 * ``tan(0) -> 0.0``
___
### <code>tanh</code>的正切值 
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
計算 * ``tanh(0) -> 0.0``
___
### <code>toBase64</code>的雙曲正切值 
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
在 base64 * ``toBase64('bojjus') -> 'Ym9qanVz'``
___
### <code>toBinary</code>中，將指定的字串編碼 
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
將任何數值/日期/時間戳記/字串轉換為二進位表示 * ``toBinary(3) -> [0x11]``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
將（' t '、' true '、' y '、' yes '、' 1 '）的值，轉換為 true，並將（' f '、' false '、' n '、' no '、' 0 '）轉換為 false，並將任何其他值設為 Null，* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``isNull(toBoolean('truthy')) -> true``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
使用選擇性的輸入日期格式，將輸入日期字串轉換為日期。 如需可用的格式，請參閱 JAVA 的 SimpleDateFormat。 如果省略輸入日期格式，預設格式為 yyyy-[M] M-[d] d。 接受的格式為： [yyyy，yyyy-[M] M，yyyy-[M] M-[d] d，yyyy-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')``
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
將任何數值或字串轉換為小數值。 若未指定有效位數和小數位數，則預設為 (10,2)。可使用選用的 Java 十進位格式進行轉換。 選擇性地區設定格式，採用 BCP47 語言，例如 en-us、de、zh-CN * ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
將任何數值或字串轉換為雙精準數值。 可使用選擇性的 Java 十進位格式進行轉換。 選擇性地區設定格式，採用 BCP47 語言，例如 en-us、de、zh-CN * ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
將任何數值或字串轉換為浮點值。 可使用選擇性的 Java 十進位格式進行轉換。 截斷任何 double * ``toFloat(123.45) -> 123.45f``
* ``toFloat('123.45') -> 123.45f``
* ``toFloat('$123.45', '$###.00') -> 123.45f``
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
將任何數值或字串轉換成整數值。 可使用選擇性的 Java 十進位格式進行轉換。 截斷任何 long、float、double * ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
將任何數值或字串轉換為長值。 可使用選擇性的 Java 十進位格式進行轉換。 截斷任何 float、double * ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
將任何數值或字串轉換為短值。 可使用選擇性的 Java 十進位格式進行轉換。 截斷任何整數、long、float、double * ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
將基本資料類型轉換為字串。 針對數字和日期，可以指定格式。 若未指定，則會選擇系統預設值。對於數字會使用 Java 十進位格式。 如需所有可能的日期格式，請參閱 JAVA SimpleDateFormat。預設格式為 yyyy-mm-dd * ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``
* ``toString(4 == 20) -> 'false'``
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
指定選擇性的時間戳記格式，將字串轉換為時間戳記。 如需所有可能的格式，請參閱 JAVA SimpleDateFormat。 如果省略時間戳記，則為預設模式。 yyyy-[M] M-[d] d hh： mm： ss [. f ...] 是使用。 您可以用 ' GMT '、' PST '、' UTC '、' 北美洲/開曼 ' 的格式傳遞選擇性的時區。時間戳記支援最多毫秒的精確度，其值為 999Refer JAVA 的 SimpleDateFormat，以取得可用的格式。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
將時間戳記轉換為 UTC。 您可以用 'GMT'、'PST'、'UTC'、'America/Cayman' 的格式傳遞選擇性的時區。 其預設為目前 timezoneRefer JAVA 的 SimpleDateFormat，以取得可用的格式。 https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
將字串中的一組字元取代為另一組字元。 字元有1到1個取代 * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
修剪開頭和結尾字元的字串。 若未指定第二個參數，則會修剪空白字元。 否則，它會修剪第二個參數中指定的任何字元 * ``trim('  dumbo  ') -> 'dumbo'``
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
一律傳回 true 值。 如果有名為 ' true ' 的資料行 * ``(10 + 20 == 30) -> true``
* ``(10 + 20 == 30) -> true()``
___
### <code>typeMatch</code>，請使用函數語法（true （）） 
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
比對資料行的類型。 只能在模式運算式中使用。數位符合 short、integer、long、double、float 或 decimal、integer 比對 short、整數、long、小數比對 double、float、decimal 和 datetime 符合日期或時間戳記類型 * ``typeMatch(type, 'number')``
* ``typeMatch('date', 'datetime')``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Uppercases 字串 * ``upper('bojjus') -> 'BOJJUS'``
___
### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
傳回產生的 UUID * ``uuid()``
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得給定日期 * ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>weeks</code>的年份周 
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>

___
### <code>xor</code>* ``weeks(2) -> 1209600000L``周數的持續時間（以毫秒為單位） 
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
邏輯 XOR 運算子。 與 ^ 運算子相同 * ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
取得日期 * ``year(toDate('2012-8-8')) -> 2012``的年份值 
## 彙總函式：下列函數僅適用于匯總、資料透視、unpivot 和視窗轉換 ___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
取得資料行 * ``avg(sales)``
___
### <code>avgIf</code>的平均值 
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根據條件取得資料行的平均值 * ``avgIf(region == 'West', sales)``
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
取得值的彙總計數。 如果指定了選擇性的資料行，它會忽略計數中的 Null 值 * ``count(custId)``
* ``count(custId, custName)``
* ``count()``
* ``count(iif(isNull(custId), 1, NULL))``
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
取得一組資料行的相異值匯總計數，* ``countDistinct(custId, custName)``
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
根據條件取得值的彙總計數。 如果指定了選擇性的資料行，它會忽略 * ``countIf(state == 'CA' && commission < 10000, name)``
___
### <code>covariancePopulation</code>計數中的 Null 值 
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
取得兩個數據行之間的人口共變數 * ``covariancePopulation(sales, profit)``
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
根據條件，取得兩個數據行的人口共變數 * ``covariancePopulationIf(region == 'West', sales)``
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
取得兩個數據行的樣本共變數 * ``covarianceSample(sales, profit)``
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
根據條件，取得兩個數據行的樣本共變數 * ``covarianceSampleIf(region == 'West', sales, profit)``
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
取得資料行群組的第一個值。 如果省略第二個參數 ignoreNulls，則會假設為 false * ``first(sales)``
* ``first(sales, false)``
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行 * ``kurtosis(sales)``
___
### <code>kurtosisIf</code>的峰度 
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件，取得資料行 * ``kurtosisIf(region == 'West', sales)``
___
### <code>last</code>的峰度 
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
取得資料行群組的最後一個值。 如果省略第二個參數 ignoreNulls，則會假設為 false * ``last(sales)``
* ``last(sales, false)``
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
取得資料行 * ``max(sales)``
___
### <code>maxIf</code>的最大值 
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
根據條件，取得資料行 * ``maxIf(region == 'West', sales)``
___
### <code>mean</code>的最大值 
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
取得資料行的平均值。 與 AVG * ``mean(sales)``
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根據條件取得資料行的平均值。 與 avgIf * ``meanIf(region == 'West', sales)``
___
### <code>min</code>相同 
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
取得資料行的最小值 * ``min(sales)``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
根據條件，取得資料行 * ``minIf(region == 'West', sales)``
___
### <code>skewness</code>的最小值 
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行 * ``skewness(sales)``
___
### <code>skewnessIf</code>的偏斜度 
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件，取得資料行的偏斜 * ``skewnessIf(region == 'West', sales)``
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行 * ``stdDev(sales)``
___
### <code>stddevIf</code>的標準差 
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件，取得資料行 * ``stddevIf(region == 'West', sales)``
___
### <code>stddevPopulation</code>的標準差 
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行 * ``stddevPopulation(sales)``
___
### <code>stddevPopulationIf</code>的人口標準差 
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件，取得資料行 * ``stddevPopulationIf(region == 'West', sales)``
___
### <code>stddevSample</code>的人口標準差 
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行 * ``stddevSample(sales)``
___
### <code>stddevSampleIf</code>的樣本標準差 
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件，取得資料行 * ``stddevSampleIf(region == 'West', sales)``
___
### <code>sum</code>的樣本標準差 
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
取得數值資料行的匯總總和 * ``sum(col)``
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
取得數值資料行之相異值的匯總總和，* ``sumDistinct(col)``
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根據條件取得數值資料行的彙總總和。 條件可以根據任何資料行 * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``
* ``sumDistinctIf(true, sales)``
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
根據條件取得數值資料行的彙總總和。 條件可以根據任何資料行 * ``sumIf(state == 'CA' && commission < 10000, sales)``
* ``sumIf(true, sales)``
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行 * ``variance(sales)``
___
### <code>varianceIf</code>的變異數 
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件，取得資料行 * ``varianceIf(region == 'West', sales)``
___
### <code>variancePopulation</code>的變異數 
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行 * ``variancePopulation(sales)``
___
### <code>variancePopulationIf</code>的擴展變異數 
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據條件，取得資料行 * ``variancePopulationIf(region == 'West', sales)``
___
### <code>varianceSample</code>的擴展變異數 
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
取得資料行 * ``varianceSample(sales)``
___
### <code>varianceSampleIf</code>的非偏誤變異數 
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
根據準則，取得資料行的非偏誤變異數 * ``varianceSampleIf(region == 'West', sales)``
## 視窗函式，下列函數僅適用于視窗轉換 ___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
CumeDist 函式會計算某個值與分割區中所有值的相對位置。 依照分割區的排序，將目前資料列 (含此列) 之前的列數，除以視窗分割區中的總列數，即為結果。 順序中的任何系結值都會評估為相同的位置。
* ``cumeDist()``
___
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
計算某值在值群組中的排名。 依照分割區的排序，將目前資料列 (含此列) 之前的列數加一，即為結果。 這些值不會在序列中產生間距。 即使資料未排序，還是會在 * ``denseRank(salesQtr, salesAmt)``
___
### <code>lag</code>的值中尋找變更，密集排名也可以 
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
取得第一個參數對目前資料列之前的 n 個資料列評估的值。 第二個參數是要查看的資料列數目，而預設值為1。 如果沒有多個資料列，除非指定了預設值 * ``lag(amount, 2)``
* ``lag(amount, 2000, 100)``
___
### <code>lead</code>，否則會傳回 null 的值 
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
取得第一個參數對目前資料列之後的 n 個資料列評估的值。 第二個參數是要向前查看的資料列數目，而預設值為1。 如果沒有多個資料列，除非指定了預設值 * ``lead(amount, 2)``
* ``lead(amount, 2000, 100)``
___
### <code>nTile</code>，否則會傳回 null 的值 
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
NTile 函式會將每個視窗分割區中的資料列分成從 1 到 `n` 的 `n` 個貯體。 貯體值的最大差異為 1。 如果分割區中的資料列數目無法平均分成貯體數，其餘值將會逐一均分給各個貯體，從第一個貯體開始。 NTile 函數適用于計算 tertiles、分量、十分位數和其他常見的摘要統計資料。 函式會在初始化期間計算兩個變數：一般值區的大小會新增一個額外的資料列。 這兩個變數都是以目前分割區的大小為基礎。 在計算過程中，此函式會追蹤目前的資料列數目、目前的貯體數目，和貯體將有所變更的資料列號碼 (bucketThreshold)。 當目前的資料列數目達到貯體閾值時，貯體值將會加一，而閾值會依貯體大小增加 (如果目前的貯體已填補，則額外再加一)。
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
計算某值在值群組中的排名。 依照分割區的排序，將目前資料列 (含此列) 之前的列數加一，即為結果。 這些值會在序列中產生間距。 即使資料未排序，還是會在 * ``rank(salesQtr, salesAmt)``
___
### <code>rowNumber</code>的值中尋找變更，順位運作 
<code><b>rowNumber() => integer</b></code><br/><br/>
為視窗中的資料列指派循序的資料列編號，從 1 開始 * ``rowNumber()``

## <a name="next-steps"></a>後續步驟

[瞭解如何使用運算式](concepts-data-flow-expression-builder.md)產生器。
