---
title: 遠端監視資料存取控制 - Azure | Microsoft Docs
description: 此文章提供如何在遠端監視解決方案加速器中設定時間序列深入解析遙測總管之存取控制的相關資訊
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "65827251"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>設定時間序列深入解析遙測總管的存取控制

此文章提供如何在遠端監視解決方案加速器中設定時間序列深入解析總管之存取控制的相關資訊。 若要允許解決方案加速器的使用者存取時間序列深入解析總管，您需要將資料存取權授與每個使用者。

資料存取原則可授與下列權限：發出資料查詢、在環境中操作參考資料，以及共用與環境相關聯的已儲存查詢和檢視方塊。

## <a name="grant-data-access"></a>授與資料存取

依照下列步驟來授與使用者主體的資料存取權︰

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 找到您的時間序列深入解析環境。 在**搜尋**方塊中輸入**時間序列深入解析**。 選取搜尋結果中的 [時間序列環境]****。 

3. 從清單中選取 Time Series Insights 環境。

4. 選取 [資料存取原則]****，然後選取 [+新增]****。
    ![管時間序列深入解析來源 - 環境](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. 選取 [選取使用者]****。  搜尋使用者名稱或電子郵件地址，以找出您想要新增的使用者。 按一下 [選取]**** 以確認選取項目。 

    ![管理 Time Series Insights 來源 - 新增](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. 選取 [選取角色]****。 為使用者選擇適當的存取角色：
   - 如果您想要允許使用者變更參考資料，並與其他環境使用者共用已儲存的查詢和觀點，請選取 [**參與者**]。 
   - 否則，請選取 [**讀取器**] 以允許環境中的使用者查詢資料，並在環境中儲存個人（非共用）查詢。

     選取 **[確定]** 以確認角色選擇。

     ![管理 Time Series Insights 來源 - 選取使用者](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. 在 [選取使用者角色]**** 頁面中選取 [確定]****。

    ![管理 Time Series Insights 來源 - 選取角色](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. [資料存取原則]**** 頁面會列出使用者和每個使用者的角色。

    ![管理 Time Series Insights 來源 - 結果](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>後續步驟

在此文章中，您已了解如何在遠端監視解決方案加速器中授與時間序列深入解析總管的存取控制。

如需遠端監視解決方案加速器的詳細概念資訊，請參閱[遠端監視架構](iot-accelerators-remote-monitoring-sample-walkthrough.md)

如需關於自訂遠端監視解決方案的詳細資訊，請參閱[自訂和重新部署微服務](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->