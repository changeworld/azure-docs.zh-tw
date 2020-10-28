---
title: 如何在 Microsoft 商業市場中建立諮詢服務供應專案
description: 瞭解如何使用 Microsoft 合作夥伴中心中的商業 Marketplace 方案，為 Microsoft AppSource 或 Azure Marketplace 建立新的諮詢服務供應專案。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754342"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>如何在商業市場中建立諮詢服務供應專案

本文說明如何使用合作夥伴中心為 Microsoft 商業 marketplace 建立諮詢服務供應專案。 

## <a name="before-you-begin"></a>開始之前

若要發佈諮詢服務供應專案，您必須符合特定的資格需求，以示範您現場的專業知識。 如果您尚未這麼做，請參閱 [方案 a 諮詢服務供應專案（適用于商業市場](./plan-consulting-service-offer.md)）。 它描述諮詢服務的必要條件，以及當您使用合作夥伴中心建立供應專案時所需的資產。

## <a name="create-a-new-consulting-service-offer"></a>建立新的諮詢服務供應項目

1. 登入[合作夥伴中心](https://partner.microsoft.com/dashboard/home)。
2.  在左側導覽功能表中，選取 [ **商業 Marketplace**  >  **總覽** ]。
3.  在 [總覽] 索引標籤上，選取 [ **+ 新增供應** 專案  >  **諮詢服務** ]。

    ![說明左側導覽功能表。](./media/new-offer-consulting-service.png)

4. 在 [ **新增供應** 專案] 對話方塊中，輸入 **供應專案識別碼** 。 此識別碼會顯示在商業 marketplace 清單的 URL 中。 例如，如果您在此方塊中輸入「測試供應專案-1」，供應專案的網址將會是 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` 。

    * 您帳戶中的每個供應專案都必須有唯一的供應專案識別碼。
    * 請一律使用小寫字母和數字。 供應專案識別碼可以包含連字號和底線，但不能包含空格，且限制為50個字元。
    * 選取 [ **建立** ] 之後，就無法變更供應專案識別碼。

5. 輸入 **供應項目別名** 。 這是用於合作夥伴中心內供應項目的名稱。 它不會顯示在線上商店中，不同于向客戶顯示的供應專案名稱。
6. 若要產生供應專案並繼續，請選取 [ **建立** ]。

## <a name="configure-lead-management"></a>設定潛在客戶管理

將您的客戶關係管理 (CRM) system 與您的商業 marketplace 供應專案連接，讓您可以在客戶對您的諮詢服務感興趣時，收到客戶連絡人資訊。 您可以在建立供應專案期間或之後的任何時間修改此連接。 如需詳細指引，請參閱 [您的商業 marketplace 供應專案客戶潛在客戶](./partner-center-portal/commercial-marketplace-get-customer-leads.md)。

若要在合作夥伴中心中設定潛在客戶管理：

1.  在合作夥伴中心中，移至 [ **供應專案設定** ] 索引標籤。
2.  在 [ **客戶潛在客戶** ] 下，選取 [連線 **]** 連結。
3.  在 [ **連接詳細資料** ] 對話方塊中，從清單中選取潛在客戶目的地。
4.  完成顯示的欄位。 如需詳細步驟，請參閱下列文章：

    * [設定供應項目以將潛在客戶傳送至 Azure 資料表](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [設定您的供應專案，以將潛在客戶傳送給 dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (先前為 Dynamics CRM Online) 
    * [設定您的供應專案以將潛在客戶傳送至 HTTPS 端點](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [設定供應項目以將潛在客戶傳送至 Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [設定您的供應項目，以便將潛在客戶傳送至 Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  若要驗證您所提供的設定，請選取 [ **驗證] 連結** 。
6.  當您已設定連接詳細資料時，請選取 **[連接]** 。
7.  選取 [儲存草稿]。

當您在合作夥伴中心提交供應專案以供發行時，我們會驗證連線，並將測試潛在客戶傳送給您。 當您在供應專案上線前進行預覽時，請嘗試在預覽環境中自行購買供應專案，以測試您的潛在客戶連接。

> [!TIP]
> 確認潛在客戶目的地的連線保持在最新狀態，您就不會遺失任何潛在客戶。

## <a name="next-steps"></a>後續步驟

* [如何設定您的諮詢服務供應專案屬性](./create-consulting-service-offer-properties.md)