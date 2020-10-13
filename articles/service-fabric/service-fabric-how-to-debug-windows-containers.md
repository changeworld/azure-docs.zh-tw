---
title: 使用 Service Fabric 和 VS 對 Windows 容器進行偵錯
description: 瞭解如何使用 Visual Studio 2019，在 Azure Service Fabric 中進行 Windows 容器的調試。
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 3e6e7785278b182cebb21115a70f35ade52303c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86247246"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>如何：使用 Visual Studio 2019 在 Azure Service Fabric 中進行 Windows 容器的調試

在 Visual Studio 2019 中，您可以將容器中的 .NET 應用程式作為 Service Fabric 服務進行偵錯工具。 本文說明如何設定環境，然後在執行於本機 Service Fabric 叢集的容器中，對 .NET 應用程式進行偵錯。

## <a name="prerequisites"></a>Prerequisites

* 在 Windows 10 上，請遵循本快速入門來[設定 Windows 10 以執行 Windows 容器](/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* 在 Windows Server 2016 上，請遵循本快速入門來[設定 Windows 2016 以執行 Windows 容器](/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* 遵循[在 Windows 上準備您的開發環境](./service-fabric-get-started.md)，以設定本機 Service Fabric 環境

## <a name="configure-your-developer-environment-to-debug-containers"></a>設定開發人員環境以對容器進行偵錯

1. 先確定適用於 Windows 服務的 Docker 有在執行，再繼續進行下一個步驟。

1. 若要支援容器之間的 DNS 解析，您必須使用電腦名稱稱設定您的本機開發叢集。 如果您想要透過反向 Proxy 處理服務，則也需要執行這些步驟。
   1. 以系統管理員身分開啟 PowerShell
   2. 瀏覽至 SDK 叢集的安裝程式資料夾，通常是 `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`。
   3. 執行指令碼`DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > 您可以使用 `-CreateOneNodeCluster` 來設定單節點叢集。 依預設會建立五節點的本機叢集。
      >

      若要深入了解 Service Fabric 中的 DNS 服務，請參閱 [Azure Service Fabric 中的 DNS 服務](./service-fabric-dnsservice.md)。 若要深入了解如何從在容器中執行的服務使用 Service Fabric 反向 Proxy，請參閱[針對在容器中執行的服務進行反向 Proxy 特殊處理](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers)。

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>在對 Service Fabric 中的容器進行偵錯時的已知限制

以下是對 Service Fabric 中的容器進行偵錯時的已知限制和可能的解決方式清單：

* 使用 localhost for 對 clusterfqdnorip 不支援容器中的 DNS 解析。
    * 解決方式：使用機器名稱設定本機叢集 (請參閱上文)
* 在虛擬機器中執行 Windows10 不會讓 DNS 回復回到容器。
    * 解決方式：對虛擬機器 NIC 上的 IPv4 停用 UDP 總和檢查碼卸載
    * 執行 Windows10 會降低電腦上的網路效能。
    * https://github.com/Azure/service-fabric-issues/issues/1061
* 如果應用程式是使用 Docker Compose 部署，則使用 DNS 服務名稱解析相同應用程式中的服務無法在 Windows10 上運作
    * 解決方式：使用 servicename.applicationname 來解析服務端點
    * https://github.com/Azure/service-fabric-issues/issues/1062
* 如果對 ClusterFQDNorIP 使用 IP-address，則在主機上變更主要 IP 會破壞 DNS 功能。
    * 解決方式：使用主機上的新主要 IP 或使用機器名稱，來重新建立叢集。 這項破壞是設計的。
* 如果建立叢集的 FQDN 無法在網路上解析，DNS 將會失敗。
    * 解決方式：使用主機的主要 IP 來重新建立本機叢集。 這項失敗是設計的。
* 在對容器進行偵錯時，Docker 記錄只能透過 Visual Studio 輸出視窗取得，而無法透過 Service Fabric API (包括 Service Fabric Explorer) 取得

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>針對在 Service Fabric 上的 Docker 容器中執行的 .NET 應用程式進行偵錯

1. 以系統管理員身分執行 Visual Studio。

1. 開啟現有的 .NET 應用程式，或新建一個。

1. 以滑鼠右鍵按一下專案，然後選取 [新增] -> [容器協調器支援] -> [Service Fabric]****

1. 按 **F5** 開始對應用程式進行偵錯工具。

    Visual Studio 支援適用於 .NET 和 .NET Core 的主控台與 ASP.NET 專案類型。

## <a name="next-steps"></a>接下來的步驟
若要深入瞭解 Service Fabric 和容器的功能，請參閱 [Service Fabric 容器總覽](service-fabric-containers-overview.md)。
