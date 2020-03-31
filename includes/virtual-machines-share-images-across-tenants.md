---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903682"
---
但是，如果要大規模在 Azure 租戶外部共用圖像，則應創建應用註冊以方便共用。  使用應用註冊可以啟用更複雜的共用方案，例如： 

* 當一家公司收購另一家公司時管理共用映射，並且 Azure 基礎結構分佈在單獨的租戶中。 
* Azure 合作夥伴代表其客戶管理 Azure 基礎結構。 映射的自訂在合作夥伴租戶中完成，但基礎結構部署將在客戶的租戶中進行。 


## <a name="create-the-app-registration"></a>創建應用註冊

創建應用程式註冊，由兩個租戶用於共用圖像庫資源。
1. 在[Azure 門戶中打開應用註冊（預覽）。](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)    
1. 從頁面頂部的功能表中選擇 **"新註冊**"。
1. 在**名稱**中，鍵入*我的"庫圖應用程式*"。
1. 在 **"支援帳戶類型**"**中，選擇任何組織目錄中的帳戶和個人 Microsoft 帳戶**。
1. 在**重定向 URI**中*https://www.microsoft.com*，鍵入，然後選擇 **"註冊**"。 創建應用註冊後，將打開概覽頁面。
1. 在概述頁上，複製**應用程式（用戶端）ID**並保存以供以後使用。   
1. 選擇**證書&機密**，然後選擇 **"新用戶端機密**"。
1. 在 **"說明"** 中，鍵入*共用圖像庫跨租戶應用程式機密*。
1. 在 **"過期"** 中，保留**預設值 1 年**，然後選擇"**添加**"。
1. 複製機密的值並將其保存到安全的地方。 離開頁面後無法檢索它。


授予應用註冊許可權以使用共用圖像庫。
1. 在 Azure 門戶中，選擇要與其他租戶共用的共用映射庫。
1. **選擇"存取控制 "（IAM），** 在 **"添加角色指派"** 下選擇 *"添加*"。 
1. 在**角色**下，選擇 **"讀取器**"。
1. 在 **"分配對： " 下**，將其保留為**Azure AD 使用者、組或服務主體**。
1. 在 **"選擇**"下，鍵入*myGalleryApp，* 並在清單中顯示時將其選中。 完成之後，請選取 [儲存]****。


## <a name="give-tenant-2-access"></a>授予租戶 2 存取權限

通過使用瀏覽器請求登錄，為租戶 2 授予對應用程式的許可權。 將*\<租戶 2 ID>* 替換為要與共享映射庫的租戶的租戶 ID。 將*\<應用程式（用戶端）ID>* 替換為您創建的應用註冊的應用程式 ID。 完成替換後，將 URL 粘貼到瀏覽器中，然後按照登錄提示登錄到租戶 2。

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

在[Azure 門戶](https://portal.azure.com)中登錄租戶 2，並授予應用註冊存取權限到要創建 VM 的資源組。

1. 選擇資源組，然後選擇**存取控制 （IAM）。** 在 **"添加角色指派"** 下選擇 **"添加**"。 
1. 在**角色**下，鍵入 **"參與者**"。
1. 在 **"分配對： " 下**，將其保留為**Azure AD 使用者、組或服務主體**。
1. 在 **"選擇**"下鍵入*myGalleryApp，* 然後在清單中顯示時選擇它。 完成之後，請選取 [儲存]****。

> [!NOTE]
> 您需要等待映射版本完全完成正在構建和複製，然後才能使用相同的託管映射創建另一個映射版本。

