---
title: 在 Azure 流量管理員中啟用資源記錄
description: 瞭解如何為您的流量管理員設定檔啟用資源記錄，並存取作為結果建立的記錄檔。
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: 25c0b18da1690557f11e36dd11dda693ddddb838
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401311"
---
# <a name="enable-resource-logging-in-azure-traffic-manager"></a>在 Azure 流量管理員中啟用資源記錄

本文說明如何啟用收集診斷資源記錄，以及存取流量管理員設定檔的記錄資料。

Azure 流量管理員資源記錄可讓您深入瞭解流量管理員設定檔資源的行為。 例如，您可以使用設定檔的記錄資料判斷為何針對某個端點進行的個別探查已經逾時。

## <a name="enable-resource-logging"></a>啟用資源記錄

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

您可以執行 [Azure Cloud Shell](https://shell.azure.com/powershell) 中採用的命令，或從您的電腦執行 PowerShell。 Azure Cloud Shell 是免費的互動式殼層。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。 如果您從電腦執行 PowerShell，就需要 Azure PowerShell 模組 1.0.0 或更新版本。 您可以執行 `Get-Module -ListAvailable Az` 來了解安裝的版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如果您在本機執行 PowerShell，也需要執行 `Login-AzAccount` 來登入 Azure。

1. **擷取流量管理員設定檔：**

    若要啟用資源記錄，您需要流量管理員設定檔的識別碼。 使用 [>get-aztrafficmanagerprofile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)來取得您想要啟用資源記錄的流量管理員設定檔。 輸出會包含流量管理員設定檔的識別碼資訊。

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **啟用流量管理員設定檔的資源記錄：**

    使用上一個步驟中取得的識別碼，為流量管理員設定檔啟用資源記錄，並 [設定 >set-azdiagnosticsetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest)。 以下命令會將流量管理員設定檔的詳細資訊記錄儲存至指定的 Azure 儲存體帳戶。 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **確認診斷設定**：

      使用 [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest) \(英文\)，來確認流量管理員設定檔的診斷設定。 以下命令會顯示記錄的資源類別。

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      請確定與流量管理員設定檔資源關聯的所有記錄類別都顯示為已啟用。 也請您確認儲存體帳戶已正確設定。

## <a name="access-log-files"></a>存取記錄檔
1. 登入 [Azure 入口網站](https://portal.azure.com)。 
1. 在入口網站中瀏覽至您的 Azure 儲存體帳戶。
2. 在 Azure 儲存體帳戶的 [概觀]**** 頁面中，於選取 [服務]**** 底下選取 [Blob]****。
3. 針對 [容器]****，選取 **insights-logs-probehealthstatusevents**，並向下瀏覽至 PT1H.json 檔案，然後按一下 [下載]**** 以下載並儲存一份此記錄。

    ![從 blob 儲存體存取您流量管理員設定檔記錄檔](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>流量管理員記錄結構描述

所有可透過 Azure 監視器共用的資源記錄都會共用通用的最上層架構，而且每個服務都有彈性地發出其本身事件的唯一屬性。 如需最上層資源記錄架構，請參閱 [Azure 資源記錄的支援服務、架構和類別](../azure-monitor/platform/tutorial-dashboards.md)。

下表包含 Azure 流量管理員設定檔資源特定的記錄結構描述。

|欄位名稱|欄位類型|定義|範例|
|----|----|---|---|
|EndpointName|字串|正在記錄其健全狀況狀態的流量管理員端點的名稱。|*myPrimaryEndpoint*|
|狀態|字串|所探查流量管理員端點的健全狀況狀態。 狀態可以是 **Up** 或 **down**。|**Up**|
|||||

## <a name="next-steps"></a>後續步驟

* 深入了解[流量管理員監視](traffic-manager-monitoring.md)

