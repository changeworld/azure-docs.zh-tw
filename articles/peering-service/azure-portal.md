---
title: 註冊 Azure 對等互連服務-Azure 入口網站
description: 瞭解如何使用 Azure 入口網站來註冊 Azure 對等互連服務
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 596f06d6dc3ad33f2794dfe0e3ed7822a4820adb
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399010"
---
# <a name="register-peering-service-by-using-the-azure-portal"></a>使用 Azure 入口網站註冊對等互連服務

Azure 對等互連服務是一種網路服務，可加強客戶與 Microsoft 雲端服務的連線能力，例如 Microsoft 365、Dynamics 365、軟體即服務 (SaaS) services、Azure，或任何可透過公用網際網路存取的 Microsoft 服務。

在本文中，您將瞭解如何使用 Azure 入口網站來註冊對等互連服務連接。

如果您沒有 Azure 訂用帳戶，請立即建立[帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> 

## <a name="prerequisites"></a>必要條件

您必須滿足以下條件：

### <a name="azure-account"></a>Azure 帳戶

您必須具有有效且作用中的 Microsoft Azure 帳戶。 需要此帳戶才能設定對等互連服務連線。 對等互連服務是 Azure 訂用帳戶內的資源。 

### <a name="connectivity-provider"></a>連線提供者

您可以使用網際網路服務提供者或網際網路交換合作夥伴來取得對等互連服務，以將您的網路與 Microsoft 網路連線。

請確定連線 [提供者](location-partners.md) 已與 Microsoft 合作。



## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

從瀏覽器移至 Azure 入口網站，然後使用您的 Azure 帳戶登入。

## <a name="register-a-peering-service-connection"></a>註冊對等互連服務連線

1. 若要註冊對等互連服務連線，請選取 [建立資源] ****  > [對等互連服務] **** 。

    ![註冊對等互連服務](./media/peering-service-portal/peering-servicecreate.png)
1. 在 [建立對等互連服務連線] 頁面上，於 [基本] 索引標籤上輸入下列詳細資料。

 
1. 選取訂用帳戶和與訂用帳戶相關聯的資源群組。

   ![註冊對等基本索引標籤](./media/peering-service-portal/peering-servicebasics.png)

1. 輸入應註冊對等互連服務執行個體的**名稱**。
 
1. 現在，選取頁面底部的 [下一步: 設定] 按鈕。 [設定] 頁面隨即出現。

## <a name="configure-the-peering-service-connection"></a>設定對等互連服務連線

1. 在 [設定] 頁面上，選取必須啟用對等互連服務的位置，方法是從 [對等互連服務位置] 下拉式清單中選取該相同位置。

1. 從 [對等互連服務提供者] 下拉式清單中選取提供者名稱，以選取必須從中取得對等互連服務的服務提供者。
 
1. 選取 [字首] 區段底部的 [建立新的字首]，文字方塊就會出現。 現在，輸入字首資源的名稱，以及與服務提供者相關聯的字首。

1. 選取 [字首索引鍵]，然後新增由提供者 (ISP 或 IXP) 提供給您的字首索引鍵。 此索引鍵會允許 MS 驗證已配置您的 IP 字首的字首和提供者。
   > ![[註冊對等互連服務設定] 索引標籤](./media/peering-service-portal/peering-serviceconfiguration.png)

1. 選取頁面左下方的 [檢閱 + 建立] 按鈕。 在 [檢閱 + 建立] 頁面隨即出現，並且 Azure 會驗證設定。
    

1. 當您看到顯示的 [驗證成功] 訊息時，請選取 [建立]。

   > ![[註冊對等互連服務設定] 索引標籤](./media/peering-service-portal/peering-service-prefix.png)


1. 註冊對等互連服務連線之後，會對包含的字首執行額外的驗證。 您可以在資源名稱的 [字首] 區段下檢查驗證狀態。 如果驗證失敗，則會顯示下列其中一個錯誤訊息：

   - 對等互連服務字首無效，字首應該是有效的格式，僅支援 IPv4 字首。
   - 未收到來自對等互連服務提供者的字首。
   - 前置詞宣告沒有有效的 BGP 團體、contact 對等互連服務提供者。
   - 找不到備份路由，請聯絡對等互連服務提供者。
   - 收到的首碼會以較長的路徑、連絡人對等互連服務提供者。
   - 以私用形式收到的首碼，如同路徑中的，請聯絡對等互連服務提供者。

### <a name="add-or-remove-a-prefix"></a>新增或移除字首

選取 [字首] 頁面上的 [新增字首] 以新增字首。

選取所列字首旁的省略號 (...)，然後選取 [刪除] 選項。

### <a name="delete-a-peering-service-connection"></a>刪除對等互連服務連線

在 [所有資源] 頁面上，選取 [對等互連服務] 上的核取方塊，然後選取頁面頂端的 [刪除] 選項。

> [!NOTE]
> 您無法修改現有的前置詞。
>

## <a name="next-steps"></a>後續步驟

- 若要深入了解對等互連服務連線，請參閱[對等互連服務連線](connection.md)。
- 若要深入了解對等互連服務連線遙測，請參閱[對等互連服務連線遙測](connection-telemetry.md)。
- 若要測量遙測，請參閱[測量連線遙測](measure-connection-telemetry.md)。
- 若要使用 Azure PowerShell 註冊連線，請參閱[註冊對等互連服務連線 - Azure PowerShell](powershell.md)。
- 若要使用 Azure CLI 註冊連線，請參閱[註冊對等互連服務連線 - Azure CLI](cli.md)。
