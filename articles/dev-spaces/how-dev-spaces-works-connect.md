---
title: 將您的開發電腦連接到 AKS 叢集的運作方式
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: 說明使用 Azure Dev Spaces 將您的開發電腦連接到 Azure Kubernetes Service 叢集的處理常式
keywords: Azure Dev Spaces，Dev Spaces，Docker，Kubernetes，Azure，AKS，Azure Kubernetes Service，容器
ms.openlocfilehash: a74a5a623006ccd64441023c2c4bc9ad3dcb517e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241708"
---
# <a name="how-connecting-your-development-computer-to-your-aks-cluster-works"></a>將您的開發電腦連接到 AKS 叢集的運作方式

有了 Azure Dev Spaces，您就可以將開發電腦連接到 AKS 叢集，讓您在開發電腦上執行和偵錯工具代碼，就像是在叢集上執行一樣。 Azure Dev Spaces 會藉由在叢集上執行 pod 做為遠端代理程式，在您的開發電腦與叢集之間重新導向流量，以重新導向已連線 AKS 叢集之間的流量。 此流量重新導向可讓您的開發電腦上執行的程式碼和在 AKS 叢集中執行的服務進行通訊，就好像它們位於相同的 AKS 叢集中一樣。 此連接也可讓您在開發電腦上，使用或不使用容器來執行和偵錯工具代碼。 將您的開發電腦連接到您的叢集，可協助您快速開發應用程式，並執行端對端測試。

## <a name="connecting-to-your-cluster"></a>連接到您的叢集

您可以使用已安裝于 MacOS 或 Windows 10 的[Azure Dev Spaces][azds-vs-code]延伸模組[Visual Studio Code][vs-code] ，連線到現有的 AKS 叢集。 當您建立連線時，可以選擇將叢集中新的或現有 pod 的所有流量重新導向至您的開發電腦。

> [!NOTE]
> 使用 Visual Studio Code 連接到您的叢集時，Azure Dev Spaces 延伸模組可讓您選擇將服務重新導向至您的開發電腦。 此選項是識別重新導向之 pod 的便利方式。 AKS 叢集與開發電腦之間的所有重新導向都適用于 pod。

連接到您的叢集不需要在叢集上啟用 Azure Dev Spaces。 相反地，當 Azure Dev Spaces 擴充功能建立與叢集的連線時，它會：

* 以將流量重新導向至您的開發電腦的遠端代理程式容器，取代 AKS 叢集上 pod 中的容器。 重新導向新的 pod 時，Azure Dev Spaces 會使用遠端代理程式在 AKS 叢集中建立新的 pod。
* 在您的開發電腦上執行[kubectl 埠轉送][kubectl-port-forward]，以將流量從您的開發電腦轉送到叢集中正在執行的遠端代理程式。
* 使用遠端代理程式，從您的叢集收集環境資訊。 此環境資訊包括環境變數、可見服務、磁片區掛接和密碼裝載。
* 在 Visual Studio Code 終端機中設定環境，讓開發電腦上的服務可以存取與在叢集上執行相同的變數。  
* 更新您的 hosts 檔案，將 AKS 叢集上的服務對應至開發電腦上的本機 IP 位址。 這些裝載的檔案專案允許在您的開發電腦上執行的程式碼，對在您的叢集中執行的其他服務提出要求。 若要更新您的主機檔案，Azure Dev Spaces 在連線到您的叢集時，會要求您的開發電腦上具有系統管理員存取權。

如果您已在叢集上啟用 Azure Dev Spaces，您也可以選擇使用 Azure Dev Spaces 所提供的[流量][how-it-works-routing]重新導向。 Azure Dev Spaces 所提供的「流量重新導向」，可讓您連接到在子開發人員空間中執行的服務複本。 使用子開發人員空間可協助您避免中斷父開發人員空間中的其他工作，因為您只會將目標設為服務之子空間實例的流量重新導向，而讓服務的父空間實例未經修改。

連線到您的叢集之後，無論您的開發電腦上是否有執行服務，都會將流量路由傳送至您的開發電腦。

## <a name="running-code-on-your-development-computer"></a>在您的開發電腦上執行程式碼

建立與 AKS 叢集的連線之後，您可以在電腦上以原生方式執行任何程式碼，而不需要容器化。 遠端代理程式接收的任何網路流量都會重新導向至連線期間指定的本機埠，讓您的原生執行程式碼可以接受並處理該流量。 您的叢集環境變數、磁片區和密碼可供在開發電腦上執行的程式碼使用。 此外，由於 Azure Dev Spaces 將主機檔案專案和埠轉送新增至您的開發人員電腦，因此您的程式碼可以使用叢集的服務名稱，將網路流量傳送至叢集上執行的服務，並將流量轉送到叢集中正在執行的服務。

由於您的程式碼是在開發電腦上執行，因此您可以彈性地使用您平常用於開發的任何工具來執行您的程式碼並加以調試。 您的開發電腦與叢集之間的流量會在您連線的整個時間進行路由。 這個持續性連接可讓您視需要啟動、停止和重新開機程式碼，而不必重新建立連接。

此外，Azure Dev Spaces 提供一種方法，可透過*azds-local* ，將環境變數和掛接的檔案複寫到開發電腦的 AKS 叢集中的 pod。 您也可以使用這個檔案來建立新的環境變數和磁片區裝載。

## <a name="additional-configuration-with-azds-localenv"></a>使用 azds-local 的其他設定

*Azds-local*檔案可讓您將環境變數和掛接的檔案複寫至 AKS 叢集中的 pod。 您可以在*azds-local 的 env*檔案中指定下列動作：

* 下載磁片區，並將該磁片區的路徑設定為環境變數。
* 從磁片區下載個別檔案或一組檔案，並將它裝載在您的開發電腦上。
* 無論您連線的叢集為何，皆可使用服務。

以下是範例*azds-local*檔案：

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

預設*azds-local*不會自動建立，因此您必須在專案的根目錄手動建立檔案。

## <a name="diagnostics-and-logging"></a>診斷和記錄

連線到您的 AKS 叢集時，叢集的診斷記錄會記錄到開發電腦的[臨時目錄][azds-tmp-dir]中。 使用 Visual Studio Code，您也可以使用 [*顯示診斷資訊*] 命令，從您的 AKS 叢集中列印目前的環境變數和 DNS 專案。

## <a name="next-steps"></a>後續步驟

若要開始將您的本機開發電腦連接到 AKS 叢集，請參閱[將您的開發電腦連線到 AKS][connect]叢集。

[azds-tmp-dir]: troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[connect]: how-to/connect.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[vs-code]: https://code.visualstudio.com/download
