---
title: 使用 Azure Service Fabric Explorer 視覺化您的叢集
description: Service Fabric Explorer 是一種應用程式，可檢查和管理 Microsoft Azure Service Fabric 叢集中的雲端應用程式和節點。
author: mikkelhegn
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 80e3d990b6e8026c57ffff0048d0447a95529564
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79258183"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>使用 Service Fabric 總管視覺化叢集

Service Fabric Explorer (SFX) 是開放原始碼工具，可檢查及管理 Azure Service Fabric 叢集。 Service Fabric Explorer 是適用於 Windows、macOS 和 Linux 的桌面應用程式。

## <a name="service-fabric-explorer-download"></a>Service Fabric Explorer 下載

您可以使用下列連結將 Service Fabric Explorer 下載為桌面應用程式：

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Service Fabric Explorer 的桌面版本可能比叢集支援有更多或更少功能。 您可以切換回部署至叢集的 Service Fabric Explorer 版本，以確認完整功能的相容性。
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>從叢集執行 Service Fabric Explorer

Service Fabric Explorer 也裝載於 Service Fabric 叢集的 HTTP 管理端點。 若要在網頁瀏覽器中啟動 SFX，請從任何瀏覽器流覽至叢集的 HTTP 管理端點，例如 HTTPs： \/ /clusterFQDN：19080。

針對開發人員工作站設定，您可以瀏覽至 https://localhost:19080/Explorer，在本機叢集上啟動 Service Fabric Explorer。 請參閱此文章以[準備您的開發環境](service-fabric-get-started.md)。

> [!NOTE]
> 如果您的叢集受到自我簽署憑證保護，您就會從網頁瀏覽器收到「此網站不安全」的錯誤訊息。 在大多數新式網頁瀏覽器中，您可以藉由覆寫警告來直接繼續進行操作。 在生產環境中，應該使用通用名稱和憑證授權單位簽發的憑證來保護您的叢集。 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>連線至 Service Fabric 叢集
若要連線至 Service Fabric 叢集，您需要叢集管理端點 (FQDN/IP) 和 HTTP 管理端點連接埠 (預設為 19080)。 例如 HTTPs \: //mysfcluster.westus.cloudapp.azure.com:19080。 使用 [連線至 localhost] 核取方塊，連線至工作站上的本機叢集。

### <a name="connect-to-a-secure-cluster"></a>連線到安全的叢集
您可以為使用憑證或使用 Azure Active Directory (AAD) 來控制用戶端對您 Service Fabric 叢集的存取。

如果您嘗試連線到安全的叢集，則視叢集的設定而定，您將需要出示用戶端憑證或使用 AAD 登入。

## <a name="understand-the-service-fabric-explorer-layout"></a>了解 Service Fabric 總管配置
您可以使用左邊的樹狀目錄來瀏覽 Service Fabric 總管。 在樹狀目錄的根目錄，叢集儀表板會提供您叢集的概觀，包括應用程式和節點健康情況的摘要。

![Service Fabric 總管叢集儀表板][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>檢視叢集的配置
Service Fabric 叢集中的節點會橫跨容錯網域和升級網域的二維方格放置。 這個位置可確保您的應用程式在發生硬體故障及應用程式升級時仍然可用。 您可以使用叢集對應檢視目前叢集的配置方式。

![Service Fabric 總管叢集對應][sfx-cluster-map]

### <a name="view-applications-and-services"></a>檢視應用程式和服務
叢集包含兩個樹狀子目錄：一個用於應用程式，另一個用於節點。

您可以使用應用程式檢視瀏覽 Service Fabric 的邏輯階層：應用程式、服務、資料分割，以及複本。

在以下範例中，**MyApp** 應用程式是由 **MyStatefulService** 與 **WebService** 兩個服務組成。 由於 **MyStatefulService** 可設定狀態，因此它包含一個具有一個主要複本和兩個次要複本的資料分割。 對比之下，WebSvcService 則無狀態，而且只包含單一執行個體。

![Service Fabric 總管應用程式檢視][sfx-application-tree]

在樹狀目錄的每個層級，主要窗格會顯示項目的相關資訊。 例如，您可以看到特定服務的健康狀態和版本。

![Service Fabric 總管基本資訊窗格][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>檢視叢集的節點
節點檢視會顯示叢集的實體配置。 對於指定的節點，您可以檢查已經在該節點上部署程式碼的應用程式。 更具體地說，您可以看到目前在那裡執行的複本。

## <a name="actions"></a>動作
「Service Fabric 總管」提供一個對您叢集內的節點、應用程式及服務快速叫用動作的方式。

例如，若要刪除應用程式實例，請從左邊的樹狀目錄中選擇應用程式，然後選擇 [**動作**] [  >  **刪除應用程式**]。

![在 Service Fabric 總管中刪除應用程式][sfx-delete-application]

> [!TIP]
> 您可以按下每個元素旁邊的省略符號來執行相同動作。
>
> 可以透過 Service Fabric 總管執行的每個動作也都可以透過 PowerShell 或 REST API 執行，以實現自動化。
>
>

您也可以使用 Service Fabric Explorer，為指定的應用程式類型和版本建立應用程式執行個體。 在樹狀檢視中選擇應用程式類型，然後在右邊窗格中按一下您想要的版本旁邊的 [建立應用程式執行個體] **** 連結。

![在 Service Fabric Explorer 中建立應用程式執行個體][sfx-create-app-instance]

> [!NOTE]
> 建立應用程式執行個體時，Service Fabric Explorer 不支援參數。 應用程式執行個體會使用預設參數值。
>
>

## <a name="event-store"></a>事件存放區
EventStore 是平台所提供的功能，可提供可在 Service Fabric Explorer 中及透過 REST API 取得的 Service Fabric 平台事件。 您可以看到快照集檢視，其中顯示每個實體 (例如節點、服務、應用程式) 在叢集中發生什麼情況，並根據事件的時間進行查詢。 您也可以在 [EventStore 概觀](service-fabric-diagnostics-eventstore.md)進一步了解 EventStore。   

![EventStore][sfx-eventstore]

>[!NOTE]
>截至 Service Fabric 6.4 版為止， EventStore 不是預設啟用的功能，必須在 Resource Manager 範本中啟用

>[!NOTE]
>截至 Service Fabric 6.4 版為止， EventStore API 僅適用於在 Azure 上執行的 Windows 叢集。 我們正在將這個功能和我們的獨立叢集移植到 Linux。

## <a name="image-store-viewer"></a>映射存放區檢視器
映射存放區檢視器是使用原生映射存放區所提供的功能，可讓您查看映射存放區的目前內容，以及取得檔案和資料夾資訊，以及移除檔案/資料夾。

![Service Fabric 總管叢集對應][sfx-imagestore]

## <a name="backup-and-restore"></a>備份與還原
Service Fabric Explorer 提供與[備份和還原](./service-fabric-reliable-services-backup-restore.md)互動的能力。 若要查看 SFX 中的備份和還原功能，必須啟用 [advanced] 模式。

![啟用 advanced 模式][0]
 
可能的作業如下：

* 建立、編輯和刪除備份原則。
* 啟用和停用應用程式、服務或分割區的備份。
* 暫停和繼續應用程式、服務或分割區的備份。
* 觸發和追蹤分割區的備份。
* 觸發和追蹤分割區的還原。

如需備份和還原服務的詳細資訊，請參閱[REST API 參考](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)。
## <a name="next-steps"></a>後續步驟
* [在 Visual Studio 中管理您的 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)
* [使用 PowerShell 部署 Service Fabric 應用程式](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png