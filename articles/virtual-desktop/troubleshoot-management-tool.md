---
title: Windows 虛擬桌面管理工具 - Azure
description: 如何解決 Windows 虛擬桌面管理工具的問題。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 11/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9d07ba42e83d9eec071ab047e9e1e92bac1f1411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127480"
---
# <a name="troubleshoot-the-windows-virtual-desktop-management-tool"></a>Windows 虛擬桌面管理工具疑難排解

本文介紹了在部署 Windows 虛擬桌面管理工具時可能出現的問題以及如何修復這些問題。

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>錯誤：管理工具服務已配置但自動設置失敗

當您成功為管理工具設置服務但自動設置失敗時，您將看到以下錯誤訊息：

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

這通常意味著以下兩件事之一：

- 使用者對其訂閱和租戶級別的全域管理員具有擁有者許可權，但他們無法登錄到 Azure。
- 使用者帳戶設置已啟用多重要素驗證。

修正方法：

1. 確保為 Azure 活動目錄使用者主體名稱創建的使用者具有"參與者"訂閱級別。
2. 使用 UPN 帳戶登錄以<portal.azure.com>以檢查帳戶設置並確保未啟動多重要素驗證。 如果打開，請將其關閉。
3. 訪問 Windows 虛擬桌面同意頁，並確保伺服器和用戶端應用已獲得同意。
4. 如果問題仍然存在並重新部署該工具，請查看["部署管理工具](manage-resources-using-ui.md)教程"。

## <a name="error-job-with-specified-id-already-exists"></a>錯誤：具有指定 ID 的作業已存在

如果使用者看到錯誤訊息"具有指定 ID 的作業已存在"，那是因為在部署範本時，他們在"應用程式名稱"參數中未提供唯一名稱。

要解決此問題，請重新部署已填充"應用程式名稱"參數的管理工具。

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>打開管理工具時延遲同意提示

部署管理工具時，同意提示可能不會立即打開。 這意味著 Azure Web 應用服務載入的時間比平常長。 載入完 Azure Web 後，應顯示提示。

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>使用者無法在美國東部區域部署管理工具

如果客戶將地區設定到美國東部，則他們無法部署管理工具。

要解決此問題，請將管理工具部署到其他區域。 在不同區域重新部署該工具不應影響使用者體驗。

## <a name="next-steps"></a>後續步驟

- 在[故障排除概述、回饋和支援](troubleshoot-set-up-overview.md)中瞭解有關升級跟蹤。
- 瞭解如何在[用於遠端桌面服務的 ARM 範本](https://github.com/Azure/RDS-Templates/blob/master/README.md)中報告 Windows 虛擬桌面工具的問題。
- 有關 Windows 虛擬桌面和升級跟蹤的故障排除概述，請參閱[故障排除概述、回饋和支援](troubleshoot-set-up-overview.md)。
- 要瞭解如何部署管理工具，請參閱[部署管理工具](manage-resources-using-ui.md)。