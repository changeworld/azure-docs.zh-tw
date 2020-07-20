---
title: 包含檔案
description: 包含檔案
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67325790"
---
1. 登錄到 Azure[門戶]。

2. 按一下 **"創建資源**"。

3. 在搜索框中，鍵入**Web 應用**。
    
4. 在結果清單中，從應用商店中選擇**Web 應用**。

5. 選擇**訂閱****和資源組**（選擇現有資源組_或_創建新資源組（使用與應用相同的名稱）。

6. 選擇 Web 應用的唯一**名稱**。

7. 選擇預設**的"發佈"** 選項作為**代碼**。

8. 在**運行時堆疊**中，您需要在**ASP.NET**或**節點**下選擇一個版本。 如果要構建 .NET 後端，請在"ASP.NET"下選擇一個版本。 否則，如果您的目標是基於節點的應用程式，請從 Node 中選擇一個版本。

9. 選擇正確的**作業系統**，Linux 或 Windows。 

10. 選擇要部署此應用**的區域**。 

11. 選擇適當的**應用服務方案**並點擊 **"審閱"並創建**。 

12. 在 [資源群組]**** 下，選取現有的資源群組或__ 建立新的資源群組 (使用與應用程式相同的名稱)。

13. 按一下 **[建立]**。 等候幾分鐘的時間來成功地部署服務，然後再繼續進行。 監看狀態更新入口網站標頭中的 [通知] \(鈴鐺) 圖示。

14. 部署完成後，按一下 **"部署詳細資訊**"部分，然後按一下 Microsoft 類型**的資源.Web/網站**。 它將引導您到您剛剛創建的應用服務 Web 應用。 

15. 按一下 **"設置"** 下的 **"配置**"邊欄選項卡，在 **"應用程式設定"** 中按一下"**新建應用程式"設置**按鈕。

16. 在 **"添加/編輯應用程式設定"** 頁中，將 **"名稱**"作為**MobileAppsManagement_EXTENSION_VERSION**和"值"作為**最新**值，然後點擊"確定"。

您都設置為使用此新創建的應用服務 Web 應用作為移動應用。

<!-- URLs. -->
[Azure 門戶]: https://portal.azure.com/