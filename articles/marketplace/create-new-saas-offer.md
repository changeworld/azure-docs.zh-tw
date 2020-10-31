---
title: 如何在 Microsoft 商業市場中建立 SaaS 供應專案
description: 瞭解如何使用 Microsoft 雲端解決方案提供者中的商用 Marketplace 入口網站，建立新的軟體即服務 (SaaS) 供應專案，以在 Microsoft AppSource、Azure Marketplace 或 () CSP 合作夥伴中心方案中進行清單或銷售。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: f689993ce56a1125a1d1de8f65ce05d01f776ea9
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130063"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>如何在商業市場中建立 SaaS 供應專案

作為商業 marketplace 發行者，您可以建立「軟體即服務」 (SaaS) 供應專案，讓潛在客戶可以購買 SaaS 技術解決方案。 本文說明為 Microsoft 商業市場建立 SaaS 供應專案的流程。

## <a name="before-you-begin"></a>開始之前

如果您還沒有這麼做，請參閱為 [商業市場提供 SaaS 供應](plan-saas-offer.md)專案的方案。 它將說明您的 SaaS 應用程式的技術需求，以及您在建立供應專案時所需的資訊和資產。 除非您打算在商業市場中發佈簡單列表 ( **Contact me** 清單選項) ，否則您的 SaaS 應用程式必須符合有關驗證的技術需求。

## <a name="create-a-new-saas-offer"></a>建立新的 SaaS 供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
1. 在左側導覽功能表中，選取 [ **商業 Marketplace**  >  **總覽** ]。
1. 在 [ **總覽** ] 索引標籤上，選取 [ **+ 新增供應** 專案  >  **軟體即服務** ]。

   :::image type="content" source="media/new-offer-saas.png" alt-text="說明左側導覽功能表和新的供應專案清單。":::

1. 在 [ **新增供應** 專案] 對話方塊中，輸入 **供應專案識別碼** 。 此識別碼會顯示在商用 marketplace 清單的 URL 中，且 Azure Resource Manager 範本（如果適用）。 例如，如果您在此方塊中輸入「 **測試供應專案-1** 」，供應專案的網址將會是 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` 。
   + 您帳戶中的每個供應專案都必須有唯一的供應專案識別碼。
   + 請一律使用小寫字母和數字。 識別碼可以包含連字號和底線，但不能有空格，且限制為 50 個字元。
   + 選取 [ **建立** ] 之後，就無法變更供應專案識別碼。

1. 輸入 **供應項目別名** 。 這是用於合作夥伴中心內供應項目的名稱。

   + 此名稱不會出現在商業 marketplace 中，而是與供應專案名稱和向客戶顯示的其他值不同。
   + 選取 [ **建立** ] 之後，就無法變更供應專案別名。
1. 若要產生供應專案並繼續，請選取 [ **建立** ]。

## <a name="configure-your-saas-offer-setup-details"></a>設定您的 SaaS 供應專案設定詳細資料

在 [ **供應專案設定** ] 索引標籤的 [ **設定詳細資料** ] 底下，您將選擇是要透過 Microsoft 銷售您的供應專案，或個別管理您的交易。 透過 Microsoft 銷售的供應專案稱為 _可交易_ 供應專案，這表示 Microsoft 會代表發行者促進軟體授權的金錢交換。 如需這些選項的詳細資訊，請參閱 [列出選項](plan-saas-offer.md#listing-options) 和 [判斷您的發行選項](determine-your-listing-type.md)。

1. 若要透過 Microsoft 銷售並讓我們為您加速交易，請選取 **[是]** 。 繼續 [啟用測試磁片磁碟機](#enable-a-test-drive-optional)。

1. 若要透過商業 marketplace 和個別處理交易來列出您的供應專案，請選取 [ **否** ]，然後執行下列其中一項：
   + 若要為您的供應專案提供免費訂用帳戶，請選取 [ **立即取得] (免費)** 。 然後，在出現的 [ **供應專案 URL** ] 方塊中，輸入 url (開頭為 *HTTP* 或 *HTTPs* ) ，客戶可以 [使用 Azure Active Directory (Azure AD)](azure-ad-saas.md)，透過單鍵驗證來取得試用版。 例如： `https://contoso.com/saas-app` 。
   + 若要提供30天的免費試用版，請選取 [ **免費試用** ]，然後在出現的 [ **試用 url** ] 方塊中輸入 URL (開頭為 *HTTP* 或 *HTTPs* ) ，客戶可以 [使用 Azure Active Directory (Azure AD)](azure-ad-saas.md)，透過單鍵驗證存取您的免費試用。 例如： `https://contoso.com/trial/saas-app` 。
   + 若要讓潛在客戶聯繫您購買供應專案，請選取 [ **洽詢我** ]。

### <a name="enable-a-test-drive-optional"></a>啟用 (選用) 的試用產品

試用產品是為潛在客戶展示您的供應專案的絕佳方式，方法是讓他們能夠存取預先設定的環境一段固定的時數。 提供試用產品會產生更高的轉換率，並產生高度合格的潛在客戶。 若要深入瞭解試用產品，請參閱 [什麼是試用產品？](./what-is-test-drive.md)。

> [!TIP]
> 試用產品不同于免費試用版。 您可以提供試用產品、免費試用或兩者。 它們都能提供客戶一段固定時間的解決方案。 但是，試用產品也包含實際操作的自我引導式導覽，說明您產品的主要功能，以及在實際執行案例中所示範的優點。

**啟用試用產品**
1.  在 [ **測試磁片磁碟機** ] 下，選取 [ **啟用試用** 產品] 核取方塊。
1.  從顯示的清單中選取 [試用產品] 類型。

### <a name="configure-lead-management"></a>設定潛在客戶管理

將您的客戶關係管理 (CRM) system 與您的商業 marketplace 供應專案連接，讓您可以在客戶表達興趣或部署您的產品時，收到客戶連絡人資訊。 您可以在建立供應專案期間或之後的任何時間修改此連接。

> [!NOTE]
> 如果您是透過 Microsoft 銷售您的供應專案，或您選取了 [ **Contact Me** ] 清單選項，就必須設定潛在客戶管理。 如需詳細指引，請參閱 [您的商業 marketplace 供應專案客戶潛在客戶](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

#### <a name="to-configure-the-connection-details-in-partner-center"></a>若要在合作夥伴中心中設定連接詳細資料

1.  在 [ **客戶潛在客戶** ] 下，選取 [連線 **]** 連結。
1. 在 [ **連接詳細資料** ] 對話方塊中，從清單中選取潛在客戶目的地。
1. 完成顯示的欄位。 如需詳細步驟，請參閱下列文章：

   - [設定供應項目以將潛在客戶傳送至 Azure 資料表](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [設定您的供應專案，以將潛在客戶傳送給 dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (先前為 Dynamics CRM Online) 
   - [設定您的供應專案以將潛在客戶傳送至 HTTPS 端點](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [設定供應項目以將潛在客戶傳送至 Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [設定您的供應項目，以便將潛在客戶傳送至 Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 若要驗證您所提供的設定，請選取 [ **驗證** ] 連結。
1. 若要關閉對話方塊，請選取 **[確定]** 。

## <a name="next-steps"></a>後續步驟

- [如何設定您的 SaaS 供應專案屬性](create-new-saas-offer-properties.md)