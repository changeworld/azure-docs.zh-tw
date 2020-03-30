---
title: 將 Windows 更新設置配置為使用 Azure 更新管理
description: 本文介紹配置為使用 Azure 更新管理的 Windows 更新設置。
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279022"
---
# <a name="configure-windows-update-settings-for-update-management"></a>為更新管理配置 Windows 更新設置

Azure 更新管理依賴于[Windows 更新用戶端](https://docs.microsoft.com//windows/deployment/update/windows-update-overview)來下載和安裝 Windows 更新。 Windows 更新用戶端在連接到 Windows 伺服器更新服務 （WSUS） 或 Windows 更新時使用的特定設置。 許多這些設置可以使用以下方式進行管理：

- 本機群組原則編輯器
- 群組原則
- PowerShell
- 直接編輯註冊表

更新管理尊重為控制 Windows 更新用戶端而指定的許多設置。 如果使用設置啟用非 Windows 更新，更新管理也將管理這些更新。 如果要在更新部署發生之前啟用更新下載，更新部署可以更快、更高效，並且不太可能超過維護視窗。

## <a name="pre-download-updates"></a>預下載更新

要配置自動下載更新但不自動安裝更新，可以使用群組原則將["配置自動更新"設置](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)設置為**3**。 此設置允許在後臺下載所需的更新，並通知您更新已準備好安裝。 這樣，更新管理仍控制著計畫，但更新可以在更新管理維護視窗之外下載。 此行為可防止更新管理中**維護視窗超過**錯誤。

您可以通過運行以下命令，使用 PowerShell 啟用此設置：

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>配置重新開機設置

通過編輯[用於管理重新開機的註冊表和登錄機碼](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)[在"配置自動更新"](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)中列出的登錄機碼可能會導致電腦重新開機，即使您在 **"更新部署"** 設置中指定 **"從不重新開機**"。 配置這些登錄機碼以最適合您的環境。

## <a name="enable-updates-for-other-microsoft-products"></a>啟用其他 Microsoft 產品的更新

預設情況下，Windows 更新用戶端配置為僅為 Windows 提供更新。 如果在更新 Windows 設置**時啟用其他 Microsoft 產品的"給我更新**"，您還會收到其他產品的更新，包括 Microsoft SQL Server 和其他 Microsoft 軟體的安全修補程式。 如果您已下載並複製適用于 Windows 2016 及更高版本的最新[管理範本檔](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)，則可以配置此選項。

如果您正在運行 Windows Server 2012 R2，則群組原則無法配置此設置。 在這些電腦上運行以下 PowerShell 命令。 更新管理符合此設置。

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 配置設置

更新管理支援 WSUS 設置。 下面列出了可用於使用更新管理可以配置的 WSUS 設置。

### <a name="intranet-microsoft-update-service-location"></a>內聯網微軟更新服務位置

您可以在["指定內聯網微軟更新服務位置](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)"下指定用於掃描和下載更新的源。 預設情況下，Windows 更新用戶端配置為從 Windows 更新下載更新。 當您指定 WSUS 伺服器作為電腦的源時，如果更新在 WSUS 中未批准，則更新部署將失敗。 

要將電腦限制為僅該內部更新服務，請配置["不要連接到任何 Windows 更新 Internet 位置](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)"。 

## <a name="next-steps"></a>後續步驟

配置 Windows 更新設置後，可以按照[Azure VM 的管理更新和修補程式](automation-tutorial-update-management.md)中的說明來安排更新部署。
