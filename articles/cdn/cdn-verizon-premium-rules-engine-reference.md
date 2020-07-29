---
title: Azure CDN 規則引擎參考 | Microsoft Docs
description: Azure CDN 規則引擎比對條件和功能的參考文件。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: 75633521474ec3bcbc35cea49ea7a2da6a271e01
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872519"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>來自 Verizon Premium 的 Azure CDN 規則引擎參考

本文會針對 Azure 內容傳遞網路 (CDN) [規則引擎](cdn-verizon-premium-rules-engine.md)列出可用比對條件和功能的詳細描述。

此規則引擎是設計為特定類型要求如何由 CDN 處理的最終授權。

**常見用途**：

- 覆寫或定義自訂快取原則。
- 保護或拒絕機密內容的要求。
- 將要求重新導向。
- 儲存自訂記錄檔資料。
## <a name="key-concepts"></a>重要概念
設定規則引擎的重要概念如下所述。
### <a name="draft"></a>草稿
原則的草稿是由一或多個規則所組成，可用來識別要求，以及將對其套用的一組動作。 草稿是進行中的工作，可在不影響網站流量情況下進行頻繁的設定更新。 當草稿準備好要完成之後，應該將其轉換成唯讀原則。

### <a name="rule"></a>規則
規則可識別一或多種類型的要求，以及將對其套用的一組動作。

其中包括： 

- 一組條件運算式，可定義用來識別要求的邏輯。
- 一組比對條件，可定義用來識別要求的準則。
- 一組功能，可定義 CDN 將如何處理上述要求。
這些項目會在下圖中識別。

![原則部署工作流程](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>原則
原則是由一組唯讀規則所組成，可用來：

- 建立、儲存及管理多種規則版本。
- 復原為先前部署的版本。
- 事先準備事件特定規則 (例如，因客戶來源維護而重新導向流量的規則)。

> [!NOTE]
> 雖然每個環境只允許一個原則，但可視需要部署多個原則。

### <a name="deploy-request"></a>部署要求
部署要求提供一個簡單流暢的程序，其可供快速將原則套用至預備或生產環境。 這會提供部署要求的歷程記錄，以協助追蹤套用至這些環境的變更。

> [!NOTE]
> 只有未通過自動驗證和錯誤偵測系統的要求才需要手動審查及核准。

### <a name="rule-precedence"></a>規則優先順序
原則中包含的規則通常會依其列出順序來處理 (例如，由上到下)。 如果要求符合衝突的規則，則會優先使用最後一個要處理的規則。

### <a name="policy-deployment-workflow"></a>原則部署工作流程
可用來將原則套用至生產或預備環境的工作流程如下所示。

![原則部署工作流程](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|步驟 |描述 |
|---------|---------|
|[建立草稿](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    草稿是由一組規則所組成，可定義 CDN 應該如何處理內容要求。     |
|鎖定草稿   |     當草稿完成之後，應該將其鎖定並轉換成唯讀原則。    |
|[提交部署要求](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> 部署要求可讓原則套用至測試或生產環境流量。</br> <br>將部署要求提交至預備或生產環境。</br>     |
|部署要求審查   |    <br>部署要求會經過自動驗證和錯誤偵測。</br><br>雖然系統會自動核准大部分的部署要求，但更複雜的原則需要手動檢閱。</br>   |
|原則部署 ([預備](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging))   |  <br> 當預備環境的部署要求核准之後，就會將原則套用至預備環境。 此環境可讓原則針對模擬網站流量進行測試。</br><br>當原則準備好要套用至即時網站流量之後，應該對生產環境提交新的部署要求。</br>      |
|原則部署 ([生產](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti))   |  當生產環境的部署要求核准之後，就會將原則套用至生產環境。 此環境可讓原則作為最終授權單位，以判斷 CDN 應該如何處理即時流量。     |
## <a name="syntax"></a>語法

處理特殊字元的方式會根據比對條件或功能如何處理文字值而異。 比對條件或功能可能會以下列其中一種方式解譯文字︰

- [**常值**](#literal-values)
- [**萬用字元值**](#wildcard-values)
- [**規則運算式**](#regular-expressions)

### <a name="literal-values"></a>常值

解譯為常值的文字會將所有特殊字元 (除了 % 符號之外)，視為必須符合值的一部分。 換句話說，設定為 `\'*'\` 的常值比對條件，只有在找到確切值 (亦即，`\'*'\`) 時獲得滿足。

百分比符號是用來表示 URL 編碼 (例如 `%20`)。

### <a name="wildcard-values"></a>萬用字元值

解譯為萬用字元值的文字會將額外意義指派給特殊字元。 下表描述如何解譯下列字元集：

字元 | 描述
----------|------------
\ | 反斜線是用來逸出此資料表中指定的任何字元。 必須在逸出特殊字元之前指定反斜線。<br/>例如，下列語法逸出星號︰`\*`
% | 百分比符號是用來表示 URL 編碼 (例如 `%20`)。
\* | 星號為萬用字元，表示一或多個字元。
Space | 空白字元，表示比對條件可能藉由指定值或模式獲得滿足。
'value' | 單引號不具有特殊意義。 不過，單引號組是用來表示值應視為常值。 使用方式如下：<br><br/>- 它可讓比對條件在指定值符合比較值的任何部分時獲得滿足。  例如，`'ma'` 會比對下列任何字串︰ <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- 它可讓特殊字元指定為常值字元。 例如，您可以指定常值空白字元，方法是在單引號組內括住空白字元 (亦即，`' '` 或 `'sample value'`)。<br/>- 它可以指定空白值。 藉由指定單引號組來指定空白值 (亦即，'')。<br /><br/>**重要：**<br/>- 如果指定值不包含萬用字元，則它會自動被視為常值，這表示不一定要指定一組單引號。<br/>- 如果反斜線不會逸出此資料表中的另一個字元，它會在於一組單引號內指定時遭到忽略。<br/>- 另一種將特殊字元指定為常值字元的方式是使用反斜線逸出它 (亦即，`\`)。

### <a name="regular-expressions"></a>規則運算式

規則運算式會定義要在文字值中搜尋的模式。 規則運算式標記法會將特定意義定義為各種符號。 下表指出支援規則運算式的比對條件和功能如何處理特殊字元。

特殊字元 | 描述
------------------|------------
\ | 反斜線會逸出緊接在後的字元，這會導致該字元被視為常值，而非採用它的規則運算式的意義。 例如，下列語法逸出星號︰`\*`
% | 百分比符號的意義取決於其使用方式。<br/><br/> `%{HTTPVariable}`:這個語法會識別 HTTP 變數。<br/>`%{HTTPVariable%Pattern}`:這個語法會使用百分比符號來識別 HTTP 變數，並作為分隔符號。<br />`\%`:逸出百分比符號可供其作為常值使用，或表示 URL 編碼 (例如 `\%20`)。
\* | 星號可讓前置字元比對零或多次。
Space | 空白字元通常會被視為常值字元。
'value' | 單引號會被視為常值字元。 單引號組不具有特殊意義。

支援規則運算式的比對條件和功能接受 Perl 相容規則運算式 (Perl Compatible Regular Expressions，PCRE) 所定義模式。



## <a name="next-steps"></a>後續步驟

- [規則引擎比對條件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [規則引擎條件運算式](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [規則引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用規則引擎覆寫 HTTP 行為](cdn-verizon-premium-rules-engine.md)
- [Azure CDN 概觀](cdn-overview.md)
