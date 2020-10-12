---
title: 針對 Azure 中的共用映像問題進行疑難排解
description: 了解如何針對共用映像資源庫的問題進行疑難排解。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 06/15/2020
ms.author: cynthn
ms.reviewer: cynthn
ms.openlocfilehash: 3a206a7aabee9f75524ab4715afa30ec05c612bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328058"
---
# <a name="troubleshooting-shared-image-galleries-in-azure"></a>針對 Azure 中的共用映射資源庫進行疑難排解

如果您在共用映像資源庫、映像定義及映像版本上執行任何作業時遇到問題，請再次以偵錯模式執行失敗的命令。 藉由 `--debug` 使用 CLI 傳遞參數和 `-Debug` 使用 PowerShell 來切換參數來啟用偵錯工具模式。 一旦您找到錯誤，請遵循本文件來針對錯誤進行疑難排解。


## <a name="unable-to-create-a-shared-image-gallery"></a>無法建立共用映像資源庫

可能的原因：

資源庫名稱無效。**

資源庫名稱允許的字元為大寫或小寫字母、數字、點和句點。 資源庫名稱不能包含連字號。 變更資源庫名稱並再試一次。 

資源庫名稱在您的訂用帳戶內不是唯一的。**

挑選另一個資源庫名稱並再試一次。


## <a name="unable-to-create-an-image-definition"></a>無法建立映像定義 

可能的原因：

映像定義名稱無效。**

影像定義允許的字元為大寫或小寫字母、數位、點、破折號和句號。 變更映像定義名稱並再試一次。

未填入可供建立映像定義的必要屬性。**

名稱、發行者、供應項目、sku 和 OS 類型等都是必要屬性。 請確認是否正在傳遞所有屬性。

請確定映射定義的 **OSType**（Linux 或 Windows）和您用來建立映射版本的來源相同。 


## <a name="unable-to-create-an-image-version"></a>無法建立映像版本 

可能的原因：

映像版本名稱無效。**

映像版本允許的字元是數字及句點。 數字必須在 32 位元整數的範圍內。 格式： *MajorVersion. MinorVersion Patch*。 變更映像版本名稱並再試一次。

找不到從中建立映像版本的來源受控映像。** 

檢查來源映像是否存在且位於與映像版本相同的區域。

受控映像未完成佈建。**

請確定來源受控映像的佈建狀態是 [已成功]****。

*目的地區域清單不包含來源區域。*

目標區域清單必須包含映像版本的來源區域。 確定您已在您希望 Azure 將您的映像版本複寫至的目標區域清單中包含來源區域。

未完成複寫至所有目標區域。**

使用 **--expand ReplicationStatus** 旗標，以檢查是否已完成複寫到所有指定的目標區域。 如果尚未完成，最多等待 6 小時讓作業完成。 如果失敗，再次執行命令來建立和複寫映像版本。 如果映像版本要複寫至大量目標區域，請考慮分階段進行複寫。

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>無法建立 VM 或擴展集 

可能的原因：

嘗試建立 VM 或虛擬機器擴展集的使用者沒有映像版本的讀取權限。**

請聯絡訂用帳戶擁有者，並要求他們提供映射版本或父資源的讀取權限 (例如共用映射庫或映射定義) 透過 [azure 角色型存取控制 (AZURE RBAC) ](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)。 

找不到映像版本。**

確認您嘗試在其中建立 VM 或虛擬機器擴展集的區域包含在映像版本的目標區域清單中。 如果此區域已經在目標區域清單中，則確認複寫作業是否已完成。 您可使用 **-ReplicationStatus** 旗標，檢查是否已完成複寫到所有指定的目標區域。 

VM 或虛擬機器擴展集建立花費很長的時間。**

確認您嘗試用來建立 VM 或虛擬機器擴展集的映射版本 **OSType** ，與您用來建立映射版本的來源具有相同的 **OSType** 。 

## <a name="unable-to-share-resources"></a>無法共用資源

在訂用帳戶之間共用共用映射庫、映射定義和映射版本資源的功能，可使用 [azure 角色型存取控制 (AZURE RBAC) ](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)來啟用。 

## <a name="replication-is-slow"></a>複寫速度很慢

使用 **--expand ReplicationStatus** 旗標，以檢查是否已完成複寫到所有指定的目標區域。 如果尚未完成，最多等待 6 小時讓作業完成。 如果失敗，再次觸發命令來建立和複寫映像版本。 如果映像版本要複寫至大量目標區域，請考慮分階段進行複寫。

## <a name="azure-limits-and-quotas"></a>Azure 限制和配額 

[Azure 限制和配額](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)適用於所有的共用映像資源庫、映像定義和映像版本資源。 確定您在您訂用帳戶的限制範圍內。 


## <a name="next-steps"></a>後續步驟

深入了解[共用映像資源庫](./linux/shared-image-galleries.md)。
