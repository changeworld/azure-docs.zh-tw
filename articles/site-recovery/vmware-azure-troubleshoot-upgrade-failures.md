---
title: 微軟 Azure 網站恢復提供程式的故障排除升級
description: 解決升級 Microsoft Azure 網站恢復提供程式時發生的常見問題。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: b59f933fedd5f1d3ed3f7972b1a1fe653df31be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893902"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>對 Microsoft Azure Site Recovery Provider 升級失敗進行疑難排解

本文可協助您解決在 Microsoft Azure Site Recovery 提供者期間升級可能會導致失敗的問題。

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>在升級時未報告已安裝最新的 Site Recovery 提供者

在升級 Microsoft Azure Site Recovery Provider (DRA) 時，整合安裝程式升級會失敗並發出錯誤訊息：

不支援升級，因為已安裝軟體的更高版本。

若要升級，請使用下列步驟：

1. 下載 Microsoft Azure Site Recovery 整合安裝：
   1. 在 [Azure Site Recovery 中的服務更新](service-updates-how-to.md#links-to-currently-supported-update-rollups)一文的＜目前支援的更新彙總套件連結＞小節中，選取您要升級的提供者。
   2. 在 [彙總] 頁面上，找出 [更新資訊]**** 區段，並下載適用於 Microsoft Azure Site Recovery 整合安裝更新彙總套件。

2. 開啟命令提示字元並瀏覽至您下載整合安裝檔案的資料夾。 使用下列命令將安裝檔從下載項目中解壓縮：MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;已解壓縮檔案的資料夾路徑&gt;。
    
    範例命令：

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. 在命令提示字元中，瀏覽至您解壓縮檔案的資料夾並執行下列安裝命令：
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART  UCX_SERVER_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. 返回您下載整合安裝的資料夾，然後執行 MicrosoftAzureSiteRecoveryUnifiedSetup.exe 以完成升級。 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>升級失敗，因為協力廠商資料夾被重命名

要成功升級，不得重命名協力廠商資料夾。

解決此問題。

1. 啟動登錄編輯程式 (regedit.exe)，並開啟 HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10 分支。
1. 檢查 `Build_Version` 機碼值。 如果機碼是設定為最新版本，請減少版本號碼。 例如，如果最新版本為 9.22.\*，且 `Build_Version` 機碼設定為該值，則將它減少為 9.21.\*。
1. 下載最新的 Microsoft Azure Site Recovery 整合安裝：
   1. 在 [Azure Site Recovery 中的服務更新](service-updates-how-to.md#links-to-currently-supported-update-rollups)一文的＜目前支援的更新彙總套件連結＞小節中，選取您要升級的提供者。
   2. 在 [彙總] 頁面上，找出 [更新資訊]**** 區段，並下載適用於 Microsoft Azure Site Recovery 整合安裝更新彙總套件。
1. 開啟命令提示字元並瀏覽至您下載整合安裝檔案的資料夾，並使用下列命令將安裝檔從下載項目中解壓縮：MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;已解壓縮檔案的資料夾路徑&gt;。

    範例命令：

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. 在命令提示字元中，瀏覽至您解壓縮檔案的資料夾並執行下列安裝命令：
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. 使用 [工作管理員] 來監視安裝進度。 當 CX_THIRDPARTY_SETUP.EXE 的處理序已不再顯示在 [工作管理員] 中時，請繼續進行下一個步驟。
1. 確認 C:\thirdparty 存在且該資料夾包含 RRD 程式庫。
1. 返回您下載整合安裝的資料夾，然後執行 MicrosoftAzureSiteRecoveryUnifiedSetup.exe 以完成升級。 