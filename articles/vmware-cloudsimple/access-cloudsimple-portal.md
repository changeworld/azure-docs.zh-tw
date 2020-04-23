---
title: 透過雲端簡單存取 Azure VMware 解決方案 - 門戶
description: 說明如何從 Azure 入口網站經由 CloudSimple 入口網站存取 VMware 解決方案
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 91d3dd2eee6f771df23b72eae7a33122c42f1690
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869339"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>透過雲端簡單門戶從 Azure 入口存取 VMware 解決方案

訪問雲簡單門戶時,支援單一登錄。 登錄到 Azure 門戶後,無需再次登錄即可訪問 CloudSimple 門戶。 首次訪問 CloudSimple 門戶時,系統會提示您授權[雲端簡單服務授權](#consent-to-cloudsimple-service-authorization-application)應用程式。  授權是一次性操作。

## <a name="before-you-begin"></a>開始之前

具有內置**擁有者**和**參與者**角色的用戶可以訪問 CloudSimple 門戶。  必須在部署 CloudSimple 服務的資源組上配置角色。  角色也可以在 CloudSimple 服務物件上配置。  有關檢查角色的詳細資訊,請參閱[查看角色分配](https://docs.microsoft.com/azure/role-based-access-control/check-access)一文。 只有具有內置**擁有者**和**參與者**角色的用戶可以訪問 CloudSimple 門戶。  必須在訂閱上配置角色。  有關檢查角色的詳細資訊,請參閱[查看角色分配](https://docs.microsoft.com/azure/role-based-access-control/check-access)一文。

如果使用自定義角色,該角色應在```Actions```下 具有以下任何操作。  有關自訂角色的詳細資訊,請參閱[Azure 資源的自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。  如果任何操作是```NotActions```中的一部分,則用戶無法訪問 CloudSimple 門戶。

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

1. 選取 [所有服務]  。

2. 搜尋**雲簡單服務**。

3. 選擇要在其中創建私有雲的雲簡單服務。

4. 在 **「概述」** 頁上,按一下「**轉到雲簡單門戶**」。  如果您是首次從 Azure 門戶訪問 CloudSimple 門戶,系統將提示您授權[雲端服務授權](#consent-to-cloudsimple-service-authorization-application)應用程式。 

    ![啟動雲簡單門戶](media/launch-cloudsimple-portal.png)

> [!NOTE]
> 如果直接從 Azure 門戶選擇私有雲操作(如創建或擴展私有雲),CloudSimple 門戶將打開到指示的頁面。

在 CloudSimple 門戶中,在側功能表上選擇 **「主頁」** 以顯示有關私有雲的摘要資訊。 將顯示私有雲的資源和容量,以及需要注意的警報和任務。 對於常見任務,請單擊頁面頂部的命名圖示。

![首頁](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>同意雲端簡單服務授權應用程式

首次從 Azure 門戶啟動雲簡單門戶需要獲得雲端簡單服務授權應用程式的同意。  選擇 **「接受**」以授予請求的權限並造訪雲端簡單門戶。

![同意雲端簡單服務授權 - 管理員](media/cloudsimple-azure-consent.png)

如果您具有全域管理員許可權,則可以同意您的組織。  選取 [代表貴組織同意]****。

![同意雲端簡單服務授權 - 全球管理員](media/cloudsimple-azure-consent-global-admin.png)

如果您的許可權不允許訪問 CloudSimple 門戶,請與租戶的全域管理員聯繫以授予所需的許可權。  全域管理員可以代表您的組織同意。

![同意雲端簡單服務授權 - 需要管理員](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>後續步驟

* 瞭解如何[建立私有雲](https://docs.microsoft.com/azure/vmware-cloudsimple/create-private-cloud/)
* 瞭解如何[設定私有雲端環境](quickstart-create-private-cloud.md)
