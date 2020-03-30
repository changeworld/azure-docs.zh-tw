---
title: 使用 Service Fabric 和 VS 對 Windows 容器進行偵錯
description: 瞭解如何使用 Visual Studio 2019 在 Azure 服務結構中調試 Windows 容器。
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 2a00a352d09562ffe46dc8e6e63a5d4963ac3a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127626"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>如何：使用 Visual Studio 2019 調試 Azure 服務結構中的 Windows 容器

借助 Visual Studio 2019，您可以將 .NET 應用程式調試到容器中作為服務交換矩陣服務。 本文說明如何設定環境，然後在執行於本機 Service Fabric 叢集的容器中，對 .NET 應用程式進行偵錯。

## <a name="prerequisites"></a>Prerequisites

* 在 Windows 10 上，請遵循本快速入門來[設定 Windows 10 以執行 Windows 容器](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* 在 Windows Server 2016 上，請遵循本快速入門來[設定 Windows 2016 以執行 Windows 容器](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* 遵循[在 Windows 上準備您的開發環境](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)，以設定本機 Service Fabric 環境

## <a name="configure-your-developer-environment-to-debug-containers"></a>設定開發人員環境以對容器進行偵錯

1. 先確定適用於 Windows 服務的 Docker 有在執行，再繼續進行下一個步驟。

1. 要支援容器之間的 DNS 解析，您必須使用電腦名稱稱設置本地開發群集。 如果您想要透過反向 Proxy 處理服務，則也需要執行這些步驟。
   1. 以系統管理員身分開啟 PowerShell
   2. 瀏覽至 SDK 叢集的安裝程式資料夾，通常是 `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`。
   3. 執行指令碼`DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > 您可以使用 `-CreateOneNodeCluster` 來設定單節點叢集。 依預設會建立五節點的本機叢集。
      >

      若要深入了解 Service Fabric 中的 DNS 服務，請參閱 [Azure Service Fabric 中的 DNS 服務](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice)。 若要深入了解如何從在容器中執行的服務使用 Service Fabric 反向 Proxy，請參閱[針對在容器中執行的服務進行反向 Proxy 特殊處理](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers)。

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>在對 Service Fabric 中的容器進行偵錯時的已知限制

以下是對 Service Fabric 中的容器進行偵錯時的已知限制和可能的解決方式清單：

* 將本地主機用於群集FQDNorIP不支援容器中的 DNS 解析。
    * 解決方式：使用機器名稱設定本機叢集 (請參閱上文)
* 在虛擬機器中運行 Windows10 不會將 DNS 答覆回復回容器。
    * 解決方式：對虛擬機器 NIC 上的 IPv4 停用 UDP 總和檢查碼卸載
    * 運行 Windows10 會降低電腦上的網路性能。
    * https://github.com/Azure/service-fabric-issues/issues/1061
* 使用 DNS 服務名稱解析同一應用程式中的服務在 Windows10 上不起作用，如果應用程式使用 Docker Compose 部署
    * 解決方式：使用 servicename.applicationname 來解析服務端點
    * https://github.com/Azure/service-fabric-issues/issues/1062
* 如果對 ClusterFQDNorIP 使用 IP-address，則在主機上變更主要 IP 會破壞 DNS 功能。
    * 解決方式：使用主機上的新主要 IP 或使用機器名稱，來重新建立叢集。 這種破損是設計造成的。
* 如果使用創建的群集的 FQDN 在網路上無法解析，DNS 將失敗。
    * 解決方式：使用主機的主要 IP 來重新建立本機叢集。 此故障是設計故障。
* 在對容器進行偵錯時，Docker 記錄只能透過 Visual Studio 輸出視窗取得，而無法透過 Service Fabric API (包括 Service Fabric Explorer) 取得

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>針對在 Service Fabric 上的 Docker 容器中執行的 .NET 應用程式進行偵錯

1. 以系統管理員身分執行 Visual Studio。

1. 開啟現有的 .NET 應用程式，或新建一個。

1. 以滑鼠右鍵按一下專案，然後選取 [新增] -> [容器協調器支援] -> [Service Fabric]****

1. 按**F5**開始調試應用程式。

    Visual Studio 支援適用於 .NET 和 .NET Core 的主控台與 ASP.NET 專案類型。

## <a name="next-steps"></a>後續步驟
要瞭解有關服務結構和容器功能的詳細資訊，請參閱[服務結構容器概述](service-fabric-containers-overview.md)。
