---
title: Windows Azure VM 架構的工作流程 |Microsoft Docs
description: 本文概述當您部署服務時的工作流程程式。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "71162154"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Windows Azure 傳統 VM 架構的工作流程 
本文概述當您部署或更新 Azure 資源（例如虛擬機器）時所發生的工作流程程式。 

> [!NOTE]
>Azure 建立和處理資源的部署模型有二種：資源管理員和傳統。 本文涵蓋之內容包括使用傳統部署模型。

下圖顯示 Azure 資源的架構。

![Azure 工作流程](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>工作流程基本概念
   
**答**： RDFE/FFE 是從使用者到網狀架構的通訊路徑。 RDFE （RedDog 前端）是公開的 API，其為管理入口網站的前端和服務管理 API （例如 Visual Studio、Azure MMC 等等）。  使用者所提出的所有要求都會通過 RDFE。 FFE （網狀架構前端）是將來自 RDFE 的要求轉譯成網狀架構命令的層級。 來自 RDFE 的所有要求都會通過 FFE，以連線到網狀架構控制器。

**B.** 網狀架構控制器負責維護和監視資料中心內的所有資源。 它會與光纖 OS 上的網狀架構主機代理程式通訊，以傳送資訊，例如客體作業系統版本、服務套件、服務設定和服務狀態。

**C**。 主機代理程式位於主機 OS 上，並且負責設定來賓 OS 和與來賓代理程式（WindowsAzureGuestAgent）通訊，以便將角色更新為預定目標狀態，並使用來賓代理程式進行檢查。 如果主機代理程式未收到10分鐘的回應時間，主機代理程式就會重新開機客體作業系統。

**C2**。 WaAppAgent 負責安裝、設定和更新 WindowsAzureGuestAgent.exe。

**D.**  WindowsAzureGuestAgent 負責下列各項：

1. 設定客體作業系統，包括防火牆、Acl、LocalStorage 資源、服務套件和設定，以及憑證。
2. 設定角色將在其下執行之使用者帳戶的 SID。
3. 將角色狀態與網狀架構通訊。
4. 開始 Wahostbootstrapper.exe 並監視它，以確定角色處於目標狀態。

**E**。 Wahostbootstrapper.exe 負責：

1. 讀取角色設定，並啟動所有適當的工作和程式來設定和執行角色。
2. 監視其所有子進程。
3. 在角色主機進程上引發 StatusCheck 事件。

**F**。 如果角色已設定為完整的 IIS web 角色（不會針對 SDK 1.2 HWC 角色執行），Iisconfigurator 找到就會執行。 它負責：

1. 啟動標準 IIS 服務
2. 在 web 設定中設定重寫模組
3. 在服務模型中設定所設定角色的 AppPool
4. 設定 IIS 記錄以指向 DiagnosticStore LocalStorage 資料夾
5. 設定許可權和 Acl
6. 網站位於% roleroot%： \sitesroot\0，而 AppPool 指向這個位置以執行 IIS。 

**G.** 啟動工作是由角色模型所定義，並由 Wahostbootstrapper.exe 啟動。 啟動工作可以設定為在背景中以非同步方式執行，而主機啟動載入器將會開始啟動工作，然後繼續執行其他啟動工作。 啟動工作也可以設定為以簡單（預設）模式執行，讓主機啟動載入器會等候啟動工作完成執行，並傳回成功（0）結束代碼，然後再繼續進行下一個啟動工作。

**H**。 這些工作是 SDK 的一部分，並定義為角色的服務定義（...）中的外掛程式。 當擴充到啟動工作時， **DiagnosticsAgent**和**RemoteAccessAgent**是唯一的，因為每個都定義兩個啟動工作，一個為一般，另一個具有 **/blockStartup**參數。 一般啟動工作會定義為背景啟動工作，讓它可以在角色本身執行時于背景中執行。 **/BlockStartup**啟動工作是定義為簡單的啟動工作，因此 wahostbootstrapper.exe 會等待它結束，然後再繼續進行。 **/BlockStartup**工作會等候一般工作完成初始化，然後結束並允許主機啟動載入器繼續進行。 這是為了讓您可以在角色處理開始之前設定診斷和 RDP 存取（這是透過/blockStartup 工作完成）。 這也可讓診斷和 RDP 存取在主機啟動載入器完成啟動工作（這是透過正常工作完成）後繼續執行。

**我**。 Waworkerhost.exe 是一般背景工作角色的標準主機進程。 此主機進程會裝載所有角色的 Dll 和進入點程式碼，例如 OnStart 並執行。

**J**。 WaWebHost 是 web 角色的標準主機進程，如果它們設定為使用 SDK 1.2 相容的可裝載 Web 核心（HWC）。 角色可以藉由從服務定義（.）中移除元素來啟用 HWC 模式。 在此模式中，所有服務的程式碼和 Dll 都會從 WaWebHost 進程執行。 不會使用 IIS （w3wp.exe），而且 IIS 管理員中不會設定任何 AppPools，因為 IIS 是裝載在 WaWebHost.exe 內。

**K**。 Waiishost.exe 是使用完整 IIS 之 web 角色的角色進入點代碼的主機進程。 此程式會載入第一個使用**RoleEntryPoint**類別的 DLL，並從這個類別執行程式碼（OnStart、Run、OnStop）。 在此進程中，會引發在 RoleEntryPoint 類別中建立的任何**RoleEnvironment**事件（例如 StatusCheck 和已變更）。

**L**。 W3WP.EXE 是標準的 IIS 背景工作進程，如果角色已設定為使用完整 IIS，則會使用此程式。 這會執行從 Iisconfigurator 找到設定的 AppPool。 此處建立的任何 RoleEnvironment 事件（例如 StatusCheck 和 Changed）都會在此程式中引發。 請注意，如果您在這兩個處理常式中訂閱事件，RoleEnvironment 事件將會在兩個位置（Waiishost.exe 和 w3wp.exe）中引發。

## <a name="workflow-processes"></a>工作流程處理

1. 使用者提出要求，例如上傳 ". .cspkg" 和 ".cscfg" 檔案、告訴資源停止或進行設定變更等等。 這可以透過 Azure 入口網站或使用服務管理 API 的工具（例如 Visual Studio 發佈功能）來完成。 此要求會前往 RDFE 來執行所有與訂用帳戶相關的工作，然後將要求傳達給 FFE。 這些工作流程步驟的其餘部分是部署新的套件，並加以啟動。
2. FFE 會尋找正確的電腦集區（根據客戶輸入，例如，同質群組或地理位置，再加上網狀架構的輸入，例如機器可用性），並與該電腦集區中的主要網狀架構控制器通訊。
3. 網狀架構控制器會尋找具有可用 CPU 核心（或啟動新主機）的主機。 服務封裝和設定會複製到主機，而網狀架構控制器會與主機 OS 上的主機代理程式通訊，以部署套件（設定 Dip、埠、客體作業系統等）。
4. 主機代理程式會啟動「來賓 OS」並與「來賓代理程式」（WindowsAzureGuestAgent）通訊。 主機會將心跳傳送給來賓，以確定角色正朝著其目標狀態運作。
5. WindowsAzureGuestAgent 會設定虛擬作業系統（防火牆、Acl、LocalStorage 等）、將新的 XML 設定檔案複製到 c:\Config，然後啟動 Wahostbootstrapper.exe 程式。
6. 針對完整的 IIS web 角色，Wahostbootstrapper.exe 會啟動 Iisconfigurator 找到，並告訴它從 IIS 刪除 web 角色的任何現有 AppPools。
7. Wahostbootstrapper.exe 會從 E:\RoleModel.xml 讀取**啟動**工作，並開始執行啟動工作。 Wahostbootstrapper.exe 會等到所有簡單啟動工作都完成，並傳回「成功」訊息為止。
8. 針對完整的 IIS web 角色，Wahostbootstrapper.exe 會告知 Iisconfigurator 找到設定 IIS AppPool 並將網站指向 `E:\Sitesroot\<index>` ，其中 `<index>` 是針對服務所定義之專案數目的以零為起始的索引 `<Sites>` 。
9. Wahostbootstrapper.exe 會根據角色類型來啟動主機進程：
    1. 背景**工作角色**： WaWorkerHost.exe 已啟動。 Wahostbootstrapper.exe 會執行 OnStart （）方法。 傳回之後，Wahostbootstrapper.exe 會開始執行 Run （）方法，然後將該角色同時標示為就緒，並將其放入負載平衡器迴圈中（如果已定義 InputEndpoints）。 WaHostBootsrapper 接著會進入檢查角色狀態的迴圈。
    1. **SDK 1.2 HWC Web 角色**： WaWebHost 已啟動。 Wahostbootstrapper.exe 會執行 OnStart （）方法。 傳回之後，Wahostbootstrapper.exe 會開始執行 Run （）方法，然後將該角色同時標示為就緒，並將其放入負載平衡器迴圈中。 WaWebHost 會發出準備要求（GET/do. rd_runtime_init）。 所有的 web 要求都會傳送至 WaWebHost.exe。 WaHostBootsrapper 接著會進入檢查角色狀態的迴圈。
    1. **完整的 IIS Web 角色**： aIISHost 已啟動。 Wahostbootstrapper.exe 會執行 OnStart （）方法。 傳回之後，它會開始執行 Run （）方法，然後將該角色同時標示為就緒，並將其放入負載平衡器迴圈中。 WaHostBootsrapper 接著會進入檢查角色狀態的迴圈。
10. 對完整 IIS web 角色的傳入 web 要求會觸發 IIS 來啟動 W3WP.EXE 程式並提供要求，就像在內部部署 IIS 環境中所做的一樣。

## <a name="log-file-locations"></a>記錄檔位置

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
此記錄檔包含服務的變更，包括啟動、停止和新的設定。 如果服務不會變更，您可以預期在此記錄檔中看到較長的時間間隔。
- C:\Logs\WaAppAgent.Log.  
此記錄檔包含狀態更新和通知，並每隔2-3 秒更新一次。  此記錄檔包含實例狀態的歷史視圖，並會在實例不是處於就緒狀態時告訴您。
 
**Wahostbootstrapper.exe**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**Waiishost.exe**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**Iisconfigurator 找到**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS 記錄**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Windows 事件記錄**

`D:\Windows\System32\Winevt\Logs`
 



