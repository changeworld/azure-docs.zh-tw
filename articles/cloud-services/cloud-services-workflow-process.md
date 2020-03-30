---
title: Windows Azure VM 體系結構的工作流 |微軟文檔
description: 本文概述了部署服務時的工作流過程。
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "71162154"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Windows Azure 經典 VM 體系結構的工作流 
本文概述了部署或更新 Azure 資源（如虛擬機器）時發生的工作流過程。 

> [!NOTE]
>Azure 建立和處理資源的部署模型有二種：資源管理員和傳統。 本文涵蓋之內容包括使用傳統部署模型。

下圖顯示了 Azure 資源的體系結構。

![Azure 工作流](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>工作流基礎知識
   
**A**. RDFE / FFE 是從使用者到結構的通訊路徑。 RDFE（紅狗前端）是公開公開的 API，它是監管中心和服務管理 API（如視覺化工作室、Azure MMC 等）的前端。  來自使用者的所有請求都通過 RDFE。 FFE（結構前端）是將來自 RDFE 的請求轉換為結構命令的層。 RDFE 的所有請求都通過 FFE 到達結構控制器。

**B**. . . 結構控制器負責維護和監視資料中心中的所有資源。 它與交換矩陣作業系統上的交換矩陣主機代理通信，發送諸如客體作業系統版本、服務包、服務配置和服務狀態等資訊。

**C**. . 主機代理位於主機作業系統上，負責設置客體作業系統並與來賓代理 （WindowsAzureGuestAgent） 通信，以便將角色更新到預期的目標狀態，並與來賓代理執行活動訊號檢查。 如果主機代理在 10 分鐘內未收到活動訊號回應，則主機代理將重新開機客體作業系統。

**C2**. WaAppAgent 負責安裝、配置和更新 WindowsAzureGuestAgent.exe。

**D**. .  WindowsAzureGuestAgent 負責以下工作：

1. 配置客體作業系統，包括防火牆、ACL、本機存放區資源、服務包和配置以及證書。
2. 為角色將在該角色下運行的使用者帳戶設置 SID。
3. 將角色狀態傳達給結構。
4. 啟動 WaHostBootstrapper 並監視它，以確保角色處於目標狀態。

**E**. . WaHostBootstrapper 負責：

1. 讀取角色配置，並啟動所有適當的任務和進程以配置和運行角色。
2. 監視其所有子進程。
3. 在角色主機進程上引發狀態檢查事件。

**F**. . 如果角色配置為完全 IIS Web 角色（它將不為 SDK 1.2 HWC 角色運行），則 IIS配置器將運行。 它負責：

1. 啟動標準 IIS 服務
2. 在 Web 配置中配置重寫模組
3. 為服務模型中配置的角色設置 AppPool
4. 設置 IIS 日誌記錄以指向診斷存儲本機存放區資料夾
5. 配置許可權和 ACL
6. 網站駐留在 %角色根%：\sites root\0 中，AppPool 指向此位置以運行 IIS。 

**G**. . 啟動任務由角色模型定義，由 WaHostBootstrapper 啟動。 啟動任務可以配置為在後臺非同步運行，主機引導器將啟動啟動任務，然後繼續執行其他啟動任務。 啟動任務也可以配置為在簡單（預設）模式下運行，其中主機引導器將等待啟動任務完成運行並返回成功 （0） 結束代碼，然後再繼續執行下一個啟動任務。

**H**. . 這些任務是 SDK 的一部分，被定義為角色服務定義 （.csdef） 中的外掛程式。 擴展為啟動任務時，**診斷代理**和**RemoteAccessAgent**是唯一的，因為它們各自訂兩個啟動任務，一個常規任務，一個具有 **/blockStartup**參數。 正常啟動任務定義為後臺啟動任務，以便在角色本身運行時在後臺運行。 **/blockStartup 啟動**任務定義為一個簡單的啟動任務，以便 WaHostBootstrapper 等待它退出，然後再繼續。 **/blockStartup**任務等待常規任務完成初始化，然後退出並允許主機引導程式繼續。 這樣做是為了在角色進程啟動之前配置診斷和 RDP 訪問（這通過 /block 啟動任務完成）。 這還允許診斷和 RDP 訪問在主機引導器完成啟動任務（這通過正常任務完成）後繼續運行。

**I**. WaWorkerHost 是正常工作人員角色的標準主機進程。 此主機進程承載角色的所有 DLL 和進入點代碼，如 OnStart 和 Run。

**J**. . 如果 Web 角色配置為使用 SDK 1.2 相容的可託管 Web 核心 （HWC），則 WaWebHost 是 Web 角色的標準主機進程。 角色可以通過從服務定義 （.csdef） 中刪除元素來啟用 HWC 模式。 在此模式下，服務的所有代碼和 DLL 都從 WaWebHost 進程運行。 不使用 IIS （w3wp），並且沒有在 IIS 管理器中配置 AppPools，因為 IIS 託管在 WaWebHost.exe 中。

**K**. WaIISHost 是使用完全 IIS 的 Web 角色角色進入點代碼的主機進程。 此過程載入找到的第一個 DLL，該 DLL 使用**角色入口Point**類並執行來自此類的代碼（OnStart、運行、OnStop）。 在此過程中，將引發在角色進入點類中創建的任何**角色環境**事件（如狀態檢查和更改）。

**L**. W3WP 是標準 IIS 工作進程，如果角色配置為使用完全 IIS，則使用該進程。 這將運行從 IIS 配置器配置的應用池。 在此創建的任何角色環境事件（如狀態檢查和更改）將在此過程中引發。 請注意，如果您在兩個進程中訂閱事件，則角色環境事件將在這兩個位置（WaIISHost 和 w3wp.exe）觸發。

## <a name="workflow-processes"></a>工作流流程

1. 使用者發出請求，例如上載".cspkg"和".cscfg"檔、告訴資源停止或進行配置更改等。 這可以通過 Azure 門戶或使用服務管理 API 的工具（如視覺化工作室發佈功能）來完成。 此請求轉到 RDFE 以執行所有與訂閱相關的工作，然後將請求傳達給 FFE。 這些工作流步驟的其餘部分是部署新包並啟動它。
2. FFE 找到正確的電腦池（基於客戶輸入，如關聯組或地理位置加上來自結構的輸入（如機器可用性），並與該電腦池中的主結構控制器進行通信。
3. 結構控制器查找具有可用 CPU 內核（或旋轉新主機）的主機。 服務包和配置將複製到主機，並且結構控制器與主機作業系統上的主機代理通信以部署包（配置 DIP、埠、客體作業系統等）。
4. 主機代理啟動客體作業系統並與來賓代理（WindowsAzureGuestAgent）通信。 主機向來賓發送活動訊號，以確保角色正朝著目標狀態工作。
5. WindowsAzureGuestAgent 設置客體作業系統（防火牆、ACL、本機存放區等），將新的 XML 配置檔案複製到 c：\Config，然後啟動 WaHostBootstrapper 進程。
6. 對於完全 IIS Web 角色，WaHostBootstrapper 啟動 IIS 配置器，並告訴它從 IIS 中刪除 Web 角色的任何現有 AppPools。
7. WaHostBootstrapper 從 E：\RoleModel.xml 讀取**啟動**任務並開始執行啟動任務。 WaHostBootstrapper 等待所有簡單的啟動任務都已完成並返回"成功"消息。
8. 對於完整 IIS Web 角色，WaHostBootstrapper 告訴 IIS 配置器配置 IIS AppPool`E:\Sitesroot\<index>`並將`<index>`網站指向 ，其中基於 0`<Sites>`的索引將輸入為服務定義的元素數。
9. WaHostBootstrapper 將根據角色類型啟動主機進程：
    1. **工作人員角色**： WaWorkerHost.exe 已啟動。 WaHostBootstrapper 執行 OnStart（） 方法。 返回後，WaHostBootstrapper 開始執行 Run（） 方法，然後同時將角色標記為"就緒"，並將其放入負載等化器旋轉中（如果定義了 InputEndpoints）。 然後，WaHostBootsrapper 進入檢查角色狀態的迴圈。
    1. **SDK 1.2 HWC Web 角色**：WaWebHost 已啟動。 WaHostBootstrapper 執行 OnStart（） 方法。 返回後，WaHostBootstrapper 開始執行 Run（） 方法，然後同時將角色標記為"就緒"，並將其放入負載等化器旋轉中。 WaWebHost 發出預熱請求 （GET /do.rd_runtime_init）。 所有 Web 請求都發送到 WaWebHost.exe。 然後，WaHostBootsrapper 進入檢查角色狀態的迴圈。
    1. **完整的 IIS Web 角色**： aIISHost 已啟動。 WaHostBootstrapper 執行 OnStart（） 方法。 返回後，它開始執行 Run（） 方法，然後同時將角色標記為"就緒"，並將其放入負載等化器旋轉中。 然後，WaHostBootsrapper 進入檢查角色狀態的迴圈。
10. 對完全 IIS Web 角色的傳入 Web 請求觸發 IIS 啟動 W3WP 進程並提供服務請求，這與在本地 IIS 環境中的請求相同。

## <a name="log-file-locations"></a>日誌檔位置

**WindowsAzureGuestAgent**

- C：\Logs_AppAgent運行時.Log。  
此日誌包含對服務的更改，包括啟動、停止和新配置。 如果服務未更改，則可以在此日誌檔中看到較大的時間差距。
- C：[Logs]WaAppAgent.Log。  
此日誌包含狀態更新和活動訊號通知，每 2-3 秒更新一次。  此日誌包含實例狀態的歷史視圖，並將告訴您實例何時未處於"就緒"狀態。
 
**瓦霍斯博斯特拉珀**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**瓦伊霍斯**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IIS配置器**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS 日誌**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**視窗事件日誌**

`D:\Windows\System32\Winevt\Logs`
 



