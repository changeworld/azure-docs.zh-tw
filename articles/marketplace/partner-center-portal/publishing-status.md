---
title: 檢查您的商業 Marketplace 供應專案的發佈狀態
description: 在 Microsoft 合作夥伴中心，檢查透過商業 Marketplace 發佈供應專案所需的驗證、認證和預覽步驟的狀態。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 012a574887d9980e0c71c3af84ff70ca8d31312c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80275674"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>檢查您的商業 Marketplace 供應專案的發佈狀態

您可以在合作夥伴中心的[商業 Marketplace 入口網站](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)的 [**供應專案總覽**] 索引標籤上，查看目前的**發行狀態**。

每個供應項目應該都會顯示下列狀態指標的其中一個。

| **狀態**    | **描述**  |
| :---------- | :-------------------|
| **草稿** | 已建立供應專案，但尚未發佈。 |
| **正在發行** | 供應專案/方案會透過發佈程式的步驟來運作。 |
| **需要注意** | Microsoft 或任何發佈步驟在認證期間發現重大問題。 |
| **預覽** | 供應專案已由 Microsoft 認證，現在會等待發行者進行最終驗證。 選取 [上線]，讓供應專案上線。 |
| **直播** | 供應專案會在 marketplace 中上線，並可供客戶查看和取得。 |
| **暫止停止銷售** | 發行者已選取 [停止銷售] 供應專案或方案，但該動作尚未完成。 |
| **Marketplace 中未提供** | 已移除 marketplace 中先前發佈的供應專案/方案。 |

## <a name="automated-validation"></a>自動化驗證

發行程式的第一個步驟是一組自動驗證。 每個驗證步驟都對應于您在建立供應專案時所選擇啟用的功能。 如果未啟用該功能，驗證會跳到下一個發佈步驟。 每個驗證檢查都必須在發行狀態核准之前完成。

- **供應專案採購流程設定（<10 分鐘）**

在此步驟中，我們會確保您的供應專案可在客戶透過 Azure 入口網站購買時完成。 此步驟僅適用于透過 Microsoft 銷售的供應專案。

- **測試磁片磁碟機資料驗證（大約5分鐘）**

在此步驟中，我們會驗證您在供應專案的試用產品技術設定一節中所提供的資料。 試用產品功能已通過測試和核准。 此步驟僅適用于已啟用試用產品的供應專案。

- **測試磁片磁碟機布建（~ 30 分鐘）**

在此步驟中，在上一個步驟中驗證試用產品的資料和功能之後，我們會部署並複寫您的試用產品實例，讓它們可供客戶使用。  此步驟僅適用于已啟用試用產品的供應專案。

- **潛在客戶管理驗證和註冊（<15 分鐘）**

在此步驟中，我們會確認您的潛在客戶管理系統可以根據供應專案設定中所提供的詳細資料來接收客戶潛在客戶。 此步驟僅適用于已啟用潛在客戶管理的供應專案。

## <a name="certification"></a>認證

在發佈之前，已提交給合作夥伴中心的商業 Marketplace 的供應專案必須經過認證。 已提交的供應專案經過嚴謹的測試，部分自動化和其他手冊，包括對[Azure Marketplace 認證原則](https://docs.microsoft.com/legal/marketplace/general-policies)的檢查。 供應專案提交必須標示為適用于認證，才能繼續進行發佈流程中的下一個步驟。

### <a name="types-of-validation-that-take-place-during-certification"></a>認證期間所發生的驗證類型

每個提交供應專案的認證程式中包含三個層級的驗證。

- 發行者商務資格
- 內容驗證
- 技術驗證

#### <a name="publisher-business-eligibility"></a>發行者商務資格

每個供應專案類型都會檢查發行者必須符合的一組基本資格準則。 資格準則可能包括發行者的 MPN 狀態、職稱、專長認證層級等等。

#### <a name="content-validation"></a>內容驗證

在內容驗證期間，會檢查您建立供應專案時所輸入的資訊是否具有品質與相關性。 這些檢查會檢查您的專案中是否有 marketplace 清單詳細資料、定價、可用性、相關聯的計畫等等。為了符合 Azure Marketplace 和/或 AppSource 的清單準則，我們會驗證您的供應專案包括：

- 準確描述供應專案的標題;
- 撰寫完善的描述，提供完整的總覽和價值主張;
- 品質螢幕擷取畫面和隨附的影片;和
- 提供供應專案如何利用 Microsoft 平臺和工具的說明。

閱讀[一般清單原則](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general)，以深入瞭解內容驗證準則。

#### <a name="technical-validation"></a>技術驗證

在技術驗證期間，供應專案（封裝或二進位）會經歷下列檢查。
- 已掃描是否有惡意程式碼
- 受監視的網路呼叫
- 已分析封裝
- 完整掃描供應專案的實際功能

此供應專案會跨各種平臺和版本進行測試，以確保其健全。

請在本檔的技術設定一節中，參閱您的供應專案所需的特定設定詳細資料。

### <a name="certification-failure-report"></a>認證失敗報告

完成審查後，如果您的供應專案已通過認證，則會移至發佈程式中的下一個步驟。 如果您的供應專案已失敗任何清單、技術或原則檢查，或如果您不適合提交該類型的供應專案，則會產生認證失敗報告，並透過電子郵件傳送給您。

這份報告包含任何失敗原則的描述，以及審核注意事項。 請參閱此電子郵件報告、解決任何問題、視需要更新您的供應專案，然後使用合作夥伴中心的[商業 Marketplace 入口網站](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)重新提交供應專案。 （您可以視需要重新提交供應專案多次，直到通過認證為止）。

## <a name="preview-creation"></a>預覽建立

在**預覽建立**步驟中，我們會建立您的供應專案版本，僅供您在供應專案的 [預覽] 區段中指定的物件存取。

>[!Note]
> 請勿使用此步驟讓組織外部的人員看到供應專案。 請改用**私用供應**專案選項。 此時，您的供應專案尚未經過完整測試和驗證，而且尚未準備好進行外部散發。

## <a name="publisher-approval"></a>發行者核准

在此步驟中，您將會透過電子郵件傳送要求，讓您在最終發行步驟之前，先審查並核准您的供應專案預覽。

如果您已選擇透過 Microsoft 銷售您的供應專案，您將能夠測試供應專案的取得與部署，以確保它符合您在此預覽核准階段的需求。 您的供應專案將不會在公用 marketplace 中提供。 在您測試並核准此預覽之後，您將需要在[**供應專案總覽**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)儀表板上選取 [**上線**]。

如果您想要在此預覽階段變更供應專案，您可以編輯並重新提交，以發佈新的預覽。 如需更多變更的詳細資料，請參閱[更新現有的 marketplace](#update-existing-marketplace-offers)供應專案一文。

如果您的供應專案已上線並可供 marketplace 中的公眾使用，則您所做的任何更新都將不會上線，直到您在[**供應專案總覽**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)儀錶**板上選取 [上線]** 為止。

### <a name="publish-offer-to-the-public"></a>將供應專案發佈至公用

登入合作夥伴中心，並存取供應專案。 系統會將您重新導向至供應專案的 **[總覽**] 頁面。 在此頁面的頂端，您會看到 [上線] 的**選項。** 選取 [**上線]，** 確認之後，供應專案就會開始發佈至 [公開]。 當供應專案上線時，您會收到電子郵件通知。

## <a name="publish"></a>發佈

現在您已選擇要與您的供應專案**上線**，使其可在 marketplace 中使用，有一系列最終的驗證檢查會逐步執行，以確保即時供應專案的設定就如同供應專案的預覽版本。

- **供應專案採購流程設定（>10 分鐘）**

在此步驟中，我們會確保您的供應專案可在客戶透過 Azure 入口網站購買時完成。 此步驟僅適用于透過 Microsoft 銷售的供應專案。

- **測試磁片磁碟機資料驗證（大約5分鐘）**

在此步驟中，我們會驗證您在供應專案的試用產品技術設定一節中所提供的資料。 試用產品功能已通過測試和核准。 此步驟僅適用于已啟用試用產品的供應專案。

- **測試磁片磁碟機布建（~ 30 分鐘）**

在此步驟中，我們會部署並複寫試用產品的實例，讓它們可供客戶使用。  此步驟僅適用于已啟用試用產品的供應專案。

- **潛在客戶管理驗證和註冊（>15 分鐘）**

在此步驟中，我們會確認您的潛在客戶管理系統可以根據供應專案設定中所提供的詳細資料來接收客戶潛在客戶。 此步驟僅適用于已啟用潛在客戶管理的供應專案。

- **最終發行（>30 分鐘）**

在此步驟中，我們會確保您的供應專案在 marketplace 中可公開使用。

## <a name="update-existing-marketplace-offers"></a>更新現有的 marketplace 供應專案

如果您想要對已發佈的供應專案進行變更，您必須先更新現有的供應專案，然後再發佈一次。

## <a name="next-steps"></a>後續步驟

- [更新商業市集中的現有供應項目](./update-existing-offer.md)
