---
title: 設定 Windows Update 設定以使用 Azure 更新管理
description: 本文說明您設定以使用 Azure 更新管理的 Windows Update 設定。
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226263"
---
# <a name="configure-windows-update-settings-for-update-management"></a>設定更新管理的 Windows Update 設定

Azure 更新管理依賴[Windows Update 用戶端](https://docs.microsoft.com//windows/deployment/update/windows-update-overview)來下載和安裝 Windows 更新。 連接到 Windows Server Update Services （WSUS）或 Windows Update 時，Windows Update 用戶端會使用特定設定。 其中許多設定都可以透過下列方式來管理：

- 本機群組原則編輯器
- 群組原則
- PowerShell
- 直接編輯登錄

更新管理會遵循指定用來控制 Windows Update 用戶端的許多設定。 如果您使用 [設定] 來啟用非 Windows 更新，更新管理也會管理這些更新。 如果您想要在進行更新部署之前啟用下載更新，則更新部署的速度會更快、更有效率，且較不可能超過維護時段。

## <a name="pre-download-updates"></a>預先下載更新

若要設定自動下載更新，但不要自動安裝，您可以使用群組原則將 [設定[自動更新](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)] 設定設為**3**。 此設定可讓您在背景下載必要的更新，並通知您更新已準備就緒可供安裝。 如此一來，更新管理仍然可以控制排程，但可在更新管理維護期間以外下載更新。 此行為可防止更新管理中的**維護視窗**發生錯誤。

您可以藉由執行下列命令，使用 PowerShell 來啟用此設定設定：

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>設定重新開機設定

藉[由編輯](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)登錄和[用來管理重新開機](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)的登錄機碼，在設定自動更新中所列出的登錄機碼，可能會導致您的電腦重新開機，即使您在**更新部署**設定中指定 [**永不重新開機**]。 將這些登錄機碼設定為最符合您的環境。

## <a name="enable-updates-for-other-microsoft-products"></a>啟用其他 Microsoft 產品的更新

根據預設，Windows Update 用戶端會設定為只提供 Windows 的更新。 如果您啟用 [**當我更新 Windows 時提供其他 microsoft 產品的更新**] 設定，您也會收到其他產品的更新，包括 Microsoft SQL Server 和其他 Microsoft 軟體的安全性修補程式。 如果您已下載並複製適用于 Windows 2016 和更新版本的最新系統[管理範本](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)檔案，則可以設定此選項。

如果您執行的是 Windows Server 2012 R2，則群組原則無法設定此設定。 在這些電腦上執行下列 PowerShell 命令。 更新管理符合此設定。

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 設定

更新管理支援 WSUS 設定。 以下列出您可以設定來使用更新管理的 WSUS 設定。

### <a name="intranet-microsoft-update-service-location"></a>內部網路 Microsoft 更新服務位置

您可以在 [[指定內部網路 Microsoft Update 服務位置](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)] 底下，指定掃描和下載更新的來源。 根據預設，Windows Update 用戶端會設定為從 Windows Update 下載更新。 當您將 WSUS 伺服器指定為電腦的來源時，如果 WSUS 未核准更新，則更新部署會失敗。 

若要將機器限制為僅限內部更新服務，請設定[[不要連線到任何 Windows Update 的網際網路位置]](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)。 

## <a name="next-steps"></a>後續步驟

設定 Windows Update 設定之後，您可以遵循[管理 Azure vm 的更新和修補程式](automation-tutorial-update-management.md)中的指示來排程更新部署。
