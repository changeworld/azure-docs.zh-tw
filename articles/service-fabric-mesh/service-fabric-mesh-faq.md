---
title: Azure 服務結構網格的常見問題
description: 了解 Azure Service Fabric Mesh 的常見問題和解答。
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 2a5c2ea63d162eb6fb78ab702e0519f8ac25dcc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252499"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Service Fabric Mesh 的常見問題

Azure Service Fabric Mesh 是一個受到完整管理的服務，讓開發人員能夠部署微服務應用程式，而不需管理虛擬機器、儲存體或網路功能。 本文提供常見問題的解答。

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>如何回報問題或詢問問題？

在 [service-fabric-mesh-preview GitHub 存放庫](https://aka.ms/sfmeshissues) \(英文\) 中提出問題、獲得 Microsoft 工程師所提供的解答，以及回報問題。

## <a name="quota-and-cost"></a>配額和成本

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>參與預覽版的成本為何？

當前將應用程式或容器部署到網格預覽不收取任何費用。 請留意 5 月的更新，以便啟用計費。 但是，我們鼓勵您刪除部署的資源，除非正在積極測試這些資源，否則不要將其保留。

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>核心和 RAM 數目是否有配額限制？

是。 每個訂用帳戶的配額如下：

- 申請數量： 5
- 每個應用程式的內核： 12
- 每個應用程式的總 RAM：48 GB
- 網路和入口端點： 5
- 可以附加的 Azure 卷：10
- 服務副本數： 3
- 您可以部署的最大容器限制為 4 個核心和 16 GB 的 RAM。
- 您可以將部分核心配置給容器，遞增量為 0.5 個核心，最多 6 個核心。

### <a name="how-long-can-i-leave-my-application-deployed"></a>我可以讓應用程式維持部署狀態幾天？

我們目前將應用程式的存留期限制為兩天。 這是為了充分運用配置給預覽版的可用核心。 因此，只允許您持續執行指定的部署 48 小時，之後將會關閉該部署。

如果確實發生此情況，可以藉由在 Azure CLI 中執行 `az mesh app show` 命令，以驗證系統是否將它關閉。 請檢查是否傳回 `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

例如： 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

若要刪除資源群組，請使用 `az group delete <nameOfResourceGroup>` 命令。

## <a name="deployments"></a>部署

### <a name="what-container-images-are-supported"></a>支援哪些容器映射？

如果您是在 Windows Fall Creators Update (1709 版) 機器上進行開發，則只能使用 Windows 1709 版 Docker 映像。

如果您是在 Windows 10 April 2018 update (1803 版) 機器上進行開發，則可以使用 Windows 1709 版或 Windows 1803 版的 Docker 映像。

可以使用下列容器 OS 映像部署服務：
- Windows：windowsservercore 和 nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - 視窗伺服器 1809
    - 視窗伺服器 2019 LTSC
- Linux
    - 沒有已知的限制

> [!NOTE]
> 網格的視覺化工作室工具尚不支援部署到 Windows Server 2019 和 1809 容器中。

### <a name="what-types-of-applications-can-i-deploy"></a>我可以部署哪些類型的應用程式？ 

可以部署任何在容器中運行的容器，這些容器都適合對應用程式資源施加的限制（有關配額的詳細資訊，請參閱上文）。 如果我們檢測到您使用 Mesh 運行非法工作負載或濫用系統（即挖掘），則我們保留終止部署和阻止訂閱在服務上運行的權利。 如果您對運行特定工作負載有任何疑問，請聯繫我們。 

## <a name="developer-experience-issues"></a>開發人員體驗問題

### <a name="dns-resolution-from-a-container-doesnt-work"></a>來自容器的 DNS 解析無法運作

從容器連出到 Service Fabric DNS 服務的 DNS 查詢在某些情況下可能會失敗。 我們正在調查此問題。 減輕問題：

- 使用 Windows Fall Creators Update (1709版) 或更新版本作為基礎容器映像。
- 如果服務名稱本身不起作用，請嘗試完全限定的名稱：服務名稱.應用程式名稱。
- 在您服務的 Docker 檔案中新增 `EXPOSE <port>`，其中的 port 是您公開服務的連接埠。 例如：

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS 的運作方式和在 Service Fabric 開發叢集與 Mesh 中不一樣

您可能需要在您的本機開發叢集中以及 Azure Mesh 中以不同方式參考服務。

在您的本機開發叢集中，請使用 `{serviceName}.{applicationName}`。 在 Azure Service Fabric Mesh 中，請使用 `{servicename}`。 

Azure Mesh 目前不支援跨應用程式的 DNS 解析。

有關在 Windows 10 上運行服務結構開發群集的其他已知 DNS 問題，請參閱：[調試 Windows 容器](/azure/service-fabric/service-fabric-how-to-debug-windows-containers)和已知的 DNS[問題](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues)。

### <a name="networking"></a>網路

在本機電腦上執行應用程式時，ServiceFabric 網路 NAT 可能會消失。 若要診斷是否發生此問題，請從命令提示字元執行以下命令：

`docker network ls` 並記下是否列出 `servicefabric_nat`。  如果沒有，請執行以下命令：`docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

即使應用程式已經在本機部署且狀況不良，這樣做也可以解決此問題。

### <a name="issues-running-multiple-apps"></a>執行多個應用程式的問題

您可能會遇到跨所有應用程式修正 CPU 可用性與限制。 減輕問題：
- 建立一個五節點叢集。
- 跨已部署的所有應用程式，降低服務的 CPU 使用量。 例如，在服務的 service.yaml 檔案中，將 `cpu: 1.0` 變更為 `cpu: 0.5`

您無法將多個應用程式部署到一個單一節點叢集。 減輕問題：
- 將多個應用程式部署至本機叢集時，請使用五節點叢集。
- 移除您目前沒有在測試的應用程式。

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>VS 工具對 Windows 容器的支援有限

Visual Studio 工具僅支援部署具有 Windows Server 1709 和 1803 基本作業系統版本的 Windows 容器。 

## <a name="feature-gaps-and-other-known-issues"></a>功能差距和其他已知問題

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>部署應用程式之後，與它相關聯的網路資源沒有 IP 位址

有一個已知問題 IP 位址不會立即變成可用的已知問題。 在幾分鐘內檢查網路資源的狀態，以查看相關聯的 IP 位址。

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>我的應用程式無法存取正確的網路/磁碟區資源

請在您的應用程式模型中，使用網路和磁碟區的完整資源識別碼，才能存取相關聯的資源。 以下是來自快速入門範例的範例：

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>當我相應放大時，所有容器都受到影響，包括正在執行的容器

這是一個錯誤 (bug)，目前正在實作修正程式。

## <a name="next-steps"></a>後續步驟

要瞭解有關服務結構網格的詳細資訊，請閱讀[概述](service-fabric-mesh-overview.md)。
