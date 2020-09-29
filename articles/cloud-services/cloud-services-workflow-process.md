---
title: Windows Azure VM 架構的工作流程 |Microsoft Docs
description: 本文概述當您部署服務時的工作流程處理。
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
ms.openlocfilehash: 9c427982854e1d328b5d1553aa86866ad298eea1
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461305"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Windows Azure 傳統 VM 架構的工作流程 
本文概述當您部署或更新 Azure 資源（例如虛擬機器）時所發生的工作流程處理。 

> [!NOTE]
>Azure 建立和處理資源的部署模型有二種：資源管理員和傳統。 本文涵蓋之內容包括使用傳統部署模型。

下圖顯示 Azure 資源的架構。

:::image type="content" source="./media/cloud-services-workflow-process/workflow.jpg" alt-text="<替換 Azure workflow>的影像 ":::

## <a name="workflow-basics"></a>工作流程基本概念
   
**A**。 RDFE/FFE 是從使用者到網狀架構的通訊路徑。 RDFE (RedDog 前端) 是公開的 API，也就是管理入口網站和服務管理 API 的前端，例如 Visual Studio、Azure MMC 等等。  來自使用者的所有要求都經過 RDFE。 FFE (Fabric 前端) 是將要求從 RDFE 轉譯為網狀架構命令的層級。 所有來自 RDFE 的要求都會通過 FFE，以連線到網狀架構控制器。

**B.** 網狀架構控制器負責維護和監視資料中心內的所有資源。 它會與網狀架構作業系統上的網狀架構主機代理程式通訊，以傳送客體作業系統版本、服務套件、服務設定和服務狀態等資訊。

**C.** 主機代理程式位於主機 OS 上，負責設定客體作業系統和與來賓代理程式通訊 (WindowsAzureGuestAgent) ，以便將角色更新為預期的目標狀態，並使用來賓代理程式進行檢查。 如果主機代理程式未收到10分鐘的回應時間回應，主機代理程式就會重新開機該客體作業系統。

**C2**。 WaAppAgent 負責安裝、設定和更新 WindowsAzureGuestAgent.exe。

**D.**  WindowsAzureGuestAgent 負責下列各項：

1. 設定客體作業系統，包括防火牆、Acl、LocalStorage 資源、服務套件和設定，以及憑證。
2. 設定要在其下執行角色之使用者帳戶的 SID。
3. 將角色狀態傳達給網狀架構。
4. 開始 Wahostbootstrapper.exe 和監視，以確保角色處於目標狀態。

**E**。 Wahostbootstrapper.exe 負責：

1. 讀取角色設定，並啟動所有適當的工作和進程來設定和執行角色。
2. 監視其所有子進程。
3. 引發角色主機進程的 StatusCheck 事件。

**F**。 如果角色已設定為完整的 IIS web 角色，則會執行 Iisconfigurator 找到， (不會針對 SDK 1.2 HWC 角色) 執行。 它負責：

1. 啟動標準 IIS 服務
2. 在 web 設定中設定重寫模組
3. 針對服務模型中設定的角色設定 AppPool
4. 設定 IIS 記錄以指向 DiagnosticStore LocalStorage 資料夾
5. 設定許可權和 Acl
6. 網站位於% roleroot%： \sitesroot\0 中，而 AppPool 指向這個位置以執行 IIS。 

**G.** 啟動工作由角色模型定義，並由 Wahostbootstrapper.exe 啟動。 啟動工作可以設定為以非同步方式在背景中執行，而主機啟動載入器將會啟動啟動工作，然後繼續進行其他啟動工作。 啟動工作也可以設定為以簡單 (預設) 模式執行，主機啟動載入器會等待啟動工作完成執行，並在繼續進行下一項啟動工作之前，傳回成功 (0) 結束代碼。

**H**。 這些工作是 SDK 的一部分，而且會在角色的服務定義中定義為外掛程式， ( 的) 。 當擴充到啟動工作時， **DiagnosticsAgent** 和 **RemoteAccessAgent** 是唯一的，因為它們各自訂了兩個啟動工作，一個是一般的，另一個則具有 **/blockStartup** 參數。 一般啟動工作會定義為背景啟動工作，讓它可以在角色本身執行時于背景中執行。 **/BlockStartup**啟動工作定義為簡單的啟動工作，讓 wahostbootstrapper.exe 等候它結束，然後再繼續進行。 **/BlockStartup**工作會等候一般工作完成初始化，然後結束並允許主機啟動載入器繼續。 這樣做的目的是為了讓您可以在角色處理開始之前設定診斷和 RDP 存取 (這是透過/blockStartup 工作) 完成。 這也可讓診斷和 RDP 存取在主機啟動載入器完成啟動工作之後繼續執行 (這是透過一般的工作) 完成。

**我**。 Waworkerhost.exe 是一般背景工作角色的標準主機進程。 此主機進程會裝載角色的所有 Dll 和進入點程式碼，例如 OnStart 和 Run。

**J**。 Waiishost.exe 是使用完整 IIS 之 web 角色的角色進入點程式碼的主機進程。 此程式會載入使用 **RoleEntryPoint** 類別的第一個 DLL，並執行此類別的程式碼 (OnStart、Run、OnStop) 。 在此程式中，會引發在 RoleEntryPoint 類別中建立的任何 **RoleEnvironment** 事件 (例如 StatusCheck 和已變更的) 。

**K**。 W3WP.EXE 是標準的 IIS 背景工作進程，會在角色設定為使用完整 IIS 時使用。 這會執行從 Iisconfigurator 找到設定的 AppPool。 在此程式中，會引發任何 RoleEnvironment 事件 (例如 StatusCheck 和已變更的) 。 請注意，如果您在這兩個處理常式中訂閱事件，RoleEnvironment 事件將會在兩個位置中引發 (Waiishost.exe 和 w3wp.exe) 。

## <a name="workflow-processes"></a>工作流程處理

1. 使用者提出要求，例如上傳 ". .cspkg" 和 ".cscfg" 檔案、告知資源停止或進行設定變更等等。 這可以透過 Azure 入口網站或使用服務管理 API 的工具來完成，例如 Visual Studio 發行功能。 此要求會傳送至 RDFE 以執行所有與訂用帳戶相關的工作，然後將要求傳達給 FFE。 這些工作流程步驟的其餘部分是部署新的封裝，並加以啟動。
2. FFE 會根據客戶輸入（例如，「同質群組」或「地理位置」，再加上來自網狀架構的輸入，例如機器可用性) ，並與該電腦集區中的主要網狀架構控制器通訊，來尋找正確的電腦集區 (。
3. 網狀架構控制器會尋找具有可用 CPU 核心的主機 (或啟動新的主機) 。 服務套件和設定會複製到主機，而網狀架構控制器會與主機 OS 上的主機代理程式進行通訊，以部署套件 (設定 Dip、埠、客體作業系統等) 。
4. 主機代理程式會啟動虛擬作業系統，並與來賓代理程式 (WindowsAzureGuestAgent) 進行通訊。 主機會將心跳傳送給來賓，以確保角色正致力於其目標狀態。
5. WindowsAzureGuestAgent 會在) 上設定客體作業系統 (防火牆、Acl、LocalStorage 等等，將新的 XML 配置檔案複製到 c:\Config，然後啟動 Wahostbootstrapper.exe 流程。
6. 針對完整的 IIS web 角色，Wahostbootstrapper.exe 會開始 Iisconfigurator 找到，並告訴它從 IIS 中刪除任何現有的 web 角色 AppPools。
7. Wahostbootstrapper.exe 會從 E:\RoleModel.xml 讀取 **啟動** 工作，並開始執行啟動工作。 Wahostbootstrapper.exe 會等到所有簡單的啟動工作都完成，並傳回「成功」訊息。
8. 若為完整的 IIS web 角色，Wahostbootstrapper.exe 會告知 Iisconfigurator 找到設定 IIS AppPool 並將網站指向 `E:\Sitesroot\<index>` ，其中 `<index>` 是以0為起始的索引，其中是 `<Sites>` 針對服務定義的元素數目。
9. Wahostbootstrapper.exe 會根據角色類型啟動主機進程：
    1. 背景**工作角色**： WaWorkerHost.exe 已啟動。 Wahostbootstrapper.exe 會執行 OnStart ( # A1 方法。 傳回之後，Wahostbootstrapper.exe 就會開始執行 ( # A1 方法的執行，然後將該角色同時標示為「就緒」，然後將它放入負載平衡器迴圈中 (如果 InputEndpoints 定義) 。 WaHostBootsrapper 接著會進入檢查角色狀態的迴圈。
    2. **完整的 IIS Web 角色**： aIISHost 已啟動。 Wahostbootstrapper.exe 會執行 OnStart ( # A1 方法。 傳回之後，它會開始執行 ( # A1 方法，然後將該角色同時標示為「就緒」，然後將它放入負載平衡器迴圈中。 WaHostBootsrapper 接著會進入檢查角色狀態的迴圈。
10. 傳入的 web 要求至完整的 IIS web 角色，會觸發 IIS 以啟動 W3WP.EXE 程式並提供要求，就像在內部部署 IIS 環境中一樣。

## <a name="log-file-locations"></a>記錄檔位置

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
此記錄包含服務的變更，包括啟動、停止和新設定。 如果服務沒有變更，您可以預期會在此記錄檔中看到很長的時間間隔。
- C:\Logs\WaAppAgent.Log.  
此記錄包含狀態更新和信號通知，每2-3 秒會更新一次。  此記錄檔包含實例狀態的歷程記錄，並會在實例不是就緒狀態時通知您。
 
**Wahostbootstrapper.exe**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**Waiishost.exe**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**Iisconfigurator 找到**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS 記錄**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Windows 事件記錄**

`D:\Windows\System32\Winevt\Logs`