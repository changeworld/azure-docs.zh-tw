---
title: 配置銷售隊伍的潛在客戶管理 |Azure 應用商店
description: 為 Azure 應用商店客戶配置銷售隊伍上的潛在客戶管理。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 1720026b4beff941b02a60cd1c755a043d66bdb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281488"
---
# <a name="configure-lead-management-for-salesforce"></a>針對 Salesforce 設定潛在客戶管理

本文介紹如何設置 Salesforce 系統來處理市場產品/服務的銷售線索。

## <a name="set-up-your-salesforce-system"></a>設置銷售隊伍系統

1. 登入 Salesforce。
2. 如果您使用的是 Salesforce 照明體驗。
    1. 從 Salesforce 主頁中選擇 **"設置**"。
    ![Salesforce 設定](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. 從"設置"頁面，通過左側導航導航到**平臺工具->功能設置->行銷->網路到潛在客戶**。
    ![Salesforce Web 到潛在客戶](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. 如果您使用的是 Salesforce 經典體驗：
    1. 從 Salesforce 主頁中選擇 **"設置**"。
    ![Salesforce 經典設置](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. 從"設置"頁面，通過左側導航導航到**生成->自訂>潛在客戶>Web 到潛在客戶**。
    ![Salesforce 經典 Web 到領導](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

其餘的說明是相同的，無論您使用的 Salesforce 體驗如何。

4. 在 **"Web 到潛在客戶設置"頁上**，選擇"**創建 Web 到潛在客戶表單"** 按鈕。
5. 在 [Web 到潛在客戶設定]**** 中，選取 [建立 Web 到潛在客戶表單]****。
    ![銷售隊伍 - 網路到線索設置](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. 在 **"創建 Web 到潛在客戶表單**"表單上`the Include reCAPTCHA in HTML`，請確保未選中設置，然後選擇"**生成**"。 
    ![銷售隊伍 - 創建網路到線索表單](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. 將顯示一些 HTML 文本。 搜索文本"oid"並從 HTML 文本複製**oid 值**（僅在引號之間的文本），並保存它。 您將在發佈門戶上的 **"組織識別碼"** 欄位中粘貼此值。
    ![銷售隊伍 - 創建網路到線索表單](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. 所選**已完成**。

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>將產品/服務配置為向 Salesforce 發送潛在客戶

準備好在發佈門戶中配置產品/服務的首席管理資訊時，請按照以下步驟操作：

1. 導航到產品 **/服務"產品/服務"設置**頁面。
1. 在"潛在客戶管理"部分下選擇 **"連接**"。
    ![鉛管理 - 連接](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. 在"連接詳細資訊"快顯視窗上，選擇 **"潛在客戶目標****"的 Salesforce，** 然後`oid`按照前面的步驟粘貼到 **"組織"識別碼**欄位，從 Web 到潛在客戶表單。

1. 選取 [儲存]****。 

    >[!Note]
    >您必須完成配置產品/服務的其餘部分併發布它，然後才能收到產品/服務的潛在客戶。

    ![連接詳細資訊 - 選擇潛在客戶目標](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![連接詳細資訊 - 選擇潛在客戶目標](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)
