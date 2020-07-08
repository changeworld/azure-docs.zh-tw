---
title: 存取 Azure VMware Solution by CloudSimple-入口網站
description: 說明如何從 Azure 入口網站經由 CloudSimple 入口網站存取 VMware 解決方案
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 91d3dd2eee6f771df23b72eae7a33122c42f1690
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81869339"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>從 [Azure 入口網站] 的 [CloudSimple] 入口網站存取 VMware 解決方案

支援單一登入以存取 CloudSimple 入口網站。 登入 Azure 入口網站之後，您就可以存取 CloudSimple 入口網站，而不需要再次登入。 您第一次存取 CloudSimple 入口網站時，系統會提示您授權[CloudSimple 服務授權](#consent-to-cloudsimple-service-authorization-application)應用程式。  授權是一次性的動作。

## <a name="before-you-begin"></a>開始之前

具有內建**擁有**者和**參與者**角色的使用者可以存取 CloudSimple 入口網站。  您必須在部署 CloudSimple 服務的資源群組上設定角色。  角色也可以在 CloudSimple 服務物件上進行設定。  如需檢查角色的詳細資訊，請參閱[View role 指派](https://docs.microsoft.com/azure/role-based-access-control/check-access)一文。 只有具備內建**擁有**者和**參與者**角色的使用者可以存取 CloudSimple 入口網站。  必須在訂用帳戶上設定角色。  如需檢查角色的詳細資訊，請參閱[View role 指派](https://docs.microsoft.com/azure/role-based-access-control/check-access)一文。

如果您使用自訂角色，此角色應該會有下列任何一項作業 ```Actions``` 。  如需自訂角色的詳細資訊，請參閱[適用于 Azure 資源的自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。  如果有任何作業屬於的一部分 ```NotActions``` ，使用者就無法存取 CloudSimple 入口網站。

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

2. 搜尋**CloudSimple Services**。

3. 選取您要在其上建立私人雲端的 CloudSimple 服務。

4. 在 [**總覽**] 頁面上，按一下 **[移至 CloudSimple 入口網站**]。  如果您是第一次從 Azure 入口網站存取 CloudSimple 入口網站，系統會提示您授權[CloudSimple 服務授權](#consent-to-cloudsimple-service-authorization-application)應用程式。 

    ![啟動 CloudSimple 入口網站](media/launch-cloudsimple-portal.png)

> [!NOTE]
> 如果您直接從 Azure 入口網站選取私人雲端作業（例如建立或擴充私人雲端），CloudSimple 入口網站會開啟至指定的頁面。

在 CloudSimple 入口網站中，選取側邊功能表上的 [**首頁**]，以顯示私人雲端的摘要資訊。 會顯示私人雲端的資源和容量，以及需要注意的警示和工作。 針對一般工作，按一下頁面頂端的 [已命名的圖示]。

![首頁](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>同意 CloudSimple 服務授權應用程式

第一次從 Azure 入口網站啟動 CloudSimple 入口網站時，需要您同意 CloudSimple 服務授權應用程式。  選取 [**接受**] 以授與所要求的許可權，並存取 CloudSimple 入口網站。

![同意 CloudSimple 服務授權-系統管理員](media/cloudsimple-azure-consent.png)

如果您有全域管理員許可權，您可以同意您的組織。  選取 [代表貴組織同意]****。

![同意 CloudSimple 服務授權-全域管理員](media/cloudsimple-azure-consent-global-admin.png)

如果您的許可權不允許存取 CloudSimple 入口網站，請洽詢您租使用者的全域管理員，以授與所需的許可權。  全域管理員可以代表您的組織同意。

![同意 CloudSimple 服務授權-需要系統管理員](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>後續步驟

* 瞭解如何[建立私用雲端](https://docs.microsoft.com/azure/vmware-cloudsimple/create-private-cloud/)
* 瞭解如何[設定私用雲端環境](quickstart-create-private-cloud.md)
