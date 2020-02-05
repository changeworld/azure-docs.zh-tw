---
title: 存取 Azure VMware 解決方案（AVS）-入口網站
description: 說明如何從 Azure 入口網站存取 Azure VMware 解決方案（AVS）
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015945"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>從 Azure 入口網站存取 Azure VMware 解決方案（AVS）

支援單一登入以存取 AVS 入口網站。 登入 Azure 入口網站之後，您就可以存取 AVS 入口網站，而不需要再次登入。 第一次存取 AVS 入口網站時，系統會提示您授權[Avs 服務授權](#consent-to-avs-service-authorization-application)應用程式。 授權是一次性的動作。

## <a name="before-you-begin"></a>開始之前

具有內建**擁有**者和**參與者**角色的使用者可以存取 AVS 入口網站。 您必須在部署 AVS 服務的資源群組上設定這些角色。 您也可以在 AVS 服務物件上設定角色。 如需有關檢查角色的詳細資訊，請參閱「[查看角色指派](https://docs.microsoft.com/azure/role-based-access-control/check-access)」一文。 只有具備內建**擁有**者和**參與者**角色的使用者才能存取 AVS 入口網站。 必須在訂用帳戶上設定角色。 如需有關檢查角色的詳細資訊，請參閱「[查看角色指派](https://docs.microsoft.com/azure/role-based-access-control/check-access)」一文。

如果您使用的是自訂角色，此角色在 ```Actions```之下應具有下列任何一項作業。  如需自訂角色的詳細資訊，請參閱[適用于 Azure 資源的自訂角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。 如果其中任何一項作業是 ```NotActions```的一部分，則使用者無法存取 AVS 入口網站。 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="access-the-avs-portal"></a>存取 AVS 入口網站

1. 選取 [所有服務]。

2. 搜尋 [ **AVS 服務**]。

3. 選取您要在其上建立私人雲端的 AVS 服務。

4. 在 [**總覽**] 頁面上，按一下 **[移至 AVS 入口網站**]。 如果您是第一次從 Azure 入口網站存取 AVS 入口網站，系統會提示您授權[Avs 服務授權](#consent-to-avs-service-authorization-application)應用程式。 

    ![啟動 AVS 入口網站](media/launch-cloudsimple-portal.png)

> [!NOTE]
> 如果您直接從 Azure 入口網站選取私人雲端作業（例如建立或擴充私人雲端），則 AVS 入口網站會開啟至指定的頁面。

在 AVS 入口網站中，選取側邊功能表上的 [**首頁**]，以顯示您的 AVS 私人雲端的摘要資訊。 您的 AVS 私人雲端的資源和容量會顯示，以及需要注意的警示和工作。 針對一般工作，按一下頁面頂端的 [已命名的圖示]。

![首頁](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>同意 AVS 服務授權應用程式

第一次從 Azure 入口網站啟動 AVS 入口網站時，必須同意您的 AVS 服務授權應用程式。 選取 [**接受**] 以授與要求的許可權並存取 AVS 入口網站。

![同意 AVS 服務授權-系統管理員](media/cloudsimple-azure-consent.png)

如果您有全域管理員許可權，您可以同意您的組織。 **代表您的組織**選取 [同意]。

![同意 AVS 服務授權-全域管理員](media/cloudsimple-azure-consent-global-admin.png)

如果您的許可權不允許存取 AVS 入口網站，請洽詢您租使用者的全域管理員，以授與所需的許可權。 全域管理員可以代表您的組織同意。

![同意 AVS 服務授權-需要系統管理員](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>後續步驟

* 瞭解如何[建立私用雲端](https://docs.azure.cloudsimple.com/create-private-cloud/)
* 瞭解如何[設定私用雲端環境](quickstart-create-private-cloud.md)
