---
title: 如何規劃 Microsoft 商業 marketplace 的諮詢服務供應專案
description: 如何使用 Microsoft 合作夥伴中心中的商用 Marketplace 方案規劃 Microsoft AppSource 或 Azure Marketplace 的新諮詢服務供應專案。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 7f56ff94c4dcf276accc56e8d1076266099d635d
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780700"
---
# <a name="how-to-plan-a-consulting-service-offer-in-the-commercial-marketplace"></a>如何在商業市場中規劃諮詢服務供應專案

本文介紹將諮詢服務供應專案發佈至 Microsoft 商業市場的不同選項和需求。 請閱讀本文，以準備您的供應專案以使用合作夥伴中心發佈。

## <a name="whats-a-consulting-service"></a>什麼是諮詢服務？

諮詢服務是虛擬或親自客戶的合作，可支援及擴充客戶的 Microsoft 產品使用。 藉由諮詢服務，客戶可以評估、評估及部署解決方案，以進一步瞭解其商務目標。 您可以選擇範圍、持續時間和定價結構 (固定價格或免費的服務) 。

諮詢服務供應專案已發佈為 **Contact me** 清單。 這表示客戶會直接從您在清單中提供的資訊與您聯繫。 雖然 Microsoft 會在商業市場中裝載清單，但您可以管理與客戶的所有互動。 您必須負責履行服務、計費、發票處理，以及從客戶收集。

## <a name="primary-products-and-online-stores"></a>主要產品和線上商店

每個諮詢服務都必須專注于下列其中一項 Microsoft 產品，稱為供應專案的 **主要產品** ：

|主要產品                               |                   |
|-------------------------------|-------------------|
|Azure                          |Dynamics 365 Project Service Automation |
|Dynamics 365 Business Central  |Dynamics 365 Sales |
|Dynamics 365 Commerce          |Dynamics 365 Supply Chain Management |
|Dynamics 365 Customer Insights |Microsoft 365 |
|Dynamics 365 Customer Service  |Power Apps |
|Dynamics 365 Field Service     |Power Automate |
|Dynamics 365 Finance           |Power BI |
|Dynamics 365 Marketing         |Power Virtual Agents |
|    |    |

您在合作夥伴中心中選取的主要產品，會決定您的供應專案是否會發佈至 Microsoft AppSource 或 Azure Marketplace。

* 如果諮詢服務的主要產品是 Azure，此供應專案將會列在 Azure Marketplace 上。
* 如果主要產品不是 Azure，供應專案將會列在 AppSource 上。

若要深入瞭解 AppSource 與 Azure Marketplace 之間的差異，請參閱 [商用 Marketplace 線上商店](./overview.md#commercial-marketplace-online-stores)。

## <a name="eligibility-requirements"></a>資格要求

為了向客戶展示您在現場的專業知識，您必須在發佈諮詢服務供應專案之前，先符合一組資格需求。 需求取決於您供應專案核心的產品。 每個主要產品的資格需求完整清單在 [諮詢服務的認證原則](/legal/marketplace/certification-policies#800-consulting-services)中。

> [!NOTE]
> 針對某些主要產品，您的解決方案區域中必須有金級或銀級的 Microsoft 專長認證。 如需詳細資訊，請參閱 [Microsoft 合作夥伴網路](https://partner.microsoft.com/membership/competencies)專長認證。

## <a name="service-type-and-duration"></a>服務類型和持續時間

商業 marketplace 支援五種類型的諮詢服務：

* **評** 量：評估客戶的環境，以判斷解決方案的適用性，並評估其實行的成本和時程表。
* **簡報**：使用架構、示範和客戶範例的解決方案或服務簡介。
* **執行**：可產生完整運作的解決方案的完整安裝。
* **概念證明**：判斷解決方案是否符合客戶需求的有限範圍執行。
* **研討會**：在客戶的內部部署進行互動的互動。 它可能牽涉到以客戶資料或環境為基礎的訓練、簡報、評量或示範。

您的服務應該有最多10周的固定和預先決定的持續時間。 服務持續時間必須在供應專案清單中明確地進行。

## <a name="customer-leads"></a>潛在客戶

您必須將您的供應專案連線到客戶關係管理 (CRM) 系統才能收集客戶資訊。 系統會要求客戶提供權限，以分享客戶資訊。 這些客戶詳細資料，以及供應專案名稱、識別碼和線上商店找到您的供應專案，將會傳送至您所設定的 CRM 系統。 商業 marketplace 支援不同類型的 CRM 系統，以及使用 Azure 資料表或使用 Power Automate 設定 HTTPS 端點的選項。

在供應專案建立期間或之後，您可以隨時新增或修改 CRM 連接。 如需詳細指引，請參閱 [您的商業 marketplace 供應專案客戶潛在客戶](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

## <a name="offer-listing-details"></a>供應專案清單詳細資料

當您在合作夥伴中心中建立諮詢服務供應專案時，將會輸入文字、影像、檔和其他供應專案詳細資料。 當客戶在 AppSource 或 Azure Marketplace 探索您的供應專案時，就會看到這種情況。 請參閱下列範例：

![說明諮詢服務供應專案如何顯示在線上商店中](./media/example-consulting-service.png)

**撥出描述**

1. 標誌
2. 服務類型
3. 專長認證
4. 解決方案區域 (Azure Marketplace) /產品 (AppSource) 
5. 產業
6. 國家/區域
7. 供應項目名稱
8. 搜尋結果摘要
9. 描述
10. 螢幕擷取畫面/影片
11. 支援文件

> [!NOTE]
> 如果您要使用英文以外的語言來提供服務，供應專案清單可以是該語言，但描述必須以英文片語開頭或結尾，也就是「這項服務可供 &lt; 您的供應專案內容使用」>」。 您也可以使用與供應專案清單詳細資料中所用不同的語言來提供支援檔。

為了讓您更輕鬆地建立您的供應專案，請事先準備一些專案。 除非另有說明，否則需要下列專案。

**名稱**：此名稱會顯示為商業市集中您的供應專案清單標題。 名稱可能是商標， 除非 (是商標和著作權符號) ，而且必須限制為50個字元，否則它不能包含 emoji。 此名稱必須包含供應專案的持續時間和服務類型，以最大化搜尋引擎優化 (SEO) 。 必要的格式為 *Name： Duration + type*。 請勿包含您的公司名稱，除非它也是產品名稱。 以下是一些範例：

|別說 |說出  |
|---|---|
|在製造中開始使用 Azure IoT |製造 IoT：2 天評量 |
|Smart Toaster 工作坊 |Smart 烤箱：1周的研討會 |
|依 Contoso SQL Server 遷移 PoC |SQL 移轉：3 週概念證明 |
| | |

**搜尋結果摘要**：以200個字元或更少的方式描述您供應專案的目的或目標。 此摘要可用於商業市場清單搜尋結果。 它不應該與標題相同。 請考慮包含您最熱門的 SEO 關鍵字。

**描述**：此描述會顯示在商業 marketplace 清單中。 請考慮包括價值主張、主要優點、預定的使用者群，以及任何類別或產業關聯。

撰寫描述時，請根據您的服務類型來遵循下列準則：

|服務類型 |描述需求 |
|---|---|
|評量 |包含評估時間超過一天的詳細議程，並清楚說明客戶可預期的交付成果。 |
|簡報 |說明客戶可預期的交付成果。|
|實作 |包含時間超過一天的詳細議程，並描述客戶可預期成為參與成果的工程變更、技術成品或其他成品。 |
|概念證明 |描述客戶可預期成為參與專案成果的工程變更、技術成品或其他成品。 |
|工作坊 |根據您的供應專案持續時間，包含詳細的每日、每週或每月的議程。 清楚表達研討會的學習目標或其他交付成果。 |
| | |

以下是撰寫描述的一些祕訣：

* 在前幾個句子中清楚描述供應項目的價值，包括：
    * 可從供應項目獲益的使用者類型。
    * 供應項目可解決的客戶需求或問題。
* 請記住，前幾個句子可能會顯示在搜尋結果中。
* 如果您的供應專案價格預估，請說明哪些變數會決定最終價格。
* 使用特定產業的詞彙。

您可以使用 HTML 標籤來格式化您的描述。 您最多可以在此方塊中輸入2000個字元的文字，包括 HTML 標籤和空間。 如需有關 HTML 格式的詳細資訊，請參閱 [商業 marketplace 供應專案說明中所支援的 html 標記](./supported-html-tags.md)。

**搜尋關鍵字** (選擇性) ：提供最多三個搜尋關鍵字，讓客戶可以用來在線上商店尋找您的供應專案。 您不需要包含供應專案 **名稱** 和 **描述**。

**持續時間**：您的諮詢服務供應專案必須具有最多10周的預先定義持續時間。

**連絡人資訊**：在合作夥伴中心中，系統會要求您提供名稱、電子郵件地址和您公司中兩個人的電話號碼， (您可以是這兩個連絡人) 之一。 我們會使用此資訊與您溝通您的供應專案。 這些資訊不會向客戶顯示，但可提供給雲端解決方案提供者 (CSP) 合作夥伴。

**支援檔**：上傳至少一個及最多三個客戶面向的 PDF 檔，以提供您的供應專案相關資訊。 例如，他們可能是白皮書或摺頁冊。

**Marketplace 媒體–標誌**：提供大尺寸標誌的 PNG 檔案。 合作夥伴中心將使用它來建立小型標誌。 您可以選擇性地使用不同的影像取代小型標誌。

* 從 216 x 216 到 350 x 350 px 的大型 (，需要) 
* Small (48 x 48 px，選擇性) 

大型標誌會顯示在 Azure Marketplace 或 AppSource 的供應專案清單頁面上。 小型標誌會顯示在 Azure Marketplace 搜尋結果中，或在 AppSource 主頁面和搜尋結果頁面上。

您的標誌應遵循這些指導方針：

* 請確定影像沒有模糊或伸展。
* Azure 設計具有簡單的調色盤。 限制標誌上的主要和次要色彩數目。
* 如果您使用透明背景，請確定標誌與文字不是白色、黑色或藍色。
* 避免標誌或背景中的漸層。 請勿在標誌上放置文字 (甚至是公司或品牌名稱)。

**Marketplace 媒體-螢幕擷取畫面**：新增至少一個和最多五個影像來示範您的供應專案。 所有影像的大小都必須是 1280 x 720 圖元。PNG 格式。

**Marketplace 媒體–** 影片 (選用) ：您最多可以新增四個影片來示範您的供應專案。 影片必須裝載于 YouTube 或 Vimeo 上，並且 (1280 x 720 PNG 檔案) 的縮圖。

## <a name="pricing-and-availability"></a>價格與可用性

您的諮詢服務供應專案可在一或多個國家或地區提供使用。 在合作夥伴中心中，您可以決定每個所選市場的價格。 如需支援的市場與貨幣的完整清單，請參閱 [商用 marketplace 的地理供應和貨幣支援](./marketplace-geo-availability-currencies.md)。


## <a name="next-steps"></a>後續步驟

* [在商業市場中建立諮詢服務供應專案](./create-consulting-service-offer.md)
* [陳列供應項目的最佳做法](./gtm-offer-listing-best-practices.md)