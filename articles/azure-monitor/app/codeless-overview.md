---
title: 監視您的應用程式而不需要變更程式碼-自動檢測 Azure 監視器 Application Insights |Microsoft Docs
description: Azure 監視器 Application Insights 的自動檢測總覽-無程式碼應用程式效能管理
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: b9be0808d4edc9662c1d3a4144f3102242560564
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075351"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>什麼是自動檢測或無程式碼附加 Azure 監視器 Application Insights？

自動檢測或無程式碼附加] 可讓您使用 Application Insights 啟用應用程式監視，而不需要變更您的程式碼。  

Application Insights 與各種資源提供者整合，並可在不同的環境中運作。 基本上，您只需要啟用和-在某些情況下-設定代理程式，這將會收集現成的遙測。 您將無法在 Application Insights 資源中看到計量、資料和相依性，這可讓您在潛在問題發生前找出其來源，並使用端對端交易視圖來分析根本原因。

## <a name="supported-environments-languages-and-resource-providers"></a>支援的環境、語言和資源提供者

當我們新增更多整合時，自動檢測功能矩陣會變得很複雜。 下表顯示對於各種資源提供者、語言和環境的支援，目前的重要性狀態。

|環境/資源提供者 | .NET            | .NET Core       | Java            | Node.js         |
|------------------------------|-----------------|-----------------|-----------------|-----------------|
|Windows 上的 Azure App Service  | GA，OnBD *       | GA，加入宣告      | 私人預覽 | 私人預覽 |
|Linux 上的 Azure App Service    | 不適用             | 不支援   | 公開預覽  | 公開預覽  |
|AKS 上的 Azure App Service      | 不適用             | 設計中       | 設計中       | 設計中       |
|Azure Functions-基本       | GA，OnBD *       | GA，OnBD *       | GA，OnBD *       | GA，OnBD *       |
|Azure Functions 相依性| 不支援   | 不支援   | 公開預覽  | 不支援   |
|Azure Kubernetes Service      | 不適用             | 設計中       | 透過代理程式   | 設計中       |
|Azure Vm 視窗             | 公開預覽  | 不支援   | 不支援   | 不支援   |
|內部部署 Vm 視窗       | GA，加入宣告      | 不支援   | 透過代理程式   | 不支援   |
|獨立代理程式-任何 env。   | 不支援   | 不支援   | 公開預覽  | 不支援   |

* OnBD 預設為 [開啟]，當您在支援的環境中部署應用程式時，將會自動啟用 Application Insights。 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

[Azure App Service 上的應用程式監視](./azure-web-apps.md?tabs=net)適用于 .net 應用程式，而且預設會啟用，只要按一下就能啟用 .net Core，而 JAVA 和 Node.js 則為個人預覽版。

### <a name="linux"></a>Linux 

App Service 中的 JAVA 和 Node.js 應用程式的監視處於公開預覽狀態，而且可以在所有區域中提供的 Azure 入口網站中啟用。

## <a name="azure-functions"></a>Azure Functions

預設會啟用 Azure Functions 的基本監視，以收集記錄、效能、錯誤資料和 HTTP 要求。 針對 JAVA 應用程式，您可以使用分散式追蹤來啟用更豐富的監視，並取得端對端交易詳細資料。 適用于 JAVA 的這項功能處於公開預覽狀態，您可以[在 Azure 入口網站中加以啟用](./monitor-functions.md)。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes Service 的無程式碼檢測目前適用于透過[獨立代理](./java-in-process-agent.md)程式的 JAVA 應用程式。 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows Vm 和虛擬機器擴展集

[Azure vm 和虛擬機器擴展集的自動檢測](./azure-vm-vmss-apps.md)適用于 .net 應用程式 

## <a name="on-premises-servers"></a>內部部署伺服器
您可以輕鬆地針對 .NET 應用程式和[JAVA 應用](./java-in-process-agent.md)[程式啟用內部部署 Windows 伺服器](./status-monitor-v2-overview.md)的監視。

## <a name="other-environments"></a>其他環境
多功能 JAVA 獨立代理程式可在任何環境中運作，不需要檢測您的程式碼。 [遵循指南](./java-in-process-agent.md)以啟用 Application Insights，並閱讀有關 JAVA 代理程式的絕佳功能。 代理程式處於公開預覽狀態，並可在所有區域使用。 

## <a name="next-steps"></a>後續步驟

* [Application Insights 總覽](./app-insights-overview.md)
* [應用程式對應](./../../azure-monitor/app/app-map.md)
* [端對端效能監視](./../../azure-monitor/learn/tutorial-performance.md)
