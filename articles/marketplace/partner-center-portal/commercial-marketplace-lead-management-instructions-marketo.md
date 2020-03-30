---
title: 在 Marketo 中配置潛在客戶管理 |Azure 應用商店
description: 為 Azure 市場客戶配置 Marketo 的潛在客戶管理。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 35d57d117f6308863965ffd789c0e28bedd0f301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281505"
---
# <a name="configure-lead-management-in-marketo"></a>在 Marketo 中設定潛在客戶管理

本文介紹如何設置 Marketo CRM 系統來處理市場產品/服務的銷售線索。

## <a name="set-up-your-marketo-crm-system"></a>設置市場 CRM 系統

1. 登入 Marketo。
2. 選取 [Design Studio]**** \(設計工作室\)。
    ![Marketo [Design Studio] \(設計工作室\)](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  選取 [New Form]**** \(新增表單\)。
    ![Marketo [New Form] \(新增表單\)](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  填入 [New Form] \(新增表單\) 中的必要欄位，然後選取 [Create]**** \(建立\)。
    ![Marketo 建立新表單](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  在 [Field Details] \(欄位詳細資料\) 上，選取 [Finish]**** \(完成\)。
    ![Marketo 完成表單](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  核准並關閉。

7. 在 *"市場線索支援"* 選項卡上，選擇 **"嵌入代碼**"。 

    ![內嵌程式碼](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo [Embed Code] \(內嵌程式碼\) 會顯示類似下列範例的程式碼。

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. 複製嵌入代碼表單中顯示的以下欄位的值。 您將使用這些值將產品/服務配置為在下一步中接收潛在客戶。 使用下一個範例作為從 Marketo [Embed Code] \(內嵌程式碼\) 範例取得所需識別碼的指南。

    - 伺服器 ID = **ys12**
    - 蒙奇金 ID = **123-PQR-789**
    - 表格 ID = **1179**

    **找出這些值的另一種方法**

    - 伺服器 ID 在 Marketo 實例的 URL 中找到，例如`serverID.marketo.com`""。
    - 通過"蒙奇金帳戶 ID"欄位中的管理員>Munchkin 功能表，或從 Marketo REST API 主機子域的第一部分獲取訂閱的蒙廷 ID： `https://{Munchkin ID}.mktorest.com`。
    - 表單 ID 是您在步驟 7 中創建的嵌入代碼表單的 ID，用於從我們的市場路由潛在客戶。

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>將您的優惠配置為向 Marketo 發送潛在客戶

準備好在發佈門戶中配置產品/服務的首席管理資訊時，請按照以下步驟操作： 

1. 導航到產品 **/服務"產品/服務"設置**頁面。
1. 在"潛在客戶管理"部分下選擇 **"連接**"。 

    ![鉛管理 - 連接](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. 在"連接詳細資訊"快顯視窗上，為潛在客戶目標選擇 **"市場市場**"。

    ![選擇潛在客戶目標](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. 提供**伺服器 ID**、**蒙奇帳戶 ID**和表單**ID**。

    >[!Note]
    >您必須完成配置產品/服務的其餘部分併發布它，然後才能收到產品/服務的潛在客戶。 

