---
title: 了解 Azure 成本管理資料
description: 本文將協助您深入了解 Azure 成本管理所包含的資料，及其處理、收集、顯示、關閉的頻率。
author: bandersmsft
ms.author: banders
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.openlocfilehash: 10bd2e4722751b290263fc0599890ca92cd743c9
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995644"
---
# <a name="understand-cost-management-data"></a>了解成本管理資料

本文將協助您深入了解 Azure 成本管理所包含的 Azure 成本和使用量資料。 也會說明處理、收集、顯示和關閉資料的頻率。 您須按月支付使用 Azure 的費用。 雖然計費週期是以月為期間，週期的開始和結束日期會因訂用帳戶類型而有所不同。 「成本管理」接收使用量資料的頻率會隨著不同的因素而改變。 這類因素包括處理資料所需的時間，以及 Azure 服務向計費系統發出使用量資料的頻率。

Azure 成本管理涵蓋所有使用量和購買，包括保留和 Enterprise 合約 (EA) 帳戶的第三方供應項目。 採用隨用隨付費率的 Microsoft 客戶合約帳戶和個別訂用帳戶僅包含來自 Azure 和 Marketplace 服務的使用量。 不包含支援和其他成本。 產生發票之前的成本皆為估計值，不會計入點數。

如果您有新的訂用帳戶，您無法立即使用成本管理功能。 最多可能需要48小時的時間，才能使用所有的成本管理功能。

## <a name="supported-microsoft-azure-offers"></a>支援的 Microsoft Azure 供應項目

下列資訊顯示 Azure 成本管理中目前支援的 [Microsoft Azure 供應項目](https://azure.microsoft.com/support/legal/offer-details/)。 Azure 供應項目是您 Azure 訂用帳戶的類型。 Azure 成本管理中提供的資料是從**資料起始日**的日期開始。 如果訂用帳戶變更了供應項目，則無法提供供應項目變更日期之前的成本。

| **類別目錄**  | **供應項目名稱** | **配額識別碼** | **供應項目號碼** | **資料起始日** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 2014 年 5 月<sup>1</sup> |
| **Enterprise 合約 (EA)** | Enterprise 開發/測試                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 2014 年 5 月<sup>1</sup> |
| **Enterprise 合約 (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 2014 年 5 月<sup>1</sup> |
| **Microsoft 客戶合約** | [Microsoft Azure 方案](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/A | 2019 年 3 月<sup>3</sup> |
| **Microsoft 客戶合約** | [用於開發/測試的 Microsoft Azure 方案](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/A | 2019 年 3 月<sup>3</sup> |
| **合作夥伴支援的 Microsoft 客戶合約** | Microsoft Azure 方案 | CSP_2015-05-01、CSP_MG_2017-12-01、CSPDEVTEST_2018-05-01<br><br>配額識別碼會重複用於 Microsoft 客戶合約和舊版 CSP 訂用帳戶。 目前僅支援 Microsoft 客戶合約訂用帳戶。 | N/A | 2019 年 10 月 |
| **Microsoft Developer Network (MSDN)** | [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2018 年 10 月 2 日<sup>2</sup> |
| **隨用隨付** | [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2018 年 10 月 2 日<sup>2</sup> |
| **隨用隨付** | [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2018 年 10 月 2 日<sup>2</sup> |
| **隨用隨付** | [Microsoft 合作夥伴網路](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2018 年 10 月 2 日<sup>2</sup> |
| **隨用隨付** | [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2018 年 10 月 2 日<sup>2</sup> |
| **隨用隨付** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2018 年 10 月 2 日<sup>2</sup> |
| **隨用隨付** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P、MS-AZR-0122P - MS-AZR-0125P、MS-AZR-0128P - MS-AZR-0130P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2018 年 10 月 2 日<sup>2</sup> |

_<sup>**1**</sup> 如需 2014 年 5 月之前的資料，請造訪 [Azure 企業版入口網站](https://ea.azure.com)。_

_<sup>**2**</sup> 如需 2018 年 10 月 2 日前的資料，請造訪 [Azure 帳戶中心](https://account.azure.com/subscriptions)。_

_<sup>**3**</sup> Microsoft 客戶合約於 2019 年 3 月開始，在此時間點之前沒有任何歷程記錄資料。_

_<sup>**4**</sup> 點數型和預付型訂用帳戶的歷程記錄資料可能與您的發票不符。請參閱下面的[歷程記錄資料可能與發票不符](#historical-data-might-not-match-invoice)。_

尚未支援以下供應項目：

| 類別  | **供應項目名稱** | **配額識別碼** | **供應項目號碼** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure 德國隨用隨付](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government 隨用隨付 | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **雲端解決方案提供者 (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **雲端解決方案提供者 (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **雲端解決方案提供者 (CSP)** | CSP 中適用於 Microsoft Cloud Germany 的 Azure Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **隨用隨付**                 | Azure 學生入門版 | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **隨用隨付** | [Microsoft Azure 學生版](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **隨用隨付**                 | [Microsoft Azure 贊助](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **支援方案** | 標準支援                    | Default_2014-09-01 | MS-AZR-0041P |
| **支援方案** | 專業指導支援         | Default_2014-09-01 | MS-AZR-0042P |
| **支援方案** | 開發人員支援                   | Default_2014-09-01 | MS-AZR-0043P |
| **支援方案** | 德國支援方案                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **支援方案** | Azure Government 標準支援   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **支援方案** | Azure Government 專業指導支援 | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **支援方案** | Azure Government 開發人員支援  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

### <a name="free-trial-to-pay-as-you-go-upgrade"></a>從免費試用升級至隨用隨付

如需從免費試用升級至隨用隨付定價之後，免費層服務可用性的相關資訊，請參閱 [Azure 免費帳戶常見問題](https://azure.microsoft.com/free/free-account-faq/)。

### <a name="determine-your-offer-type"></a>判斷供應項目類型

如果您未看到訂用帳戶的資料，而您想要確認訂用帳戶是否屬於支援的供應項目，您可以驗證您的訂用帳戶是否受支援。 若要確認 Azure 訂用帳戶是否受支援，請登入 [Azure 入口網站](https://portal.azure.com)。 然後，在左側功能表窗格中選取 [所有服務]。 在服務清單中，選取 [訂用帳戶]。 在訂用帳戶清單功能表中，選取您要確認的訂用帳戶。 您的訂用帳戶會顯示在 [概觀] 索引標籤上，您會看到 [供應項目] 和 [供應項目識別碼]。 下圖說明一個範例。

![顯示供應項目和供應項目識別碼的訂用帳戶概觀範例](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>包含在成本管理中的成本

下表顯示「成本管理」中包含或未包含的資料。 產生發票之前的所有成本皆為估計值。 顯示的成本不包括免費和預付的點數。

| **包含** | **未包含** |
| --- | --- |
| Azure 服務使用量<sup>5</sup>        | 支援費用 - 如需詳細資訊，請參閱[發票條款說明](../understand/understand-invoice.md)。 |
| Marketplace 供應項目使用量<sup>6</sup> | 稅金 - 如需詳細資訊，請參閱[發票條款說明](../understand/understand-invoice.md)。 |
| Marketplace 購買<sup>6</sup>      | 點數 - 如需詳細資訊，請參閱[發票條款說明](../understand/understand-invoice.md)。 |
| 保留購買<sup>7</sup>      |  |
| 保留購買的分期<sup>7</sup>      |  |

_<sup>**5**</sup> Azure 服務使用量是依據保留和議定價格。_

_<sup>**6**</sup> Marketplace 購買目前不適用於 MSDN 和 Visual Studio 供應項目。_

_<sup>**7**</sup> 保留購買目前僅適用於 Enterprise 合約 (EA) 和 Microsoft 客戶合約帳戶。_

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>如何在成本和使用量資料中使用標記

Azure 成本管理會在個別服務提交的每個使用記錄中接收標記。 這些標記適用下列限制：

- 標記必須直接套用至資源，而且不會隱含繼承自父資源群組。
- 僅部署至資源群組的資源可支援資源標記。
- 某些已部署的資源可能不支援標記，或是可能無法在使用量資料中包含標記 – 請參閱 [Azure 資源的標記支援](../../azure-resource-manager/tag-support.md)。
- 資源標記只會在套用標記時，包含在使用量資料中 – 標記不會套用至歷史記錄資料。
- 只有在重新整理資料後，您才能在成本管理中使用資源標記，請參閱[成本和使用量資料更新與保留](#cost-and-usage-data-updates-and-retention)。
- 資源標記只有在資源為作用中/執行中，以及正在產生使用量記錄時 (例如，未將 VM 解除配置時)，才可在成本管理中使用。
- 管理標記需要每個資源的參與者存取權。
- 管理標記原則需要管理群組、訂用帳戶或資源群組的擁有者或原則參與者存取權。
    
如果您在成本管理中看不到特定標記，請考量下列事項：

- 標記是否已直接套用至資源？
- 標記套用的時間是否已超過 24 小時？ 請參閱 [成本和使用量資料更新與保留](#cost-and-usage-data-updates-and-retention)
- 該資源類型是否支援標記？ 自 2019 年 12 月 1 日起，下列資源類型不支援在使用量資料中使用標記。 如需支援內容的完整清單，請參閱[適用於 Azure 資源的 標記支援](../../azure-resource-manager/tag-support.md)。
    - Azure Active Directory B2C 目錄
    - Azure 防火牆
    - Azure NetApp Files
    - Data Factory
    - Databricks
    - 負載平衡器
    - 網路監看員
    - 通知中樞
    - 服務匯流排
    - 時間序列深入解析
    
以下是使用標記的一些秘訣：

- 請先行規劃並定義標記策略，以便您依據組織、應用程式、環境等項目來細分成本。
- 使用 Azure 原則將資源群組標記複製到個別資源，並強制執行您的標記策略。
- 將標記 API 與 Query 或 UsageDetails 搭配使用，以根據目前的標記取得所有成本。


## <a name="cost-and-usage-data-updates-and-retention"></a>成本和使用量資料更新與保留

成本與使用量資料通常可於 8 至 24 小時內從 Azure 入口網站和[支援 API](../index.yml) 的 [成本管理 + 計費] 中取得。 檢閱成本時，請記住以下幾點：

- 每個 Azure 服務 (例如儲存體、計算和 SQL) 都會以不同的間隔發出使用量資料 – 您可能會看到某些服務的資料的時間比其他服務更早。
- 目前計費期間的預估費用每天會更新 6 次。
- 目前計費期間的預估費用可能隨著您產生更多使用量而變更。
- 每次更新將持續累積，包含前次更新的所有明細項目和資訊。
- Azure 會在計費期間結束後的 72 小時 (三個日曆天) 內終止或_關閉_目前計費期間。

下列範例說明計費期間的結束方式：

* Enterprise 合約 (EA) 訂用帳戶 – 如果計費月份在 3 月 31 日結束，預估費用將會在 72 個小時內更新。 在此範例中，即 4 月 4 日午夜 (UTC)。
* 隨用隨付訂用帳戶 – 如果計費月份在 5 月 15 日結束，預估費用將可在 72 個小時內更新。 在此範例中，即 5 月 19 日午夜 (UTC)。

一旦成本和使用量資料可在「成本管理 + 帳單」中查看後，這些資料將會保留至少 7 年。

### <a name="rerated-data"></a>重新評估的資料

無論您使用[成本管理 API](../index.yml)、PowerBI 還是 Azure 入口網站來擷取資料，都應預期目前計費期間的費用可能進行重新評估，且持續變更，直到發票結算為止。

## <a name="cost-rounding"></a>成本四捨五入

成本管理中顯示的成本會四捨五入。 查詢 API 所傳回的成本不會四捨五入。 例如：

- Azure 入口網站中的成本分析 - 費用會使用標準四捨五入規則來進位：大於 0.5 和更高的值會向上捨入，否則會將成本向下捨去。 只有在顯示值時，才會四捨五入。 資料處理和匯總期間不會四捨五入。 例如，成本分析會匯總成本，如下所示：
  -    費用 1：$0.004
  - 費用 2：$0.004
  -    轉譯的匯總費用：0.004 + 0.004 = 0.008。 顯示的費用為 $0.01。
- 查詢 API - 費用會顯示八個小數位數，而且不會四捨五入。

## <a name="historical-data-might-not-match-invoice"></a>歷程記錄資料可能與發票不符

點數型和預付型供應項目的歷程記錄資料可能與您的發票不符。 某些 Azure 隨用隨付、MSDN、Visual Studio 供應項目可以將 Azure 點數和預付款套用到發票。 不過，Azure 成本管理中顯示的歷程記錄資料僅以您的預估耗用量費用為依據。 Azure 成本管理的歷程記錄資料不包含付款和點數。 因此，下列供應項目顯示的歷程記錄資料可能不會與您的發票完全相符。

- Microsoft Azure 學生版 (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P、MS-AZR-0123P、MS-AZR-0125P、MS-AZR-0128P、MS-AZR-0129P)
- 免費試用 (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P、MS-AZR-0059P、MS-AZR-0060P、MS-AZR-0063P、MS-AZR-0064P)

## <a name="see-also"></a>另請參閱

- 如果您尚未完成成本管理的第一個快速入門，請在[開始分析成本](../../cost-management/quick-acm-cost-analysis.md)閱讀它。
