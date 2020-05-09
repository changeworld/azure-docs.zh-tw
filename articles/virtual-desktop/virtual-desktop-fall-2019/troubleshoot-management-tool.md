---
title: Windows 虛擬桌面管理工具-Azure
description: 如何針對 Windows 虛擬桌面管理工具的問題進行疑難排解。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb9206d2c2c15c036b0cf1184b79ed126966eb3b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614794"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Windows 虛擬桌面管理工具疑難排解

>[!IMPORTANT]
>此內容適用于不支援 Azure Resource Manager Windows 虛擬桌面物件的秋季2019版。

本文說明部署 Windows 虛擬桌面管理工具時可能發生的問題，以及如何加以修正。

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>錯誤：已設定管理工具服務但自動化安裝失敗

當您成功設定管理工具的服務，但自動安裝失敗時，您會看到下列錯誤訊息：

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

這通常表示下列兩個專案的其中一項：

- 使用者具有其訂用帳戶的擁有者許可權，以及在租使用者層級的全域管理員，但他們無法登入 Azure。
- 使用者的帳戶設定已啟用多重要素驗證。

修正方法：

1. 請確定您為 Azure Active Directory 使用者主體名稱建立的使用者具有「參與者」訂用帳戶層級。
2. 使用 UPN 帳戶登入 <portal.azure.com> 以檢查帳戶設定，並確認未開啟多重要素驗證。 如果已開啟，請將它關閉。
3. 請造訪 Windows 虛擬桌面同意頁面，並確定伺服器和用戶端應用程式均已同意。
4. 如果問題持續發生，請參閱[部署管理工具](manage-resources-using-ui.md)教學課程，然後重新部署工具。

## <a name="error-job-with-specified-id-already-exists"></a>錯誤：具有指定識別碼的作業已經存在

如果您的使用者看到錯誤訊息「具有指定識別碼的工作已經存在」，這是因為在部署範本時，它們並未在「應用程式名稱」參數中提供唯一的名稱。

若要修正此問題，請使用已填入的「應用程式名稱」參數重新部署管理工具。

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>開啟管理工具時的延遲同意提示

當您部署管理工具時，同意提示可能無法立即開啟。 這表示 Azure Web 應用程式服務所花費的時間比平常長，無法載入。 提示應該會在 Azure Web 完成載入後出現。

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>使用者無法在美國東部區域部署管理工具

如果客戶將區域設定為美國東部，他們就無法部署管理工具。

若要修正此問題，請將管理工具部署在不同的區域中。 在不同區域中重新部署工具不應該影響使用者體驗。

## <a name="next-steps"></a>後續步驟

- 在[疑難排解總覽、意見反應和支援](troubleshoot-set-up-overview-2019.md)中深入瞭解呈報追蹤。
- 瞭解如何在[適用于遠端桌面服務的 ARM 範本](https://github.com/Azure/RDS-Templates/blob/master/README.md)Windows 虛擬桌面工具報告問題。
- 若要瞭解如何部署管理工具，請參閱[部署管理工具](manage-resources-using-ui.md)。