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
ms.openlocfilehash: 0ea178655646f7f130476acaffc35c60181968ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87058711"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>從 Azure 入口網站存取 VMware Solution by CloudSimple portal

支援使用單一登入來存取 CloudSimple 入口網站。 登入 Azure 入口網站之後，您就可以存取 CloudSimple 入口網站，而不需要再次登入。 當您第一次存取 CloudSimple 入口網站時，系統會提示您授權 [CloudSimple Service 授權](#consent-to-cloudsimple-service-authorization-application) 應用程式。  授權是一次性動作。

## <a name="before-you-begin"></a>開始之前

具有內建 **擁有** 者和 **參與者** 角色的使用者可以存取 CloudSimple 入口網站。  角色必須在部署 CloudSimple 服務的資源群組上進行設定。  角色也可以在 CloudSimple 服務物件上進行設定。  如需有關檢查角色的詳細資訊，請參閱 [View role 指派](../role-based-access-control/check-access.md) 文章。 只有具備內建 **擁有** 者和 **參與者** 角色的使用者可以存取 CloudSimple 入口網站。  您必須在訂用帳戶上設定角色。  如需有關檢查角色的詳細資訊，請參閱 [View role 指派](../role-based-access-control/check-access.md) 文章。

如果您使用自訂角色，則角色在下應該會有下列任何一項作業 ```Actions``` 。  如需自訂角色的詳細資訊，請參閱 [Azure 自訂角色](../role-based-access-control/custom-roles.md)。  如果有任何作業屬於的一部分 ```NotActions``` ，使用者將無法存取 CloudSimple 入口網站。

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="access-the-cloudsimple-portal"></a>存取 CloudSimple 入口網站

1. 選取 [所有服務]。

2. 搜尋 **CloudSimple 服務**。

3. 選取您要在其上建立私人雲端的 CloudSimple 服務。

4. 在 [ **總覽** ] 頁面上，按一下 **[移至 CloudSimple 入口網站**]。  如果您是第一次從 Azure 入口網站存取 CloudSimple 入口網站，系統將會提示您授權 [CloudSimple Service 授權](#consent-to-cloudsimple-service-authorization-application) 應用程式。 

    ![啟動 CloudSimple 入口網站](media/launch-cloudsimple-portal.png)

> [!NOTE]
> 如果您選取私用雲端作業 (例如直接從 Azure 入口網站建立或擴充私人雲端) ，CloudSimple 入口網站會開啟至指定的頁面。

在 CloudSimple 入口網站的側邊功能表上，選取 [ **首頁** ]，以顯示私人雲端的相關摘要資訊。 顯示私人雲端的資源和容量，以及需要注意的警示和工作。 針對一般工作，按一下頁面頂端的 [已命名] 圖示。

![首頁](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>同意 CloudSimple 服務授權應用程式

第一次從 Azure 入口網站啟動 CloudSimple 入口網站時，需要您同意 CloudSimple Service 授權應用程式。  選取 [ **接受** ] 以授與要求的許可權，並存取 CloudSimple 入口網站。

![同意 CloudSimple 服務授權-系統管理員](media/cloudsimple-azure-consent.png)

如果您有全域管理員許可權，您可以同意您的組織。  選取 [代表貴組織同意]。

![同意 CloudSimple Service 授權-全域管理員](media/cloudsimple-azure-consent-global-admin.png)

如果您的許可權不允許存取 CloudSimple 入口網站，請洽詢您租使用者的全域管理員，以授與所需的許可權。  全域系統管理員可以代表貴組織同意。

![同意 CloudSimple 服務授權-需要系統管理員](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>接下來的步驟

* 瞭解如何 [建立私用雲端](./create-private-cloud.md)
* 瞭解如何 [設定私人雲端環境](quickstart-create-private-cloud.md)
