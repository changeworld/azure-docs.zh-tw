---
title: Azure Functions 的規模調整和主控
description: 瞭解如何在 Azure Functions 取用方案和 Premium 方案之間做選擇。
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 490fa46deabc822e416705fe9bf9c5cdb58f8cd6
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936751"
---
# <a name="azure-functions-hosting-options"></a>Azure Functions 裝載選項

當您在 Azure 中建立函數應用程式時，您必須為您的應用程式選擇主控方案。 有三種基本的主控方案可用 Azure Functions：取用 [方案](consumption-plan.md)、高階 [方案](functions-premium-plan.md)和 [專用 (App Service) 方案](dedicated-plan.md)。 所有主控方案都已在 Linux 和 Windows 虛擬機器上正式推出 (GA) 。

您選擇的主控方案會指定下列行為：

* 調整函數應用程式的方式。
* 每個函數應用程式實例可用的資源。
* 支援先進的功能，例如 Azure 虛擬網路連線能力。

本文提供各種主控方案的詳細比較，以及以 Kubernetes 為基礎的裝載。

## <a name="overview-of-plans"></a>計畫的總覽

以下是函式的三個主要主控方案的優點摘要：

| | |
| --- | --- |  
|**[取用方案](consumption-plan.md)**| 當您的函式執行時，自動調整規模並只支付計算資源的費用。<br/><br/>在取用方案中，會根據傳入事件的數目，動態新增和移除函式主機的實例。<br/><br/> ✔預設主控方案。<br/>✔只有在您的函式執行時才需付費。<br/>✔會自動調整規模，即使在高負載期間也一樣。|  
|**[進階方案](functions-premium-plan.md)**|使用預先準備就緒的背景工作角色，以執行應用程式，並在閒置後無延遲、在更強大的實例上執行，並連接到虛擬網路，以根據需求自動調整規模。 <br/><br/>在下列情況下，請考慮 Azure Functions Premium 方案： <br/><br/>✔您的函式應用程式會持續執行，或幾乎持續執行。<br/>✔您有大量的小型執行和高執行費用，但取用方案中有低 GB 的秒數。<br/>✔您需要的 CPU 或記憶體選項比取用方案所提供的更多。<br/>✔您的程式碼所需的執行時間，超過取用方案允許的執行時間上限。<br/>✔您需要使用方式方案中無法使用的功能，例如虛擬網路連線能力。|  
|**[專用方案](dedicated-plan.md)** |以一般 [App Service 方案費率](https://azure.microsoft.com/pricing/details/app-service/windows/)在 App Service 方案內執行您的函式。<br/><br/>適用于無法使用 [Durable Functions](durable/durable-functions-overview.md) 的長時間執行案例。 在下列情況下，請考慮 App Service 方案：<br/><br/>✔您的現有、使用量過低的 Vm 已在執行其他 App Service 實例。<br/>✔您想要提供自訂映射，以在其上執行函數。 <br/>需要✔預測規模調整和成本。|  

本文中的比較表格也包含下列裝載選項，可提供執行函式應用程式的最高控制和隔離量。  

| | |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | App Service 環境 (ASE) 是一項 App Service 功能，可提供完全隔離和專用的環境，以安全地大規模執行 App Service 應用程式。<br/><br/>Ase 適用于需要下列需求的應用程式工作負載： <br/><br/>✔非常高的規模。<br/>✔完整的計算隔離和安全的網路存取。<br/>✔高記憶體使用量。|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes 提供在 Kubernetes 平臺上執行的完全隔離且專用的環境。<br/><br/> Kubernetes 適用于需要下列各項的應用程式工作負載： <br/>✔自訂的硬體需求。<br/>✔隔離和安全的網路存取。<br/>✔能夠在混合式或多重雲端環境中執行。<br/>✔與現有的 Kubernetes 應用程式和服務一起執行。|  

本文中其餘的表格會比較各種特性和行為的計畫。 如需動態主控方案之間的成本比較 (耗用量和 Premium) ，請參閱 [Azure Functions 定價頁面](https://azure.microsoft.com/pricing/details/functions/)。 如需各種專用方案選項的定價，請參閱 [App Service 定價頁面](https://azure.microsoft.com/pricing/details/app-service/windows/)。 

## <a name="operating-systemruntime"></a>作業系統/執行時間

下表顯示支援的作業系統和主控方案的語言執行時間支援。

| | Linux<sup>1</sup><br/>僅限程式碼 | Windows<sup>2</sup><br/>僅限程式碼 | Linux<sup>1、3</sup><br/>Docker 容器 |
| --- | --- | --- | --- |
| **[取用方案](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | 不支援  |
| **[進階方案](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[專用方案](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | n/a | n/a |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux 是 Python 執行時間堆疊唯一支援的作業系統。 <br/>
<sup>2</sup> Windows 是 PowerShell 執行時間堆疊唯一支援的作業系統。<br/>
<sup>3</sup> Linux 是 Docker 容器唯一支援的作業系統。<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>調整

下表比較各種主控方案的縮放行為。

| | 擴增 | 最大實例數目 |
| --- | --- | --- |
| **[取用方案](consumption-plan.md)** | [事件驅動](event-driven-scaling.md)。 自動調整規模，即使在高負載期間也會。 Azure Functions 基礎結構會根據傳入的觸發程式事件數目，新增其他的函式主機實例來調整 CPU 和記憶體資源。 | 200 |
| **[進階方案](functions-premium-plan.md)** | [事件驅動](event-driven-scaling.md)。 自動調整規模，即使在高負載期間也會。 Azure Functions 基礎結構會根據觸發函式的事件數目，新增函式主機的其他實例，以調整 CPU 和記憶體資源。 |100|
| **[專用方案](dedicated-plan.md)**<sup>1</sup> | 手動/自動調整 |10-20|
| **[ASE](dedicated-plan.md)**<sup>1</sup> | 手動/自動調整 |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | 使用 [KEDA](https://keda.sh)Kubernetes 叢集的事件驅動自動調整。 | 依叢集而異 &nbsp; &nbsp;&nbsp;&nbsp;|

<sup>1</sup> 針對各種 App Service 方案選項的特定限制，請參閱 [App Service 方案限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

## <a name="cold-start-behavior"></a>冷啟動行為

|    |    | 
| -- | -- |
| **[取用 &nbsp; 方案](consumption-plan.md)** | 當閒置時，應用程式可能會調整為零，表示某些要求可能會在啟動時產生額外的延遲。  取用方案有一些優化有助於減少冷開始時間，包括從已執行函式主機和語言進程的預先準備就緒預留位置函式中提取。 |
| **[進階方案](functions-premium-plan.md)** | 永久暖實例，以避免任何冷啟動。 |
| **[專用方案](dedicated-plan.md)** | 在專用方案中執行時，函式主機可以連續執行，這表示冷啟動不是真正的問題。 |
| **[ASE](dedicated-plan.md)** | 在專用方案中執行時，函式主機可以連續執行，這表示冷啟動不是真正的問題。 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | 根據 KEDA 設定，可以設定應用程式以避免冷啟動。 如果設定為調整為零，則會對新事件進行冷啟動。 

## <a name="service-limits"></a>服務限制

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>網路功能

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>計費

| | | 
| --- | --- |
| **[取用方案](consumption-plan.md)** | 您只需針對函式執行的時間付費。 帳單是根據執行數目、執行時間以及使用的記憶體。 |
| **[進階方案](functions-premium-plan.md)** | Premium 方案是根據所需和預先準備就緒的實例所使用的核心秒數和記憶體數。 每個方案至少有一個實例必須隨時保持暖。 此方案提供最容易預測的定價。 |
| **[專用方案](dedicated-plan.md)* | 如同針對其他 App Service 資源（例如 web 應用程式），您會在 App Service 方案中針對函數應用程式支付相同的費用。|
| **[App Service Environment (ASE)](dedicated-plan.md)** | ASE 的固定每月費率會支付基礎結構，且不會隨著 ASE 的大小而變更。 每 App Service 計畫 vCPU 也會產生費用。 ASE 中裝載的所有應用程式都會位於隔離價格 SKU 中。 |
| **[Kubernetes](functions-kubernetes-keda.md)**| 您只需支付 Kubernetes 叢集的成本;函數沒有額外的計費。 您的函式應用程式會以應用程式工作負載的形式在您的叢集上執行，就像一般的應用程式一樣。 |

## <a name="next-steps"></a>後續步驟

+ [Azure Functions 中的部署技術](functions-deployment-technologies.md) 
+ [Azure Functions 開發人員指南](functions-reference.md)