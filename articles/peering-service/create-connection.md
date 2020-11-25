---
title: 'Azure 對等互連服務：建立 '
description: 在本教學課程中，您將了解如何註冊 Azure 對等互連服務和字首。
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
Customer intent: With Azure Peering service enhancing the customer connectivity to Microsoft cloud services .
ms.openlocfilehash: f8f0ee71a8dd00498d868bb6dc11b3fa083df766
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021090"
---
# <a name="tutorial-create-a-peering-service-connection"></a>教學課程：建立對等互連服務連線

本教學課程說明如何建立對等互連服務資源，以及如何設定對等互連服務連線。 

1. 若要註冊對等互連服務連線，請選取 [建立資源] ****  > [對等互連服務] **** 。

 
    ![註冊對等互連服務](./media/peering-service-portal/peering-servicecreate.png)

2. 在 [建立對等互連服務連線] 頁面上，於 [基本] 索引標籤上輸入下列詳細資料。
 
3. 選取訂用帳戶和與訂用帳戶相關聯的資源群組。

    ![註冊對等互連服務基本索引標籤](./media/peering-service-portal/peering-servicebasics.png)

4. 輸入應註冊對等互連服務執行個體的 **名稱**。

5. 現在，選取頁面底部的 [下一步: 設定] 按鈕。 [設定] 頁面隨即出現。
## <a name="configure-the-peering-service-connection"></a>設定對等互連服務連線

1. 在 [設定] 頁面上，選取必須啟用對等互連服務的位置，方法是從 [對等互連服務位置] 下拉式清單中選取該相同位置。

1. 從 [對等互連服務提供者] 下拉式清單中選取提供者名稱，以選取必須從中取得對等互連服務的服務提供者。
 
1. 選取 [字首] 區段底部的 [建立新的字首]，文字方塊就會出現。 現在，輸入字首資源的名稱，以及與服務提供者相關聯的字首。

1. 選取 [字首索引鍵]，然後新增由提供者 (ISP 或 IXP) 提供給您的字首索引鍵。 此索引鍵會允許 MS 驗證已配置您的 IP 字首的字首和提供者。

    ![此螢幕擷取畫面顯示 [建立對等互連服務連線] 頁面的 [設定] 索引標籤，您可以在其中輸入前置詞索引鍵。](./media/peering-service-portal/peering-serviceconfiguration.png)

1. 選取頁面左下方的 [檢閱 + 建立] 按鈕。 在 [檢閱 + 建立] 頁面隨即出現，並且 Azure 會驗證設定。

 1. 當您看到顯示的 [驗證成功] 訊息時，請選取 [建立]。

> ![此螢幕擷取畫面顯示 [建立對等服務連線] 頁面的 [檢閱 + 建立] 索引標籤。](./media/peering-service-portal/peering-service-prefix.png)

1. 註冊對等互連服務連線之後，會對包含的字首執行額外的驗證。 您可以在資源名稱的 [字首] 區段下檢查驗證狀態。 如果驗證失敗，則會顯示下列其中一個錯誤訊息：

   - 對等互連服務字首無效，字首應該是有效的格式，僅支援 IPv4 字首。
   - 未收到來自對等互連服務提供者的字首。
   - 字首公告沒有有效的 BGP 社群，請連絡對等互連服務提供者。
   - 找不到備份路由，請連絡對等互連服務提供者。
   - 接收到的字首具有較長的 AS 路徑，請連絡對等互連服務提供者。
   - 接收到的字首路徑中具有私人 AS，請連絡對等互連服務提供者。

### <a name="add-or-remove-a-prefix"></a>新增或移除字首

選取 [字首] 頁面上的 [新增字首] 以新增字首。

選取所列字首旁的省略號 (...)，然後選取 [刪除] 選項。

### <a name="delete-a-peering-service-connection"></a>刪除對等互連服務連線

在 [所有資源] 頁面上，選取 [對等互連服務] 上的核取方塊，然後選取頁面頂端的 [刪除] 選項。
## <a name="next-steps"></a>後續步驟

- 若要深入了解對等互連服務連線，請參閱[對等互連服務連線](connection.md)。
- 若要深入了解對等互連服務連線遙測，請參閱[對等互連服務連線遙測](connection-telemetry.md)。
- 若要測量遙測，請參閱[測量連線遙測](measure-connection-telemetry.md)。
- 若要使用 Azure PowerShell 註冊連線，請參閱[註冊對等互連服務連線 - Azure PowerShell](powershell.md)。
- 若要使用 Azure CLI 註冊連線，請參閱[註冊對等互連服務連線 - Azure CLI](cli.md)。