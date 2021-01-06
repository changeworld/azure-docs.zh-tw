---
title: 規劃 Microsoft 商業 marketplace 的受控服務供應專案
description: 如何使用 Microsoft 合作夥伴中心中的商業 Marketplace 方案規劃 Azure Marketplace 的新受控服務供應專案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.date: 12/23/2020
ms.openlocfilehash: a504969d23fce8000119aadf9e45d599da0894f0
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918115"
---
# <a name="how-to-plan-a-managed-service-offer-for-the-microsoft-commercial-marketplace"></a>如何規劃 Microsoft 商業 marketplace 的受控服務供應專案

本文將介紹透過合作夥伴中心將受控服務供應專案發佈至 Microsoft 商用 marketplace 的需求。

受管理的服務是 Azure Marketplace 的供應專案，可透過 Azure Lighthouse 啟用跨租使用者和多租使用者管理。 若要深入瞭解，請參閱 [什麼是 Azure Lighthouse？](../lighthouse/overview.md) 當客戶購買受管理的服務供應專案時，他們就能夠委派一或多個訂用帳戶或資源群組

## <a name="eligibility-requirements"></a>資格要求

若要發佈受管理的服務，您必須在雲端平臺獲得金級或銀級的 Microsoft 專長認證。 此專長認證會向客戶展示您的專業知識。 如需詳細資訊，請參閱 [Microsoft 合作夥伴網路](https://partner.microsoft.com/membership/competencies)專長認證。

供應專案必須符合所有適用的 [商業 marketplace 認證原則](https://docs.microsoft.com/legal/marketplace/certification-policies) ，才能在 Azure Marketplace 上發佈。

## <a name="customer-leads"></a>潛在客戶

您必須將您的供應專案連線到客戶關係管理 (CRM) 系統才能收集客戶資訊。 系統會要求客戶提供權限，以分享客戶資訊。 這些客戶詳細資料，以及供應專案名稱、識別碼和線上商店找到您的供應專案，將會傳送至您所設定的 CRM 系統。 商業 marketplace 支援不同類型的 CRM 系統，以及使用 Azure 資料表或使用 Power Automate 設定 HTTPS 端點的選項。

在供應專案建立期間或之後，您可以隨時新增或修改 CRM 連接。 如需詳細指引，請參閱 [您的商業 marketplace 供應專案客戶潛在客戶](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

## <a name="legal-contracts"></a>法律合約

在合作夥伴中心的 [內容] 頁面中，系統會要求您提供使用供應專案的 **條款及條件** 。 您可以直接在合作夥伴中心中輸入您的條款，或提供可在其中找到這些條款的 URL。 客戶必須先接受這些條款及條件，才能購買您的供應專案。

## <a name="offer-listing-details"></a>供應專案清單詳細資料

當您在合作夥伴中心中建立受控服務供應專案時，您將會輸入文字、影像、檔和其他供應專案詳細資料。 當客戶在 Azure Marketplace 上探索您的供應專案時，就會看到這項資訊。 請參閱下列範例：

:::image type="content" source="media/example-managed-service.png" alt-text="說明受管理的服務供應專案如何顯示在 Azure Marketplace 上。":::

**撥出描述**

1. 標誌
1. 名稱
1. 簡短描述
1. 類別
1. 法律合約和隱私權原則
1. 描述
1. 螢幕擷取畫面/影片
1. 實用連結

以下是供應專案清單如何出現在 Azure 入口網站中的範例：

:::image type="content" source="media/example-managed-service-azure-portal.png" alt-text="說明這項供應專案如何出現在 Azure 入口網站中。":::

**撥出描述**

1. 名稱
2. 描述
3. 實用連結
4. 螢幕擷取畫面/影片

> [!NOTE]
> 如果您的供應專案是英文以外的語言，供應專案清單可以是該語言，但描述必須以英文片語開頭或結尾，「這項服務可用於 &lt; 您的供應專案內容的語言>」。 您也可以使用與供應專案清單詳細資料中所用不同的語言來提供支援檔。

為了讓您更輕鬆地建立您的供應專案，請事先準備一些專案。 除非另有說明，否則需要下列專案。

**名稱**：這會顯示為您的供應專案清單在商業市集中的標題。 名稱可能是商標， 除非 (是商標和著作權符號) ，而且必須限制為50個字元，否則它不能包含 emoji。

**搜尋結果摘要**：以100個字元或更少的方式描述您供應專案的目的或目標。 此摘要可用於商業市場清單搜尋結果。 它不應該與標題相同。 請考慮包含您最熱門的 SEO 關鍵字。

**簡短描述**：提供供應專案的簡短描述， (最多256個字元) 。 它會顯示在您的供應專案清單中 Azure 入口網站。

**描述**：以3000個字元或更少的方式描述您的供應專案。 此描述會顯示在商業 marketplace 清單中。 請考慮包括價值主張、主要優點、類別或產業關聯，以及任何必要的洩漏。

以下是撰寫描述的一些祕訣：

* 在前幾個句子中清楚描述供應項目的價值，包括：
    * 可從供應項目獲益的使用者類型。
    * 客戶需要或提出供應專案位址的問題。
* 請記住，前幾個句子可能會顯示在搜尋結果中。
* 使用特定產業的詞彙。

您可以使用 HTML 標籤來格式化您的描述。 如需有關 HTML 格式的詳細資訊，請參閱 [商業 marketplace 供應專案說明中所支援的 html 標記](./supported-html-tags.md)。

**隱私權原則連結**：提供網站上託管之隱私權原則的 URL。 您必須負責確保您的供應專案符合隱私權法律和規定，並提供有效的隱私權原則。

**有用的連結** (選擇性) ：上傳關於您供應專案的補充線上檔。

**連絡人資訊**：提供公司中兩個人的姓名、電子郵件地址和電話號碼 (您可以是其中之一) ：支援連絡人和工程連絡人。 我們會使用此資訊與您溝通您的供應專案。 此資訊不會向客戶顯示，但可提供給雲端解決方案提供者 (CSP) 合作夥伴

**支援 url** (選用) ：如果您有 Azure 全球客戶和/或 Azure Government 客戶的支援網站，請提供這些 url。

**Marketplace 媒體–標誌**：為您的供應專案提供大尺寸標誌的 PNG 檔案。 合作夥伴中心將使用它來建立中等和小型標誌。 您可以選擇性地使用不同的映射取代這些標誌。

* 從 216 x 216 到 350 x 350 px) 的大型標誌 (會出現在 Azure Marketplace 的供應專案清單中。
* 建立新資源時，會顯示中型標誌 (90 x 90 px) 。
* Azure Marketplace 搜尋結果中會使用 (48 x 48 px) 的小型標誌。

您的標誌應遵循這些指導方針：

* 請確定映射未伸展。
* Azure 設計具有簡單的調色盤。 限制標誌上的主要和次要色彩數目。
* Azure 入口網站色彩為白色和黑色。 請勿使用這些做為您標誌的背景。 我們建議簡單的主要色彩，讓您的標誌顯得顯著。
* 如果您使用透明背景，請確定標誌與文字不是白色、黑色或藍色。
* 您標誌的外觀與風格應該是平面的。 避免漸層。 請勿在標誌上放置文字 (甚至是公司或品牌名稱)。

**Marketplace 媒體-螢幕擷取畫面** (選擇性) ：最多可新增五個影像，以示範您的供應專案的運作方式。 所有影像的大小都必須是 1280 x 720 圖元。PNG 格式。

**Marketplace 媒體–** 影片 (選用) ：上傳最多五部示範您的供應專案的影片。 影片必須裝載于 YouTube 或 Vimeo 上，並且 (1280 x 720 PNG 檔案) 的縮圖。

## <a name="preview-audience"></a>預覽對象

預覽物件可以先存取您的供應專案，才能在 Azure Marketplace 上發佈，以便進行測試。 在合作夥伴中心的 [ **預覽物件** ] 頁面上，您可以定義有限的預覽物件。

> [!NOTE]
> 預覽物件與私人方案不同。 私人方案是您只提供給您所選擇之特定物件的方案。 這可讓您與特定客戶協調自訂方案。

您可以將邀請傳送給 Microsoft 帳戶 (MSA) 或 Azure Active Directory (Azure AD) 電子郵件地址。 手動新增10個電子郵件地址，或使用 .csv 檔案最多匯入20。 如果您的供應專案已上線，您仍然可以定義預覽物件來測試您的供應專案的任何變更或更新。

## <a name="plans-and-pricing"></a>方案和定價

受管理的服務供應專案至少需要一個方案。 方案會定義方案範圍、限制和相關聯的定價（如果適用）。 您可以為您的供應專案建立多個方案，為您的客戶提供不同的技術和定價選項。 如需方案的一般指引，包括私人方案，請參閱 [商用 marketplace 供應專案的方案和定價](plans-pricing.md)。

受控服務僅支援一種定價模型：自備 **授權 (BYOL)**。 這表示您將直接向客戶收取費用，而 Microsoft 不會向您收取任何費用。

## <a name="next-steps"></a>後續步驟

* [建立受控服務供應項目](./create-managed-service-offer.md)
* [陳列供應項目的最佳做法](./gtm-offer-listing-best-practices.md)
