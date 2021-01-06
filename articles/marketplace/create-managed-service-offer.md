---
title: 如何在 Microsoft 商業市場中建立受控服務供應專案
description: 瞭解如何使用 Microsoft 合作夥伴中心中的商業 Marketplace 方案，為 Azure Marketplace 建立新的受控服務供應專案。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918161"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>如何建立商用 marketplace 的受控服務供應專案

本文說明如何使用合作夥伴中心為 Microsoft 商業 marketplace 建立受控服務供應專案。

若要發佈受控服務供應專案，您必須在雲端平臺獲得金級或銀級的 Microsoft 專長認證。 如果您尚未這麼做，請閱讀 [方案 a Managed Service 供應專案（適用于商業市場](./plan-managed-service-offer.md)）。 當您在合作夥伴中心中建立供應專案時，它將協助您準備所需的資產。

## <a name="create-a-new-offer"></a>建立新的供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左側導覽功能表中，選取 [**商業 Marketplace**  >  **總覽**]。
3. 在 [總覽] 索引標籤上，選取 [ **+ 新增供應** 專案  >  **受控服務**]。

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="說明左側導覽功能表。":::

4. 在 [ **新增供應** 專案] 對話方塊中，輸入 **供應專案識別碼**。 這是您帳戶中每個供應項目的唯一識別碼。 此識別碼會顯示在商用 marketplace 清單的 URL 中，且 Azure Resource Manager 範本（如果適用）。 例如，如果您在此方塊中輸入「測試供應專案-1」，供應專案的網址將會是 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` 。

    * 您帳戶中的每個供應專案都必須有唯一的供應專案識別碼。
    * 請一律使用小寫字母和數字。 識別碼可以包含連字號和底線，但不能有空格，且限制為 50 個字元。
    * 選取 [建立] 之後，即無法變更供應項目識別碼。

5. 輸入 **供應項目別名**。 這是用於合作夥伴中心內供應項目的名稱。 它不會顯示在線上商店中，不同于向客戶顯示的供應專案名稱。
6. 若要產生供應專案並繼續，請選取 [ **建立**]。

## <a name="configure-lead-management"></a>設定潛在客戶管理

將您的客戶關係管理 (CRM) system 與您的商業 marketplace 供應專案連接，讓您可以在客戶對您的諮詢服務感興趣時，收到客戶連絡人資訊。 您可以在建立供應專案期間或之後的任何時間修改此連接。 如需詳細指引，請參閱 [您的商業 marketplace 供應專案客戶潛在客戶](./partner-center-portal/commercial-marketplace-get-customer-leads.md)。

若要在合作夥伴中心中設定潛在客戶管理：

1. 在合作夥伴中心中，移至 [ **供應專案設定** ] 索引標籤。
2. 在 [ **客戶潛在客戶**] 下，選取 [連線 **]** 連結。
3. 在 [ **連接詳細資料** ] 對話方塊中，從清單中選取潛在客戶目的地。
4. 完成顯示的欄位。 如需詳細步驟，請參閱下列文章：

    * [設定供應項目以將潛在客戶傳送至 Azure 資料表](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [設定您的供應專案，以將潛在客戶傳送給 dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (先前為 Dynamics CRM Online) 
    * [設定您的供應專案以將潛在客戶傳送至 HTTPS 端點](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [設定供應項目以將潛在客戶傳送至 Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [設定您的供應項目，以便將潛在客戶傳送至 Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. 若要驗證您所提供的設定，請選取 [ **驗證] 連結**。
6. 當您已設定連接詳細資料時，請選取 **[連接]**。
7. 選取 [儲存草稿]。

當您在合作夥伴中心提交供應專案以供發行時，我們會驗證連線，並將測試潛在客戶傳送給您。 當您在供應專案上線前進行預覽時，請嘗試在預覽環境中自行購買供應專案，以測試您的潛在客戶連接。

> [!TIP]
> 確認潛在客戶目的地的連線保持在最新狀態，您就不會遺失任何潛在客戶。

## <a name="configure-offer-properties"></a>設定供應專案屬性

在合作夥伴中心中您供應專案的 [屬性] 頁面上，您將定義適用于您的供應專案和法律合約的類別。 這項資訊可確保您的受控服務會在線上商店上正確顯示，並提供給正確的客戶集合。

### <a name="select-a-category"></a>選取類別目錄

在 [ **類別**] 下，選取至少一個和最多五個類別，以便將您的供應專案分組到適當的商業 marketplace 搜尋區域中。

### <a name="provide-terms-and-conditions"></a>提供條款及條件

在 [ **法律**] 下，提供此供應專案的條款及條件。 客戶必須先接受這些條款，才能使用供應專案。 您也可以提供 URL，以便於找到條款及條件。

選取 [儲存草稿] 後再繼續。

## <a name="next-step"></a>後續步驟

* [設定受管理的服務供應專案清單](./create-managed-service-offer-listing.md)