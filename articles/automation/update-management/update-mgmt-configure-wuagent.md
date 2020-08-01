---
title: 設定 Windows Update 設定以進行 Azure 自動化更新管理
description: 本文說明如何設定 Windows Update 設定以使用 Azure 自動化更新管理。
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: aa4e49d0e79704b6fc9f19eb1b736b2358cf3a14
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450050"
---
# <a name="configure-windows-update-settings-for-azure-automation-update-management"></a>設定 Windows Update 設定以進行 Azure 自動化更新管理

Azure 自動化更新管理會倚賴 [Windows Update 用戶端](/windows/deployment/update/windows-update-overview)來下載並安裝 Windows 更新。 連線至 Windows Server Update Services (WSUS) 或 Windows Update 時，Windows Update 用戶端會使用特定設定。 其中有多項設定可透過下列項目來管理：

- 本機群組原則編輯器
- 群組原則
- PowerShell
- 直接編輯登錄

更新管理會依循多項指定用來控制 Windows Update 用戶端的設定。 如果您使用設定來啟用非 Windows 更新，「更新管理」也會管理這些更新。 如果您想要啟用在進行更新部署之前先下載更新的功能，更新部署可能較為快速、較有效率，且較不會超出維護期間。

如需在 Azure 訂用帳戶中設定 WSUS 並安全地將 Windows 虛擬機器保持在最新狀態的其他建議，請參閱[規劃您的部署以便在 Azure 中更新 Windows 虛擬機器](/azure/architecture/example-scenario/wsus/)。

## <a name="pre-download-updates"></a>預先下載更新

若要設定自動下載更新而不自動安裝的功能，您可以使用群組原則，[將自動更新設定](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)設為 3。 此設定可讓您在背景下載必要的更新，並在更新已可供安裝時通知您。 如此，「更新管理」仍會繼續控制排程，但允許在更新管理維護期間以外下載更新。 此行為可防止「更新管理」發生 `Maintenance window exceeded` 錯誤。

您可以在 PowerShell 中啟用這項設定：

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>設定重新開機設定

[藉由編輯登錄來設定自動更新](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)和[用來管理重新啟動的登錄機碼](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)中列出的登錄機碼可能會導致您的機器重新開機，即使您在 [更新部署] 設定中指定了 [一律不重新開機]，仍是如此。 請依據您的環境需求設定這些登錄機碼。

## <a name="enable-updates-for-other-microsoft-products"></a>啟用其他 Microsoft 產品的更新

根據預設，Windows Update 用戶端會設定為只提供 Windows 的更新。 如果您啟用 [當我更新 Windows 時提供其他 Microsoft 產品的更新]，則也會收到其他產品的更新，包括 Microsoft SQL Server 和其他 Microsoft 軟體的安全性修補程式。 如果您已下載並複製適用於 Windows 2016 和更新版本的最新[系統管理範本檔案](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)，即可設定此選項。

如果您的機器執行 Windows Server 2012 R2，就無法透過群組原則進行此設定。 在這些機器上請執行下列 PowerShell 命令：

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="make-wsus-configuration-settings"></a>進行 WSUS 組態設定

「更新管理」支援 WSUS 設定。 您可以使用[指定內部網路 Microsoft Update 服務位置](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)中的指示，指定掃描和下載更新的來源。 根據預設，Windows Update 用戶端會設定為從 Windows Update 下載更新。 當您將 WSUS 伺服器指定為機器的來源時，如果 WSUS 未核准更新，則更新部署會失敗。 

若要限定機器使用內部更新服務，請設定 [不要連線至任何 Windows Update 網際網路位置](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)。 

## <a name="next-steps"></a>後續步驟

遵循[管理 vm 的更新和修補程式](update-mgmt-manage-updates-for-vm.md)中的指示，來排程更新部署。
