---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ff542d2782293e89b66e5d25cb67a9bcde6da8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75772951"
---
無法從伺服器存取 Azure 檔案同步服務時，可能會發生此錯誤。 您可以透過下列步驟，對此錯誤進行疑難排解：

1. 確認 Windows 服務 `FileSyncSvc.exe` 未遭到防火牆封鎖。
2. 確認連接埠 443 已開放給 Azure 檔案同步服務的傳出連線。 您可以透過 `Test-NetConnection` Cmdlet 完成這項作業。 下列 `<azure-file-sync-endpoint>` 預留位置的 URL 可以在 [Azure 檔案同步 Proxy 和防火牆設定](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall)文件中找到。 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. 確定 Proxy 設定已如預期般設定。 這可以透過 `Get-StorageSyncProxyConfiguration` Cmdlet 完成這項作業。 有關針對 Azure 檔案同步進行 Proxy 設定的詳細資訊，可以在 [Azure 檔案同步 Proxy 和防火牆設定](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall)中找到。

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. 使用 Test-StorageSyncNetworkConnectivity Cmdlet 來檢查與服務端點的網路連線能力。 若要深入了解，請參閱[測試與服務端點的網路連線能力](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints) (機器翻譯)。    

5. 如需針對網路連線進行疑難排解的其他協助，請連絡網路系統管理員。
