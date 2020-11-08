---
title: 如何在商業市場中建立 Azure 應用程式供應專案
description: 瞭解如何建立新的 Azure 應用程式供應專案，以在 Azure Marketplace 中進行清單或銷售，或使用 Microsoft 合作夥伴中心中的商用 Marketplace 入口網站，透過雲端解決方案提供者 (CSP) 方案。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369993"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>如何在商業市場中建立 Azure 應用程式供應專案

您可以建立 Azure 應用程式供應專案，讓潛在客戶購買您的解決方案，作為商業 marketplace 發行者。 本文說明為 Microsoft 商業 marketplace 建立 Azure 應用程式供應專案的流程。

如果您尚未這麼做，請參閱 [規劃適用于商業 marketplace 的 Azure 應用程式供應](plan-azure-application-offer.md)專案。 它將提供資源，並協助您收集當您建立供應專案時所需的資訊和資產。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。

1. 在左側導覽功能表中，選取 [ **商業 Marketplace**  >  **總覽** ]。

1. 在 [概觀] 頁面上，選取 [+新增供應項目] > [Azure 應用程式]。

    ![說明左側導覽功能表。](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. 在 [ **新增供應** 專案] 對話方塊中，輸入 **供應專案識別碼** 。 這是您帳戶中每個供應項目的唯一識別碼。 此識別碼會顯示在商用 marketplace 清單的 URL 中，且 Azure Resource Manager 範本（如果適用）。 例如，如果您在此方塊中輸入「測試供應專案-1」，供應專案的網址將會是 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` 。

     * 您帳戶中的每個供應專案都必須有唯一的供應專案識別碼。
     * 請一律使用小寫字母和數字。 識別碼可以包含連字號和底線，但不能有空格，且限制為 50 個字元。
     * 選取 [建立] 之後，即無法變更供應項目識別碼。

1. 輸入 **供應項目別名** 。 這是用於合作夥伴中心內供應項目的名稱。

     * 此名稱僅在合作夥伴中心中顯示，且與供應專案名稱和向客戶顯示的其他值不同。
     * 在您選取 [建立] 之後，就無法變更供應項目別名。

1. 若要產生供應專案並繼續，請選取 [  **建立** ]。

## <a name="configure-your-azure-application-offer-setup-details"></a>設定您的 Azure 應用程式供應專案設定詳細資料

在 [ **供應專案設定** ] 索引標籤的 [ **設定詳細資料** ] 底下，您將選擇是否要設定試用產品。 您也會將客戶關係管理 (CRM) system 與您的商業 marketplace 供應專案連線。

### <a name="enable-a-test-drive-optional"></a>啟用 (選用) 的試用產品

試用產品是為潛在客戶展示您的供應專案的絕佳方式，方法是讓他們能夠存取預先設定的環境一段固定的時數。 提供試用產品會產生更高的轉換率，並產生高度合格的潛在客戶。 若要深入瞭解試用產品，請參閱 [試用](plan-azure-application-offer.md#test-drive)產品。

#### <a name="to-enable-a-test-drive"></a>啟用試用產品

- 在 [ **測試磁片磁碟機** ] 下，選取 [ **啟用試用** 產品] 核取方塊。

### <a name="customer-lead-management"></a>客戶潛在客戶管理

將您的客戶關係管理 (CRM) system 與您的商業 marketplace 供應專案連接，讓您可以在客戶表達興趣或部署您的產品時，收到客戶連絡人資訊。

#### <a name="to-configure-the-connection-details-in-partner-center"></a>若要在合作夥伴中心中設定連接詳細資料

1. 在 [ **客戶潛在客戶** ] 下，選取 [連線 **]** 連結。
1. 在 [ **連接詳細資料** ] 對話方塊中，從清單中選取潛在客戶目的地。
1. 完成顯示的欄位。 如需詳細步驟，請參閱下列文章：

   - [設定供應項目以將潛在客戶傳送至 Azure 資料表](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [設定您的供應專案，以將潛在客戶傳送給 dynamics 365 Customer Engagement](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (先前為 Dynamics CRM Online) 
   - [設定您的供應專案以將潛在客戶傳送至 HTTPS 端點](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [設定供應項目以將潛在客戶傳送至 Marketo](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [設定您的供應項目，以便將潛在客戶傳送至 Salesforce](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 若要驗證您所提供的設定，請選取 [ **驗證** ] 連結（如果適用）。
1. 若要關閉對話方塊，請選取 **[連接]** 。
1. 選取 [ **儲存草稿** ]，然後繼續進行下一個索引標籤： [屬性]。

> [!NOTE]
> 請確定潛在客戶目的地的連線保持在最新狀態，如此您就不會遺失任何潛在客戶。 請務必在每次變更時更新這些連接。

## <a name="next-steps"></a>後續步驟

- [如何設定您的 Azure 應用程式供應專案屬性](create-new-azure-apps-offer-properties.md)
