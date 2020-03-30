---
title: 將開發電腦連接到 AKS 群集的工作原理
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 描述使用 Azure 開發人員空間將開發電腦連接到 Azure Kubernetes 服務群集的過程
keywords: Azure 開發空間、開發空間、Docker、庫伯奈斯、Azure、AKS、Azure 庫伯奈斯服務、容器
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241708"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>將開發電腦連接到 AKS 群集的工作原理

使用 Azure 開發人員空間，可以將開發電腦連接到 AKS 群集，從而可以在開發電腦上運行和調試代碼，就像它在群集上運行一樣。 Azure 開發人員空間通過在群集上運行充當遠端代理的 Pod 來重定向連接的 AKS 群集之間的流量，從而在開發電腦和群集之間重定向流量。 此流量重定向允許開發電腦上的代碼和在 AKS 群集中運行的服務進行通信，就像它們位於同一 AKS 群集中一樣。 此連接還允許您在開發電腦上運行和調試具有或不帶容器的代碼。 將開發電腦連接到群集可説明您快速開發應用程式並執行端到端測試。

## <a name="connecting-to-your-cluster"></a>連接到群集

使用[視覺化工作室代碼][vs-code]連接到現有的 AKS 群集，在 MacOS 或 Windows 10 上安裝了[Azure 開發空間][azds-vs-code]擴展。 建立連接時，可以選擇將所有流量重定向到群集中的新或現有窗格到開發電腦。

> [!NOTE]
> 使用 Visual Studio 代碼連接到群集時，Azure 開發人員空間擴展提供了將服務重定向到開發電腦的選項。 此選項是標識用於重定向的窗格的便捷方法。 AKS 群集和開發電腦之間的所有重定向都用於 pod。

連接到群集不需要在群集上啟用 Azure 開發空間。 相反，當 Azure 開發人員空間擴展建立到群集的連接時，它會：

* 將 AKS 群集上的窗格中的容器替換為遠端代理容器，該容器將流量重定向到開發電腦。 重定向新窗格時，Azure 開發人員空間會使用遠端代理在 AKS 群集中創建新的 Pod。
* 在開發電腦上運行[kubectl 埠轉發][kubectl-port-forward]，將流量從開發電腦轉發到群集中運行的遠端代理。
* 使用遠端代理從群集收集環境資訊。 此環境資訊包括環境變數、可見服務、卷裝載和機密裝載。
* 在 Visual Studio Code 終端中設置環境，以便開發電腦上的服務可以訪問與在群集上運行相同的變數。  
* 更新主機檔，將 AKS 群集上的服務映射到開發電腦上的本地 IP 位址。 這些主機檔條目允許開發電腦上運行的代碼向群集中運行的其他服務發出請求。 要更新主機檔，Azure 開發人員空間將在連接到群集時請求開發電腦上的管理員存取權限。

如果群集上確實啟用了 Azure 開發空間，則還可以選擇使用[Azure 開發空間提供的流量重定向][how-it-works-routing]。 Azure 開發空間提供的流量重定向允許您連接到在子開發空間中運行的服務的副本。 使用子開發空間可説明您避免中斷在父開發空間中工作的其他人，因為您只是重定向針對子空間的服務實例的流量，使服務的父空間實例未修改。

連接到群集後，無論開發電腦上是否運行服務，流量都會路由到開發電腦。

## <a name="running-code-on-your-development-computer"></a>在開發電腦上運行代碼

建立與 AKS 群集的連接後，可以在電腦上以本機方式運行任何代碼，而無需容器化。 遠端代理接收的任何網路流量都會重定向到連接期間指定的本地埠，以便本機運行的代碼可以接受和處理該流量。 群集中的環境變數、卷和機密可用於在開發電腦上運行的代碼。 此外，由於 Azure Dev Space 向開發人員電腦添加了主機檔條目和埠轉發，代碼可以使用群集中的服務名稱將網路流量發送到群集上運行的服務，並且該流量將轉發到群集中運行的服務。

由於代碼在開發電腦上運行，因此您可以靈活地使用開發中通常使用的任何工具來運行代碼並對其進行調試。 在整個連線時間，流量在開發電腦和群集之間路由。 此持久連接允許您根據需要啟動、停止和重新開機代碼，而無需重新建立連接。

此外，Azure 開發人員空間提供了一種通過*azds-local.env*檔案複製開發電腦 AKS 群集中可用於 POD 的環境變數和裝載的檔的方法。 您還可以使用此檔創建新的環境變數和卷裝載。

## <a name="additional-configuration-with-azds-localenv"></a>附加配置與 azds-local.env

*azds-local.env*檔允許您複製 AKS 群集中可供窗格使用的環境變數和裝載的檔。 您可以在*azds-local.env*檔中指定以下操作：

* 下載卷並將該卷的路徑設置為環境變數。
* 從卷下載單個檔或檔集，並將其裝載到開發電腦上。
* 無論您連接到哪個群集，都能使用服務。

下面是*一個示例 azds-local.env*檔：

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}

# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

不會自動創建預設*的 azds-local.env*檔，因此您必須在專案的根目錄上手動創建該檔。

## <a name="diagnostics-and-logging"></a>診斷和記錄

連接到 AKS 群集時，群集的診斷日誌將記錄到開發電腦[的臨時目錄][azds-tmp-dir]。 使用 Visual Studio 代碼，您還可以使用 *"顯示診斷資訊"* 命令從 AKS 群集列印當前環境變數和 DNS 條目。

## <a name="next-steps"></a>後續步驟

要開始將本地開發電腦連接到 AKS 群集，請參閱[將開發電腦連接到 AKS 群集][connect]。

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
