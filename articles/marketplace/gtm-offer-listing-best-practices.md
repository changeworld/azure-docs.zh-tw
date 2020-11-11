---
title: 供應項目清單最佳做法 - Microsoft 商業市集
description: 了解 Microsoft AppSource 和 Azure Marketplace 供應項目的進入市場清單最佳做法。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 07/06/2020
ms.openlocfilehash: 3ea6a0035a9f9354be5c14699936c6a07dea1150
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94492084"
---
# <a name="offer-listing-best-practices"></a>陳列供應項目的最佳做法

本文提供建立和參與 Microsoft 商業市場優惠的建議。 下表概述在合作夥伴中心完成供應項目資訊的最佳做法。 如需供應項目執行方式的分析，請移至合作夥伴中心內的 [Marketplace Insights 儀表板](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/marketplaceinsights)。 

## <a name="online-store-offer-details"></a>線上商店供應專案詳細資料

| 設定 | 最佳做法 |
|:--- |:--- |  
| 供應項目名稱 | 針對應用程式，請提供包含搜尋關鍵字的清楚標題，以協助客戶探索您的供應專案。 <br> <br> 針對諮詢服務，請遵循下列格式： [供應專案名稱： [持續時間] [供應專案類型] (例如 Contoso：2周的執行)  |
| 供應項目描述 | 在前幾個句子中提供描述供應專案價值主張的清楚描述。  請記住，這些句子可能會用在搜尋引擎結果中。 價值主張的核心元件應該包括： <ul> <li>產品或解決方案的描述。 </li> <li> 從產品或解決方案獲益的使用者角色。 </li> <li> 客戶需求或產品或解決方案位址的難題。 </li> </ul> <br> 盡可能使用業界標準詞彙或以權益為基礎的用語。  請勿依賴特色與功能來銷售您的產品。  反之，要鎖定在您所提供的價值。 <br> <br> 針對諮詢服務清單，請明確陳述您提供的專業服務。 |

> [!IMPORTANT]
> 參考 microsoft 商標與 Microsoft 軟體、產品和服務的名稱時，請確定您的供應專案名稱和供應專案描述遵守 **[Microsoft 商標和品牌指導方針](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general.aspx)** ，以及其他相關的特定產品指導方針。

## <a name="online-store-listing-details"></a>線上商店清單詳細資料

不同線上商店的類別和產業將適用于不同的供應專案類型。

| 線上商店 | 類別 <br>依線上商店 | 類別 <br>依線上商店 | 產業 <br> 針對 AppSource |
| :------------------- |:----------------:|:------:|:-------------:|
| **供應專案類型**   |  **Azure Marketplace**  | **AppSource**  |
| Azure 應用程式 | X | |
| 容器 | X | |
| 諮詢服務 | | | X |
| Dynamics 365 Customer Engagement & Power Platform | | X | X |
| Dynamics 365 財務 & 供應鏈管理 | | X | X | 
| Dynamics 365 Business Central | | X | X |
| IoT Edge 模組 | X | |
| Power BI | | X | X |
| SaaS | X | X | X |
| Azure 虛擬機器 |  X |    |

### <a name="categories"></a>類別

Microsoft AppSource 和 Azure Marketplace 是提供不同方案類型的線上商店。 Azure Marketplace 提供以 Azure 為基礎或適用于 Azure 的 IT 解決方案。  Microsoft AppSource 供應商務解決方案，例如產業 SaaS 應用程式、Dynamics 365 增益集、Microsoft 365 增益集，以及 Power Platform 應用程式。

類別和子類別會根據方案類型對應到每個線上商店。 您的供應專案將會發佈至 Microsoft AppSource 或 Azure Marketplace，視供應專案類型、供應專案的交易功能以及類別/子類別選取專案而定。 

選取最符合您解決方案類型的類別和子類別。 您可以選取：

* 最多兩個類別，包括主要和次要類別 (選擇性) 。
* 每個主要和/或次要類別最多可有兩個子類別。 如果未選取任何子類別，則您的供應專案只會在選取的類別上被搜尋。

[!INCLUDE [categories and subcategories](./includes/categories.md)]

#### <a name="important-saas-offers-and-microsoft-365-add-ins"></a>重要： SaaS 提供和 Microsoft 365 增益集

請參閱 [商用市集中的交易](marketplace-commercial-transaction-capabilities-and-considerations.md) ，以取得有關交易功能可能會如何影響 marketplace 客戶可查看和購買供應專案的特定詳細資料。 針對 SaaS 供應專案，供應專案的交易功能以及類別選取專案將會決定您的供應專案將發佈到的線上商店。


| SaaS 供應項目    | SaaS 供應項目   | SaaS 供應項目  | SaaS 供應項目   | SaaS 供應項目   | SaaS 供應項目   | SaaS 供應項目    | 適用的線上商店| 適用的線上商店 |
|:-------------:|:---:|:--------:|:---------:|:--:|:--:|:---:|:---------------------:|:-------------:|
| 計量付費 | Microsoft 365 增益集 | 與我連絡 | 交易 (至少1個計畫)  | 僅限私用方案 | 僅限公用方案 | 公用 & 私人方案 | AppSource | Azure Marketplace |
|  | X |  |  |  |  |  | X |  |
| X |  |  | X | X |  |  |  | X |
| X |  |  | X |  | X |  |  | X |
| X |  |  | X |  |  | X |  | X<sup>2</sup> |
|  |  |  | X | X |  |  |  | X |
|  |  |  | X |  | X |  | X<sup>1</sup> | X<sup>1</sup> |
|  |  |  | X |  |  | X | X<sup>1</sup> | X<sup>1、2</sup> |
|  |  | X |  |  |  |  | x<sup>1</sup> | X<sup>1</sup> | 

1. 依類別/子類別和產業選取專案而定
2. 具有私人方案的供應專案將會發佈至 Azure 入口網站


### <a name="industries"></a>產業

產業選擇僅適用於發佈至 Azure Marketplace 中所發佈 AppSource 和諮詢服務的供應項目。  如果您的供應項目滿足業界特定需求，請選取產業及/或垂直產業，並在供應項目描述中喚起業界特定功能。 您最多可選取兩個 (2) 個產業，而每個所選產業可有兩個 (2) 垂直產業。

>[!Note]
>針對 Azure Marketplace 中的諮詢服務供應專案，沒有任何產業縱向。

| **產業** |  **垂直** |
| :------------------- | :----------------|
| **農業** | |
| **架構 & 結構** | |
| **汽車** | |
| **Distribution** | 批發 <br> 包裹 & 套件寄送 |  
| **教育訓練** | 高等教育 <br> 主要 & 次要 Edu/K-12 <br> & 博物館的程式庫 |
| **金融服務** | 銀行 & 資本市場 <br> Insurance | 
| **政府** |  防禦 & 情報 <br> 文職政府 <br> 公共安全 & 司法 |
| **醫療保健** | 健全狀況醫療給付 <br> 健康狀態提供者 <br> Pharmaceuticals | 
| **旅遊 & 旅遊** | 旅遊和運輸 <br> 飯店 & 的休閒 <br> 餐廳 & 食物服務 | 
| **製造 & 資源** | 化學 & Agrochemical <br> 離散製造 <br> 能源 | 
| **媒體 & 通訊** | 媒體 & 娛樂 <br> 電信業 | 
| **其他公共事業產業** | 林業 & 釣魚 <br> 非營利 | 
| **專業服務** | 合作夥伴專業服務 <br> Legal | 
| **房地產** | |

僅適用于 Microsoft AppSource 的產業：

| **產業** |  **垂直** |
| :------------------- | :----------------|
| **零售 & 消費性商品** | 零售商 <br> 消費品 |

### <a name="applicable-products"></a>適用的產品

選取您的應用程式使用的適用產品，讓供應項目顯示在 AppSource 中的所選產品之下。

### <a name="search-keywords"></a>搜尋關鍵字

關鍵字可協助客戶在進行搜尋時尋找您的供應項目。 識別您供應項目的熱門搜尋關鍵字，將其併入您的供應項目摘要和描述以及供應項目清單詳細資料區段的關鍵字區段中。

## <a name="online-store-marketing-details"></a>線上商店行銷詳細資料
| 設定 | 最佳做法 |
|:--- |:--- |  
| 供應專案標誌 (PNG 格式，從216×216到 350 x 350 px) ：應用程式詳細資料頁面 | 針對數位媒體設計及最佳化您的標誌：<br>以 PNG 格式上傳標誌到您供應項目的應用程式詳細資料清單頁面。 合作夥伴中心會將其大小調整為所需的標誌大小。 |
| 供應專案標誌 (PNG 格式，48×48圖元) ：搜尋頁面 | 合作夥伴中心將會從您上傳的大型標誌產生此標誌。 您可以選擇性地將此取代為不同的映射。 |
| 「深入了解」文件 | 在 [深入了解] 下包括支援業務與行銷資產，例如：<ul><li>白皮書</li><li> 摺頁冊</li><li>檢查清單，或是</li><li> PowerPoint 簡報</li></ul><br>將所有檔案都儲存為 PDF 格式。 您此處的目標應該是教育客戶，而不是銷售產品或服務給客戶。<br><br>將您應用程式登陸頁面的連結新增到您的所有文件，並加入 URL 參數以協助您追蹤訪客和試用。 |
| 影片：僅 AppSource、諮詢服務以及 SaaS 供應項目 | 最有力的影片能以敘事方式傳遞供應項目價值：<ul> <li> 將客戶 (而非您的公司) 設定為影片故事的主角。 </li> <li> 您的影片應該解決目標客戶的主要挑戰與目標。 </li> <li> 建議長度：60-90 秒。</li> <li> 在影片名稱中包含搜尋關鍵字。 </li> <li> 請考慮新增其他影片，例如操作說明、使用者入門或是客戶證言。 </li> </ul> |
| 螢幕擷取畫面 (1280&nbsp;&times;&nbsp;720) | 新增最多五個螢幕擷取畫面：<br>在影片名稱中包含搜尋關鍵字。 |

## <a name="link-to-your-offer-page-from-your-website"></a>從您的網站連結至供應項目頁面

當您從網站上的 AppSource 或 Azure Marketplace 徽章連結到您在商業市集中的清單時，可以在 URL 的結尾加入下列查詢參數，以支援強大的分析和報告：
* **src** ：包含路由傳送到 AppSource (例如，網站、LinkedIn 或 Facebook) 的流量來源。
* **mktcmpid** ：您的行銷活動識別碼，最多可包含 16 個字元，而且可以是字母、數字、底線與連字號的任意組和 (例如，blogpost_12)。

下列範例 URL 包含前兩個查詢範例：`https://appsource.microsoft.com/product/dynamics-365/mscrm.04931187-431c-415d-8777-f7f482ba8095?src=website&mktcmpid=blogpost_12`

透過在您的 AppSource URL 新增這些參數，您可以在[合作夥伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/)的分析儀表板中檢閱行銷活動有效性。

## <a name="next-steps"></a>後續步驟

深入了解您的[商業市集權益](./gtm-your-marketplace-benefits.md)。

請登入[合作夥伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)，以建立及設定您的供應項目。
