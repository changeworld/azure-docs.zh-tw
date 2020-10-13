---
title: 在不變更程式碼的情況下監視您的應用程式-Azure 監視器 Application Insights 的自動檢測 |Microsoft Docs
description: Azure 監視器 Application Insights 的自動檢測簡介-無程式碼應用程式效能管理
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 234f772b4a77b780f0b0c1faf307c93847e5caf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87310713"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>什麼是自動檢測或無程式碼附加-Azure 監視器 Application Insights？

自動檢測或無程式碼連接可讓您使用 Application Insights 來啟用應用程式監視，而不需要變更您的程式碼。  

Application Insights 與不同的資源提供者整合，並可在不同的環境中運作。 基本上，您只需要啟用和-在某些情況下，就可以設定代理程式，以收集現成的遙測。 您將不會在 Application Insights 資源中看到計量、資料和相依性，這可讓您在潛在問題發生前找出潛在問題的來源，並使用端對端交易視圖來分析根本原因。

## <a name="supported-environments-languages-and-resource-providers"></a>支援的環境、語言和資源提供者

當我們加入更多整合時，自動檢測功能矩陣會變得複雜。 下錶針對各種資源提供者、語言和環境的支援，顯示目前狀態的最新狀態。

|環境/資源提供者 | .NET            | .NET Core       | Java            | Node.js         |
|------------------------------|-----------------|-----------------|-----------------|-----------------|
|Windows 上的 Azure App Service  | GA，OnBD *       | GA，加入      | 私人預覽 | 私人預覽 |
|Linux 上的 Azure App Service    | N/A             | 不支援   | 公開預覽  | 公開預覽  |
|AKS 上的 Azure App Service      | N/A             | 設計中       | 設計中       | 設計中       |
|Azure Functions-基本       | GA，OnBD *       | GA，OnBD *       | GA，OnBD *       | GA，OnBD *       |
|Azure Functions 相依性| 不支援   | 不支援   | 公開預覽  | 不支援   |
|Azure Kubernetes Service      | N/A             | 設計中       | 透過代理程式   | 設計中       |
|Azure Vm 視窗             | 公開預覽  | 不支援   | 不支援   | 不支援   |
|內部部署 Vm 視窗       | GA，加入      | 不支援   | 透過代理程式   | 不支援   |
|獨立代理程式-任何 env。   | 不支援   | 不支援   | 公開預覽  | 不支援   |

* OnBD 預設為開啟，一旦您在支援的環境中部署應用程式，就會自動啟用 Application Insights。 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

[Azure App Service 上的應用程式監視](./azure-web-apps.md?tabs=net) 適用于 .net 應用程式，且預設為啟用，只要按一下就可以啟用 .net Core，而 JAVA 和 Node.js 則處於私人預覽狀態。

### <a name="linux"></a>Linux 

在 App Service 中監視 JAVA 和 Node.js 應用程式目前為公開預覽狀態，而且可以在 Azure 入口網站中啟用（適用于所有區域）。

## <a name="azure-functions"></a>Azure Functions

預設會啟用 Azure Functions 的基本監視，以收集記錄、效能、錯誤資料和 HTTP 要求。 對於 JAVA 應用程式，您可以使用分散式追蹤來啟用更豐富的監視，並取得端對端交易詳細資料。 這項適用于 JAVA 的功能處於公開預覽狀態，您可以 [在 Azure 入口網站中加以啟用](./monitor-functions.md)。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes Service 的無程式碼檢測目前可透過 [獨立代理](./java-in-process-agent.md)程式在 JAVA 應用程式中使用。 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows Vm 和虛擬機器擴展集

適用于[Azure vm 和虛擬機器擴展集的自動檢測](./azure-vm-vmss-apps.md)適用于 .net 應用程式 

## <a name="on-premises-servers"></a>內部部署伺服器
針對 .NET 應用程式和[JAVA 應用](./java-in-process-agent.md)程式，您可以輕鬆地啟用內部[部署 Windows server 的](./status-monitor-v2-overview.md)監視。

## <a name="other-environments"></a>其他環境
多功能 JAVA 獨立代理程式可在任何環境中運作，而不需要檢測您的程式碼。 [遵循指南](./java-in-process-agent.md) 來啟用 Application Insights，並閱讀有關 JAVA 代理程式出色功能的資訊。 代理程式處於公開預覽狀態，並可在所有區域使用。 

## <a name="next-steps"></a>接下來的步驟

* [Application Insights 總覽](./app-insights-overview.md)
* [應用程式對應](./app-map.md)
* [端對端效能監視](../learn/tutorial-performance.md)

