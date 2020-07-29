---
title: CRS 規則群組與規則
titleSuffix: Azure Web Application Firewall
description: 此頁面提供關於 Web 應用程式防火牆 CRS 規則群組與規則的資訊。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 37e09612491d41887c5945920488569d3620bf0b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85052011"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>Web 應用程式防火牆 CRS 規則群組與規則

應用程式閘道 web 應用程式防火牆 (WAF) 會保護 web 應用程式免於一般安全性弱點和攻擊。 這是透過根據 OWASP 核心規則集3.1、3.0 或2.2.9 所定義的規則來完成。 這些規則可以依規則逐一停用。 本文包含目前提供的規則和規則集。

## <a name="core-rule-sets"></a>核心規則集

應用程式閘道 WAF 預設為使用 CRS 3.0 預先設定。 但您可以選擇改為使用 CRS 3.1 或 CRS 2.2.9。 CRS 3.1 提供新的規則集，可防範 JAVA 感染、一組初始的檔案上傳檢查、固定的誤報等等。 相較于 CRS 2.2.9，CRS 3.0 提供了更少的誤報。 您也可以[自訂規則以符合您的需求](application-gateway-customize-waf-rules-portal.md)。

> [!div class="mx-imgBorder"]
> ![管理規則](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

WAF 可防範下列 web 弱點：

- SQL 插入式攻擊
- Cross site scripting attacks (跨網站指令碼攻擊)
- 其他常見的攻擊，例如命令插入、HTTP 要求走私、HTTP 回應分割和遠端檔案包含
- HTTP 通訊協定違規
- HTTP 通訊協定異常，例如遺失主機使用者代理程式和接受標頭
- Bot、編目程式及掃描程式攻擊
- 常見的應用程式錯誤配置（例如 Apache 和 IIS）

### <a name="owasp-crs-31"></a>OWASP CRS 3。1

CRS 3.1 包含13個規則群組，如下表所示。 每個群組都包含多個可停用的規則。

> [!NOTE]
> CRS 3.1 僅適用于 WAF_v2 SKU。

|規則群組|Description|
|---|---|
|**[一般](#general-31)**|一般群組|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|鎖定方法（PUT、PATCH）|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|防止埠和環境掃描器|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|防止通訊協定和編碼問題|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|防止標頭插入、要求走私和回應分割|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|防止檔案和路徑攻擊|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|防止遠端檔案包含（RFI）攻擊|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|再次保護遠端程式碼執行攻擊|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|防止 PHP 插入式攻擊|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|防止跨網站腳本攻擊|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|防止 SQL 插入式攻擊|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|防範會話 fixation 攻擊|
|**[要求-944-應用程式-攻擊-會話-JAVA](#crs944-31)**|防範 JAVA 攻擊|

### <a name="owasp-crs-30"></a>OWASP CRS 3。0

CRS 3.0 包含12個規則群組，如下表所示。 每個群組都包含多個可停用的規則。

|規則群組|Description|
|---|---|
|**[一般](#general-30)**|一般群組|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|鎖定方法（PUT、PATCH）|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|防止埠和環境掃描器|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|防止通訊協定和編碼問題|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|防止標頭插入、要求走私和回應分割|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|防止檔案和路徑攻擊|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|防止遠端檔案包含（RFI）攻擊|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|再次保護遠端程式碼執行攻擊|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|防止 PHP 插入式攻擊|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|防止跨網站腳本攻擊|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|防止 SQL 插入式攻擊|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|防範會話 fixation 攻擊|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2。9

CRS 2.2.9 包含10個規則群組，如下表所示。 每個群組都包含多個可停用的規則。

|規則群組|Description|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|防止通訊協定違規（例如不正確字元或具有要求主體的 GET）|
|**[crs_21_protocol_anomalies](#crs21)**|防止不正確的標頭資訊|
|**[crs_23_request_limits](#crs23)**|防止引數或超過限制的檔案|
|**[crs_30_http_policy](#crs30)**|防止受限制的方法、標頭和檔案類型|
|**[crs_35_bad_robots](#crs35)**|防止 web 爬網和掃描器|
|**[crs_40_generic_attacks](#crs40)**|防禦一般攻擊（例如會話 fixation、遠端檔案包含和 PHP 插入）|
|**[crs_41_sql_injection_attacks](#crs41sql)**|防止 SQL 插入式攻擊|
|**[crs_41_xss_attacks](#crs41xss)**|防止跨網站腳本攻擊|
|**[crs_42_tight_security](#crs42)**|防範路徑-遍歷攻擊|
|**[crs_45_trojans](#crs45)**|防止後門程式特洛伊程式|

在應用程式閘道上使用 Web 應用程式防火牆時，可以使用下列規則群組和規則。

# <a name="owasp-31"></a>[OWASP 3。1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a>規則集

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">一般</p>

|RuleId|Description|
|---|---|
|200004|可能的多組件不相符的界限。|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Description|
|---|---|
|911100|原則不允許方法|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Description|
|---|---|
|913100|找到與安全性掃描程式相關聯的使用者代理程式|
|913101|找到與指令碼/一般 HTTP 用戶端相關聯的使用者代理程式|
|913102|找到與 web 編目程式/bot 相關聯的使用者代理程式|
|913110|找到與安全性掃描程式相關聯的要求標頭|
|913120|找到與安全性掃描程式相關聯的要求檔名/引數|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Description|
|---|---|
|920100|無效的 HTTP 要求列|
|920120|嘗試使用多部分/表單資料略過|
|920121|嘗試使用多部分/表單資料略過|
|920130|無法剖析要求內文。|
|920140|多部分要求主體失敗嚴格驗證|
|920160|內容長度 HTTP 標頭不是數值。|
|920170|具有內文內容的 GET 或 HEAD 要求。|
|920171|使用傳輸編碼的 GET 或 HEAD 要求。|
|920180|POST 要求遺失 Content-Length 標頭。|
|920190|範圍 = 無效的最後一個位元組值。|
|920200|範圍 = 太多欄位 (6 個或以上)|
|920201|範圍 = 太多 pdf 要求的欄位 (35 個或以上)|
|920202|範圍 = 太多 pdf 要求的欄位 (6 個或以上)|
|920210|找到多個/衝突連接標頭資料。|
|920220|URL 編碼濫用攻擊嘗試|
|920230|偵測到多個 URL 編碼|
|920240|URL 編碼濫用攻擊嘗試|
|920250|UTF8 編碼濫用攻擊嘗試|
|920260|Unicode 全/半寬度濫用攻擊嘗試|
|920270|要求 (null 字元) 中的字元無效|
|920271|要求 (非可列印的字元) 中的字元無效|
|920272|要求 (在 ASCII 127 下方的可列印字元之外) 中的字元無效|
|920273|要求 (非常嚴格的設定之外) 中的字元無效|
|920274|要求標頭 (非常嚴格的設定之外) 中的字元無效|
|920280|要求遺失主機標頭|
|920290|空白的主機標頭|
|920300|要求遺失 Accept 標頭|
|920310|要求具有空白的 Accept 標頭|
|920311|要求具有空白的 Accept 標頭|
|920320|遺失使用者代理程式標頭|
|920330|空白的使用者代理程式標頭|
|920340|要求包含內容，但遺漏 Content-type 標頭|
|920341|包含內容的要求需要內容類型標頭|
|920350|主機標頭是數字的 IP 位址|
|920360|引數名稱太長|
|920370|引數值太長|
|920380|要求中的引數太多|
|920390|超過總引數大小|
|920400|上傳的檔案大小太大|
|920410|上傳的檔案大小總數太大|
|920420|原則不允許要求內容類型|
|920430|原則不允許 HTTP 通訊協定版本|
|920440|原則會限制 URL 的副檔名|
|920450|原則會限制 HTTP 標頭 (%@{MATCHED_VAR})|
|920460|異常的換用字元|
|920470|內容類型標頭不合法|
|920480|限制 content-type 標頭內的字元集參數|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Description|
|---|---|
|921110|HTTP 要求走私攻擊|
|921120|HTTP 回應分割攻擊|
|921130|HTTP 回應分割攻擊|
|921140|透過標頭的 HTTP 標頭插入式攻擊|
|921150|透過承載 (偵測到 CR/LF) 的 HTTP 標頭插入式攻擊|
|921151|透過承載 (偵測到 CR/LF) 的 HTTP 標頭插入式攻擊|
|921160|透過承載 (偵測到 CR/LF 和 header-name) 的 HTTP 標頭插入式攻擊|
|921170|HTTP 參數污染|
|921180|HTTP 參數污染（% {TX. 1}）|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Description|
|---|---|
|930100|路徑周遊攻擊 (/../)|
|930110|路徑周遊攻擊 (/../)|
|930120|作業系統檔案存取嘗試|
|930130|受限制檔案存取嘗試|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Description|
|---|---|
|931100|可能的遠端檔案包含 (RFI) 攻擊 = 使用 IP 位址的 URL 參數|
|931110|可能的遠端檔案包含 (RFI) 攻擊 = 與 URL 承載搭配使用的一般 RFI 易受攻擊參數名稱|
|931120|可能的遠端檔案包含 (RFI) 攻擊 = 與結尾問號字元 (?) 搭配使用 URL 承載|
|931130|可能的遠端檔案包含 (RFI) 攻擊 = 關閉網域參考/連結|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Description|
|---|---|
|932100|遠端命令執行： Unix 命令插入|
|932105|遠端命令執行： Unix 命令插入|
|932106|遠端命令執行： Unix 命令插入|
|932110|遠端命令執行： Windows 命令插入|
|932115|遠端命令執行： Windows 命令插入|
|932120|遠端命令執行 = 找到 Windows PowerShell 命令|
|932130|遠端命令執行 = 找到 Unix 殼層運算式|
|932140|遠端命令執行 = 找到 Windows FOR/IF 命令|
|932150|遠端命令執行：直接執行 Unix 命令|
|932160|遠端命令執行 = 找到 Unix 殼層程式碼|
|932170|執行遠端命令 = Shellshock (CVE-2014-6271)|
|932171|執行遠端命令 = Shellshock (CVE-2014-6271)|
|932180|限制的檔案上傳嘗試|
|932190|遠端命令執行：萬用字元略過技術嘗試|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Description|
|---|---|
|933100|PHP 插入式攻擊 = 找到開頭/結尾標記|
|933110|PHP 插入式攻擊 = 找到 PHP 指令碼檔案上傳|
|933111|PHP 插入式攻擊：找到 PHP 腳本檔案上傳|
|933120|PHP 插入式攻擊 = 找到組態指示詞|
|933130|PHP 插入式攻擊 = 找到變數|
|933131|PHP 插入式攻擊：找到變數|
|933140|PHP 插入式攻擊：找到 i/o 資料流程|
|933150|PHP 插入式攻擊 = 找到高風險 PHP 函式名稱|
|933151|PHP 插入式攻擊：找到中等風險的 PHP 函數名稱|
|933160|PHP 插入式攻擊 = 找到高風險 PHP 函式呼叫|
|933161|PHP 插入式攻擊：找到低價值 PHP 函式呼叫|
|933170|PHP 插入式攻擊：序列化的物件插入|
|933180|PHP 插入式攻擊 = 找到變數函式呼叫|
|933190|PHP 插入式攻擊：找到 PHP 結束記號|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Description|
|---|---|
|941100|透過 libinjection 偵測到的 XSS 攻擊|
|941101|透過 libinjection 偵測到的 XSS 攻擊|
|941110|XSS 篩選器 - 類別 1 = 指令碼標記向量|
|941130|XSS 篩選器 - 類別 3 = 屬性向量|
|941140|XSS 篩選器 - 類別 4 = JavaScript URI 向量|
|941150|XSS 篩選器 - 類別 5 = 不允許的 HTML 屬性|
|941160|NoScript XSS InjectionChecker： HTML 插入|
|941170|NoScript XSS InjectionChecker：屬性插入|
|941180|節點狀態驗證封鎖清單關鍵字|
|941190|使用樣式表單的 XSS|
|941200|使用 VML 框架的 XSS|
|941210|使用模糊 JAVAscript 的 XSS|
|941220|使用模糊 VB 腳本的 XSS|
|941230|使用 ' embed ' 標記的 XSS|
|941240|使用 [匯入] 或 [執行中] 屬性的 XSS|
|941250|IE XSS 篩選器-偵測到攻擊|
|941260|使用 ' meta ' 標記的 XSS|
|941270|使用「連結」 href 的 XSS|
|941280|使用 ' base ' 標記的 XSS|
|941290|使用 ' applet ' 標記的 XSS|
|941300|使用 ' object ' 標記的 XSS|
|941310|US-ASCII 格式不正確編碼 XSS 篩選器 - 偵測到攻擊。|
|941320|偵測到可能的 XSS 攻擊 - HTML 標記處理常式|
|941330|IE XSS 篩選器 - 偵測到攻擊。|
|941340|IE XSS 篩選器 - 偵測到攻擊。|
|941350|UTF-7 編碼 IE XSS - 偵測到攻擊。|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Description|
|---|---|
|942100|透過 libinjection 偵測到的 SQL 插入式攻擊|
|942110|SQL 插入式攻擊：偵測到常見的插入式測試|
|942120|SQL 插入式攻擊：偵測到 SQL 運算子|
|942130|SQL 插入式攻擊：偵測到 SQL Tautology。|
|942140|SQL 插入式攻擊 = 偵測到常用的 DB 名稱|
|942150|SQL 插入式攻擊|
|942160|偵測到使用 sleep() 或 benchmark() 的盲人 sqli 測試。|
|942170|偵測到 SQL 基準測試與睡眠插入式嘗試包括條件式查詢|
|942180|偵測到基本 SQL 驗證略過嘗試1/3|
|942190|偵測到 MSSQL 程式碼執行和資訊收集嘗試次數|
|942200|偵測到 MySQL 註解/空間模糊化的插入和反引號終止|
|942210|偵測連鎖的 SQL 插入嘗試1/2|
|942220|尋找整數溢位攻擊，這些是從 skipfish 取得，但3.0.00738585072 除外|
|942230|偵測到條件式 SQL 插入嘗試|
|942240|偵測 MySQL 字元集交換器和 MSSQL DoS 嘗試|
|942250|偵測符合、合併和執行立即插入|
|942251|偵測到 HAVING 插入|
|942260|偵測到基本 SQL 驗證略過嘗試 2/3|
|942270|尋找基本的 SQL 插入。 Mysql oracle 和其他人的常見攻擊字串|
|942280|偵測 Postgres pg_sleep 插入、waitfor 延遲攻擊和資料庫關機嘗試|
|942290|尋找基本 MongoDB SQL 插入嘗試|
|942300|偵測到 MySQL 註解、條件和 ch(a)r 插入|
|942310|偵測連鎖的 SQL 插入嘗試2/2|
|942320|偵測到 MySQL 與 PostgreSQL 預存程序/函式插入|
|942330|偵測到傳統 SQL 插入探測 1/2|
|942340|偵測到基本 SQL 驗證略過嘗試 3/3|
|942350|偵測到 MySQL UDF 插入和其他資料/結構操作嘗試|
|942360|偵測到連續的基本 SQL 插入和 SQLLFI 嘗試次數|
|942361|根據關鍵字 alter 或 union 偵測基本 SQL 插入式|
|942370|偵測到傳統 SQL 插入探測 2/2|
|942380|SQL 插入式攻擊|
|942390|SQL 插入式攻擊|
|942400|SQL 插入式攻擊|
|942410|SQL 插入式攻擊|
|942420|受限制的 SQL 字元異常偵測（cookie）：已超過的特殊字元數（8）|
|942421|受限制的 SQL 字元異常偵測（cookie）：已超過的特殊字元數（3）|
|942430|受限制的 SQL 字元異常偵測 (args)：超出的特殊字元數目 (12)|
|942431|受限制的 SQL 字元異常偵測（args）：已超過的特殊字元數（6）|
|942432|受限制的 SQL 字元異常偵測（args）：已超過的特殊字元數（2）|
|942440|偵測到 SQL 註解順序。|
|942450|識別到 SQL 十六進位編碼|
|942460|中繼字元異常偵測警示 - 重複性的非文字字元|
|942470|SQL 插入式攻擊|
|942480|SQL 插入式攻擊|
|942490|偵測傳統 SQL 插入探測3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Description|
|---|---|
|943100|可能的工作階段 Fixation 攻擊 = 在 HTML 中設定 Cookie 值|
|943110|可能的工作階段 Fixation 攻擊 = 具有關閉網域查閱者的工作階段識別碼參數名稱|
|943120|可能的工作階段 Fixation 攻擊 = 沒有查閱者的工作階段識別碼參數名稱|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">要求-944-應用程式-攻擊-會話-JAVA</p>

|RuleId|Description|
|---|---|
|944120|可能的承載執行和遠端命令執行|
|944130|可疑的 JAVA 類別|
|944200|利用 JAVA 還原序列化 Apache Commons|

# <a name="owasp-30"></a>[OWASP 3.0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a>規則集

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">一般</p>

|RuleId|Description|
|---|---|
|200004|可能的多組件不相符的界限。|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Description|
|---|---|
|911100|原則不允許方法|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Description|
|---|---|
|913100|找到與安全性掃描程式相關聯的使用者代理程式|
|913110|找到與安全性掃描程式相關聯的要求標頭|
|913120|找到與安全性掃描程式相關聯的要求檔名/引數|
|913101|找到與指令碼/一般 HTTP 用戶端相關聯的使用者代理程式|
|913102|找到與 web 編目程式/bot 相關聯的使用者代理程式|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Description|
|---|---|
|920100|無效的 HTTP 要求列|
|920130|無法剖析要求內文。|
|920140|多部分要求主體失敗嚴格驗證|
|920160|內容長度 HTTP 標頭不是數值。|
|920170|具有內文內容的 GET 或 HEAD 要求。|
|920180|POST 要求遺失 Content-Length 標頭。|
|920190|範圍 = 無效的最後一個位元組值。|
|920210|找到多個/衝突連接標頭資料。|
|920220|URL 編碼濫用攻擊嘗試|
|920240|URL 編碼濫用攻擊嘗試|
|920250|UTF8 編碼濫用攻擊嘗試|
|920260|Unicode 全/半寬度濫用攻擊嘗試|
|920270|要求 (null 字元) 中的字元無效|
|920280|要求遺失主機標頭|
|920290|空白的主機標頭|
|920310|要求具有空白的 Accept 標頭|
|920311|要求具有空白的 Accept 標頭|
|920330|空白的使用者代理程式標頭|
|920340|要求包含內容，但遺漏 Content-type 標頭|
|920350|主機標頭是數字的 IP 位址|
|920380|要求中的引數太多|
|920360|引數名稱太長|
|920370|引數值太長|
|920390|超過總引數大小|
|920400|上傳的檔案大小太大|
|920410|上傳的檔案大小總數太大|
|920420|原則不允許要求內容類型|
|920430|原則不允許 HTTP 通訊協定版本|
|920440|原則會限制 URL 的副檔名|
|920450|原則會限制 HTTP 標頭 (%@{MATCHED_VAR})|
|920200|範圍 = 太多欄位 (6 個或以上)|
|920201|範圍 = 太多 pdf 要求的欄位 (35 個或以上)|
|920230|偵測到多個 URL 編碼|
|920300|要求遺失 Accept 標頭|
|920271|要求 (非可列印的字元) 中的字元無效|
|920320|遺失使用者代理程式標頭|
|920272|要求 (在 ASCII 127 下方的可列印字元之外) 中的字元無效|
|920202|範圍 = 太多 pdf 要求的欄位 (6 個或以上)|
|920273|要求 (非常嚴格的設定之外) 中的字元無效|
|920274|要求標頭 (非常嚴格的設定之外) 中的字元無效|
|920460|異常的換用字元|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Description|
|---|---|
|921100|HTTP 要求走私攻擊。|
|921110|HTTP 要求走私攻擊|
|921120|HTTP 回應分割攻擊|
|921130|HTTP 回應分割攻擊|
|921140|透過標頭的 HTTP 標頭插入式攻擊|
|921150|透過承載 (偵測到 CR/LF) 的 HTTP 標頭插入式攻擊|
|921160|透過承載 (偵測到 CR/LF 和 header-name) 的 HTTP 標頭插入式攻擊|
|921151|透過承載 (偵測到 CR/LF) 的 HTTP 標頭插入式攻擊|
|921170|HTTP 參數污染|
|921180|HTTP 參數侵害 (%@{TX.1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Description|
|---|---|
|930100|路徑周遊攻擊 (/../)|
|930110|路徑周遊攻擊 (/../)|
|930120|作業系統檔案存取嘗試|
|930130|受限制檔案存取嘗試|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Description|
|---|---|
|931100|可能的遠端檔案包含 (RFI) 攻擊 = 使用 IP 位址的 URL 參數|
|931110|可能的遠端檔案包含 (RFI) 攻擊 = 與 URL 承載搭配使用的一般 RFI 易受攻擊參數名稱|
|931120|可能的遠端檔案包含 (RFI) 攻擊 = 與結尾問號字元 (?) 搭配使用 URL 承載|
|931130|可能的遠端檔案包含 (RFI) 攻擊 = 關閉網域參考/連結|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Description|
|---|---|
|932120|遠端命令執行 = 找到 Windows PowerShell 命令|
|932130|遠端命令執行 = 找到 Unix 殼層運算式|
|932140|遠端命令執行 = 找到 Windows FOR/IF 命令|
|932160|遠端命令執行 = 找到 Unix 殼層程式碼|
|932170|執行遠端命令 = Shellshock (CVE-2014-6271)|
|932171|執行遠端命令 = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Description|
|---|---|
|933100|PHP 插入式攻擊 = 找到開頭/結尾標記|
|933110|PHP 插入式攻擊 = 找到 PHP 指令碼檔案上傳|
|933120|PHP 插入式攻擊 = 找到組態指示詞|
|933130|PHP 插入式攻擊 = 找到變數|
|933150|PHP 插入式攻擊 = 找到高風險 PHP 函式名稱|
|933160|PHP 插入式攻擊 = 找到高風險 PHP 函式呼叫|
|933180|PHP 插入式攻擊 = 找到變數函式呼叫|
|933151|PHP 插入式攻擊 = 找到中度風險 PHP 函式名稱|
|933131|PHP 插入式攻擊 = 找到變數|
|933161|PHP 插入式攻擊 = 找到低價值 PHP 函式呼叫|
|933111|PHP 插入式攻擊 = 找到 PHP 指令碼檔案上傳|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Description|
|---|---|
|941100|透過 libinjection 偵測到的 XSS 攻擊|
|941110|XSS 篩選器 - 類別 1 = 指令碼標記向量|
|941130|XSS 篩選器 - 類別 3 = 屬性向量|
|941140|XSS 篩選器 - 類別 4 = JavaScript URI 向量|
|941150|XSS 篩選器 - 類別 5 = 不允許的 HTML 屬性|
|941180|節點狀態驗證封鎖清單關鍵字|
|941190|使用樣式表單的 XSS|
|941200|使用 VML 框架的 XSS|
|941210|使用模糊 JAVAscript 的 XSS|
|941220|使用模糊 VB 腳本的 XSS|
|941230|使用 ' embed ' 標記的 XSS|
|941240|使用 [匯入] 或 [執行中] 屬性的 XSS|
|941260|使用 ' meta ' 標記的 XSS|
|941270|使用「連結」 href 的 XSS|
|941280|使用 ' base ' 標記的 XSS|
|941290|使用 ' applet ' 標記的 XSS|
|941300|使用 ' object ' 標記的 XSS|
|941310|US-ASCII 格式不正確編碼 XSS 篩選器 - 偵測到攻擊。|
|941330|IE XSS 篩選器 - 偵測到攻擊。|
|941340|IE XSS 篩選器 - 偵測到攻擊。|
|941350|UTF-7 編碼 IE XSS - 偵測到攻擊。|
|941320|偵測到可能的 XSS 攻擊 - HTML 標記處理常式|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Description|
|---|---|
|942100|透過 libinjection 偵測到的 SQL 插入式攻擊|
|942110|SQL 插入式攻擊：偵測到常見的插入式測試|
|942130|SQL 插入式攻擊：偵測到 SQL Tautology。|
|942140|SQL 插入式攻擊 = 偵測到常用的 DB 名稱|
|942160|偵測到使用 sleep() 或 benchmark() 的盲人 sqli 測試。|
|942170|偵測到 SQL 基準測試與睡眠插入式嘗試包括條件式查詢|
|942190|偵測到 MSSQL 程式碼執行和資訊收集嘗試次數|
|942200|偵測到 MySQL 註解/空間模糊化的插入和反引號終止|
|942230|偵測到條件式 SQL 插入嘗試|
|942260|偵測到基本 SQL 驗證略過嘗試 2/3|
|942270|尋找基本的 SQL 插入。 MySQL Oracle 和其他的常見攻擊字串。|
|942290|尋找基本 MongoDB SQL 插入嘗試|
|942300|偵測到 MySQL 註解、條件和 ch(a)r 插入|
|942310|偵測連鎖的 SQL 插入嘗試2/2|
|942320|偵測到 MySQL 與 PostgreSQL 預存程序/函式插入|
|942330|偵測到傳統 SQL 插入探測 1/2|
|942340|偵測到基本 SQL 驗證略過嘗試 3/3|
|942350|偵測到 MySQL UDF 插入和其他資料/結構操作嘗試|
|942360|偵測到連續的基本 SQL 插入和 SQLLFI 嘗試次數|
|942370|偵測到傳統 SQL 插入探測 2/2|
|942150|SQL 插入式攻擊|
|942410|SQL 插入式攻擊|
|942430|受限制的 SQL 字元異常偵測 (args)：超出的特殊字元數目 (12)|
|942440|偵測到 SQL 註解順序。|
|942450|識別到 SQL 十六進位編碼|
|942251|偵測到 HAVING 插入|
|942460|中繼字元異常偵測警示 - 重複性的非文字字元|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Description|
|---|---|
|943100|可能的工作階段 Fixation 攻擊 = 在 HTML 中設定 Cookie 值|
|943110|可能的工作階段 Fixation 攻擊 = 具有關閉網域查閱者的工作階段識別碼參數名稱|
|943120|可能的工作階段 Fixation 攻擊 = 沒有查閱者的工作階段識別碼參數名稱|

# <a name="owasp-229"></a>[OWASP 2.2。9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a>規則集

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a> crs_20_protocol_violations

|RuleId|Description|
|---|---|
|960911|無效的 HTTP 要求列|
|981227|Apache 錯誤 = 要求中無效的 URI。|
|960912|無法剖析要求內文。|
|960914|多部分要求主體失敗嚴格驗證|
|960915|多部分剖析偵測到可能的不相符界限。|
|960016|內容長度 HTTP 標頭不是數值。|
|960011|具有內文內容的 GET 或 HEAD 要求。|
|960012|POST 要求遺失 Content-Length 標頭。|
|960902|使用無效的身分識別編碼。|
|960022|HTTP 1.0 不允許預期標頭。|
|960020|Pragma 標頭需要 HTTP/1.1 要求的快取控制標頭。|
|958291|範圍 = 欄位存在且是以 0 開頭。|
|958230|範圍 = 無效的最後一個位元組值。|
|958295|找到多個/衝突連接標頭資料。|
|950107|URL 編碼濫用攻擊嘗試|
|950109|偵測到多個 URL 編碼|
|950108|URL 編碼濫用攻擊嘗試|
|950801|UTF8 編碼濫用攻擊嘗試|
|950116|Unicode 全/半寬度濫用攻擊嘗試|
|960901|要求中的字元無效|
|960018|要求中的字元無效|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a>crs_21_protocol_anomalies

|RuleId|Description|
|---|---|
|960008|要求遺失主機標頭|
|960007|空白的主機標頭|
|960015|要求遺失 Accept 標頭|
|960021|要求具有空白的 Accept 標頭|
|960009|要求遺漏使用者代理程式標頭|
|960006|空白的使用者代理程式標頭|
|960904|要求包含內容，但遺漏 Content-type 標頭|
|960017|主機標頭是數字的 IP 位址|

### <a name="crs_23_request_limits"></a><a name="crs23"></a>crs_23_request_limits

|RuleId|Description|
|---|---|
|960209|引數名稱太長|
|960208|引數值太長|
|960335|要求中的引數太多|
|960341|超過總引數大小|
|960342|上傳的檔案大小太大|
|960343|上傳的檔案大小總數太大|

### <a name="crs_30_http_policy"></a><a name="crs30"></a>crs_30_HTTP_policy

|RuleId|Description|
|---|---|
|960032|原則不允許方法|
|960010|原則不允許要求內容類型|
|960034|原則不允許 HTTP 通訊協定版本|
|960035|原則會限制 URL 的副檔名|
|960038|原則會限制 HTTP 標頭|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a>crs_35_bad_robots

|RuleId|Description|
|---|---|
|990002|要求會指出安全性掃描程式掃描的網站|
|990901|要求會指出安全性掃描程式掃描的網站|
|990902|要求會指出安全性掃描程式掃描的網站|
|990012|惡意網站編目程式|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a> crs_40_generic_attacks

|RuleId|Description|
|---|---|
|960024|中繼字元異常偵測警示 - 重複性的非文字字元|
|950008|插入未記載的 ColdFusion 標記|
|950010|LDAP 插入式攻擊|
|950011|SSI 插入式攻擊|
|950018|偵測到通用 PDF XSS URL。|
|950019|電子郵件插入式攻擊|
|950012|HTTP 要求走私攻擊。|
|950910|HTTP 回應分割攻擊|
|950911|HTTP 回應分割攻擊|
|950117|遠端檔案包含攻擊|
|950118|遠端檔案包含攻擊|
|950119|遠端檔案包含攻擊|
|950120|可能的遠端檔案包含 (RFI) 攻擊 = 關閉網域參考/連結|
|981133|規則 981133|
|981134|規則 981134|
|950009|工作階段 Fixation 攻擊|
|950003|工作階段 Fixation|
|950000|工作階段 Fixation|
|950005|遠端檔案存取嘗試|
|950002|系統命令存取|
|950006|系統命令插入|
|959151|PHP 插入式攻擊|
|958976|PHP 插入式攻擊|
|958977|PHP 插入式攻擊|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Description|
|---|---|
|981231|偵測到 SQL 註解順序。|
|981260|識別到 SQL 十六進位編碼|
|981320|SQL 插入式攻擊 = 偵測到常用的 DB 名稱|
|981300|規則 981300|
|981301|規則 981301|
|981302|規則 981302|
|981303|規則 981303|
|981304|規則 981304|
|981305|規則 981305|
|981306|規則 981306|
|981307|規則 981307|
|981308|規則 981308|
|981309|規則 981309|
|981310|規則 981310|
|981311|規則 981311|
|981312|規則 981312|
|981313|規則 981313|
|981314|規則 981314|
|981315|規則 981315|
|981316|規則 981316|
|981317|SQL SELECT 陳述式的異常偵測警示|
|950007|密件 SQL 插入式攻擊|
|950001|SQL 插入式攻擊|
|950908|SQL 插入式攻擊。|
|959073|SQL 插入式攻擊|
|981272|偵測到使用 sleep() 或 benchmark() 的盲人 sqli 測試。|
|981250|偵測到 SQL 基準測試與睡眠插入式嘗試包括條件式查詢|
|981241|偵測到條件式 SQL 插入嘗試|
|981276|尋找基本的 SQL 插入。 MySQL Oracle 和其他的常見攻擊字串。|
|981270|尋找基本 MongoDB SQL 插入嘗試|
|981253|偵測到 MySQL 與 PostgreSQL 預存程序/函式插入|
|981251|偵測到 MySQL UDF 插入和其他資料/結構操作嘗試|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a>crs_41_xss_attacks

|RuleId|Description|
|---|---|
|973336|XSS 篩選器 - 類別 1 = 指令碼標記向量|
|973338|XSS 篩選器 - 類別 3 = JavaScript URI 向量|
|981136|規則 981136|
|981018|規則 981018|
|958016|跨網站指令碼 (XSS) 攻擊|
|958414|跨網站指令碼 (XSS) 攻擊|
|958032|跨網站指令碼 (XSS) 攻擊|
|958026|跨網站指令碼 (XSS) 攻擊|
|958027|跨網站指令碼 (XSS) 攻擊|
|958054|跨網站指令碼 (XSS) 攻擊|
|958418|跨網站指令碼 (XSS) 攻擊|
|958034|跨網站指令碼 (XSS) 攻擊|
|958019|跨網站指令碼 (XSS) 攻擊|
|958013|跨網站指令碼 (XSS) 攻擊|
|958408|跨網站指令碼 (XSS) 攻擊|
|958012|跨網站指令碼 (XSS) 攻擊|
|958423|跨網站指令碼 (XSS) 攻擊|
|958002|跨網站指令碼 (XSS) 攻擊|
|958017|跨網站指令碼 (XSS) 攻擊|
|958007|跨網站指令碼 (XSS) 攻擊|
|958047|跨網站指令碼 (XSS) 攻擊|
|958410|跨網站指令碼 (XSS) 攻擊|
|958415|跨網站指令碼 (XSS) 攻擊|
|958022|跨網站指令碼 (XSS) 攻擊|
|958405|跨網站指令碼 (XSS) 攻擊|
|958419|跨網站指令碼 (XSS) 攻擊|
|958028|跨網站指令碼 (XSS) 攻擊|
|958057|跨網站指令碼 (XSS) 攻擊|
|958031|跨網站指令碼 (XSS) 攻擊|
|958006|跨網站指令碼 (XSS) 攻擊|
|958033|跨網站指令碼 (XSS) 攻擊|
|958038|跨網站指令碼 (XSS) 攻擊|
|958409|跨網站指令碼 (XSS) 攻擊|
|958001|跨網站指令碼 (XSS) 攻擊|
|958005|跨網站指令碼 (XSS) 攻擊|
|958404|跨網站指令碼 (XSS) 攻擊|
|958023|跨網站指令碼 (XSS) 攻擊|
|958010|跨網站指令碼 (XSS) 攻擊|
|958411|跨網站指令碼 (XSS) 攻擊|
|958422|跨網站指令碼 (XSS) 攻擊|
|958036|跨網站指令碼 (XSS) 攻擊|
|958000|跨網站指令碼 (XSS) 攻擊|
|958018|跨網站指令碼 (XSS) 攻擊|
|958406|跨網站指令碼 (XSS) 攻擊|
|958040|跨網站指令碼 (XSS) 攻擊|
|958052|跨網站指令碼 (XSS) 攻擊|
|958037|跨網站指令碼 (XSS) 攻擊|
|958049|跨網站指令碼 (XSS) 攻擊|
|958030|跨網站指令碼 (XSS) 攻擊|
|958041|跨網站指令碼 (XSS) 攻擊|
|958416|跨網站指令碼 (XSS) 攻擊|
|958024|跨網站指令碼 (XSS) 攻擊|
|958059|跨網站指令碼 (XSS) 攻擊|
|958417|跨網站指令碼 (XSS) 攻擊|
|958020|跨網站指令碼 (XSS) 攻擊|
|958045|跨網站指令碼 (XSS) 攻擊|
|958004|跨網站指令碼 (XSS) 攻擊|
|958421|跨網站指令碼 (XSS) 攻擊|
|958009|跨網站指令碼 (XSS) 攻擊|
|958025|跨網站指令碼 (XSS) 攻擊|
|958413|跨網站指令碼 (XSS) 攻擊|
|958051|跨網站指令碼 (XSS) 攻擊|
|958420|跨網站指令碼 (XSS) 攻擊|
|958407|跨網站指令碼 (XSS) 攻擊|
|958056|跨網站指令碼 (XSS) 攻擊|
|958011|跨網站指令碼 (XSS) 攻擊|
|958412|跨網站指令碼 (XSS) 攻擊|
|958008|跨網站指令碼 (XSS) 攻擊|
|958046|跨網站指令碼 (XSS) 攻擊|
|958039|跨網站指令碼 (XSS) 攻擊|
|958003|跨網站指令碼 (XSS) 攻擊|
|973300|偵測到可能的 XSS 攻擊 - HTML 標記處理常式|
|973301|偵測到 XSS 攻擊|
|973302|偵測到 XSS 攻擊|
|973303|偵測到 XSS 攻擊|
|973304|偵測到 XSS 攻擊|
|973305|偵測到 XSS 攻擊|
|973306|偵測到 XSS 攻擊|
|973307|偵測到 XSS 攻擊|
|973308|偵測到 XSS 攻擊|
|973309|偵測到 XSS 攻擊|
|973311|偵測到 XSS 攻擊|
|973313|偵測到 XSS 攻擊|
|973314|偵測到 XSS 攻擊|
|973331|IE XSS 篩選器 - 偵測到攻擊。|
|973315|IE XSS 篩選器 - 偵測到攻擊。|
|973330|IE XSS 篩選器 - 偵測到攻擊。|
|973327|IE XSS 篩選器 - 偵測到攻擊。|
|973326|IE XSS 篩選器 - 偵測到攻擊。|
|973346|IE XSS 篩選器 - 偵測到攻擊。|
|973345|IE XSS 篩選器 - 偵測到攻擊。|
|973324|IE XSS 篩選器 - 偵測到攻擊。|
|973323|IE XSS 篩選器 - 偵測到攻擊。|
|973348|IE XSS 篩選器 - 偵測到攻擊。|
|973321|IE XSS 篩選器 - 偵測到攻擊。|
|973320|IE XSS 篩選器 - 偵測到攻擊。|
|973318|IE XSS 篩選器 - 偵測到攻擊。|
|973317|IE XSS 篩選器 - 偵測到攻擊。|
|973329|IE XSS 篩選器 - 偵測到攻擊。|
|973328|IE XSS 篩選器 - 偵測到攻擊。|

### <a name="crs_42_tight_security"></a><a name="crs42"></a>crs_42_tight_security

|RuleId|Description|
|---|---|
|950103|路徑周遊攻擊|

### <a name="crs_45_trojans"></a><a name="crs45"></a> crs_45_trojans

|RuleId|Description|
|---|---|
|950110|後門程式存取|
|950921|後門程式存取|
|950922|後門程式存取|

---

## <a name="next-steps"></a>後續步驟

- [使用 Azure 入口網站自訂 Web 應用程式防火牆規則](application-gateway-customize-waf-rules-portal.md)
