---
title: 在 Azure Stack Edge 上部署即時影片分析
description: 本文列出可協助您在 Azure Stack Edge 上部署即時影片分析的步驟。
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: b13bb779a5a780b21f2d5d96ed8831ef5c26564d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933968"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>在 Azure Stack Edge 上部署即時影片分析

本文列出可協助您在 Azure Stack Edge 上部署即時影片分析的步驟。 裝置設定並啟用之後，就可以開始進行即時影片分析部署。 

針對即時影片分析，我們會透過 IoT 中樞進行部署，但 Azure Stack Edge 的資源會公開 Kubernetes API，讓客戶可以部署其他非 IoT 中樞感知解決方案，以與即時影片分析互動。 

> [!TIP]
> 使用 Kubernetes (K8s) API 進行自訂部署是一個 advanced 案例。 建議客戶建立 edge 模組，並透過 IoT 中樞部署到每個 Azure Stack Edge 資源，而不是使用 Kubernetes API。 在本文中，我們將示範使用 IoT 中樞部署即時影片分析模組的步驟。

## <a name="prerequisites"></a>必要條件

* 您擁有擁有者 [許可權](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)的 Azure 訂用帳戶。
* [Azure Stack Edge](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep)資源
   
* [IoT 中樞](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)
* 即時影片分析模組的 [服務主體](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/create-custom-azure-resource-manager-role-how-to#create-service-principal) 。

   使用其中一個可用的 IoT 中樞區域：美國東部2、美國中部、美國中北部、日本東部、美國西部2、美國中西部、加拿大東部、英國南部、法國中部、法國南部、瑞士北部、瑞士西部和日本西部。
* 儲存體帳戶

    建議您使用一般用途 v2 (GPv2) 儲存體帳戶。  
    深入瞭解 [一般用途 v2 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade?tabs=azure-portal)。
* 開發電腦上有 [Visual Studio Code](https://code.visualstudio.com/)。 請確定您有 [Azure IoT Tools 延伸模組](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)。
* 確定開發電腦所連線的網路允許透過連接埠 5671 的進階訊息佇列通訊協定。 此設定可讓 Azure IoT Tools 與 Azure IoT 中樞通訊。

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>設定使用即時影片分析的 Azure Stack Edge

Azure Stack Edge 是具有網路資料傳輸功能的硬體即服務解決方案，以及具備 AI 功能的邊緣計算裝置。 深入瞭解 [Azure Stack Edge 和詳細的安裝指示](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)。 若要開始使用，請遵循下列連結中的指示：

* [Azure Stack Edge/資料箱閘道資源建立](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-prep)
* [安裝和設定](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-install)
* [連接與啟用](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate)

### <a name="attach-an-iot-hub-to-azure-stack-edge"></a>將 IoT 中樞連結至 Azure Stack Edge

1. 在 [Azure 入口網站](https://ms.portal.azure.com)中，移至您的 Azure Stack Edge 資源，然後按一下 [總覽]。 在右窗格的 [計算] 圖格上，選取 [開始使用]。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge.png" alt-text="Azure Stack Edge":::
1. 在 [設定 Edge 計算] 圖格上，選取 [設定計算]。
1. 在 [設定 Edge 計算] 刀鋒視窗上，輸入下列資訊：
    
    | 欄位|值|
    |---|---|
    |IoT 中樞|選擇 [新增] 或 [現有]。<br/>根據預設，標準層 (S1) 用來建立 IoT 資源。 若要使用免費層 IoT 資源，請建立一個資源，然後選取現有的資源。<br/>在每個案例中，IoT 中樞資源都會使用 Azure Stack Edge 資源所使用的相同訂用帳戶和資源群組。|
    |名稱|輸入 IoT 中樞資源的名稱。|

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-get-started.png" alt-text="Azure Stack Edge 開始使用":::
1. 選取 [建立]。 建立 IoT 中樞資源需要幾分鐘的時間。 建立 IoT 中樞資源之後，[設定計算]  圖格會更新以顯示計算組態。 若要確認已設定 Edge 計算角色，請選取 [設定計算]  圖格上的 [檢視計算]  。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/edge-compute-config.png" alt-text="建立 IoT 中樞資源":::

    > [!NOTE]
    > 如果在 IoT 中樞與 Azure Stack Edge 資源相關聯之前關閉 [設定計算] 對話方塊，則會建立 IoT 中樞，但不會顯示在計算設定中。 在幾分鐘後重載頁面，並看到它出現。
    
    在 Edge 裝置上設定 Edge 計算角色時，其會建立兩個裝置：一個 IoT 裝置和一個 IoT Edge 裝置。 這兩個裝置都可以在 IoT 中樞資源中檢視。 IoT Edge 執行時間也在 IoT Edge 裝置上執行。 目前只有 Linux 平台適用於您的 IoT Edge 裝置。
    
    填妥所有資訊之後，您會看到「設定 Edge 計算卡」，如下所示：
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/configure-edge-compute.png" alt-text="設定 Edge 計算卡片 ":::
 
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>在 Azure Stack Edge 本機 UI 上啟用計算必要條件

繼續之前，請確定：

* 您已啟用您的 Azure Stack Edge 資源。
* 您可以存取執行 PowerShell 5.0 或更新版本的 Windows 用戶端系統，以存取 Azure Stack Edge 資源。
* 若要部署 Kubernetes 叢集，您必須透過其 [本機 WEB UI](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-connect-setup-activate#connect-to-the-local-web-ui-setup)來設定您的 Azure Stack Edge 資源。 
    
    * 若要啟用計算，請在裝置的本機 web UI 中，移至 [計算] 頁面。
    
        * 選取您要為計算啟用的網路介面。 選取 [啟用]。 在該網路介面上的裝置上建立虛擬交換器時，啟用計算結果。
        * 將 Kubernetes test node Ip 和 Kubernetes external services Ip 保持空白。
        * 選取套用-此操作大約需要2分鐘。
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text=" Azure Stack Edge 本機 UI 上的計算必要條件":::

        * 如果未針對 Kubernetes API 和 Azure Stack Edge 資源設定 DNS，您可以更新視窗的主機檔案。
        
            * 以系統管理員身分開啟文字編輯器
            * 開啟檔案 ' to C:\Windows\System32\drivers\etc\hosts '
            * 將 Kubernetes API 裝置名稱的 IPv4 和主機名稱新增至檔案。  (這項資訊可在 Azure Stack Edge 入口網站的 [裝置] 區段下找到。 ) 
            * 儲存並關閉

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>使用 Azure 入口網站部署即時影片分析 Edge 模組

因此，我們只會從透過 [IoT 中樞部署即時影片分析](deploy-iot-edge-device.md)，採取特定步驟。

1. 略過使用者和群組建立步驟，並移至 [部署即時影片分析 Edge](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) 模組。 遵循此處所述的步驟。
1. 在 [容器建立選項] 中，您不需要設定環境變數。 因此，請略過此步驟。
1. 開啟 [容器建立選項] 索引標籤。

   * 將下列 JSON 複製並貼到 box 中，以限制模組所產生的記錄檔大小。
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > 如果搭配使用 gRPC 通訊協定與共享記憶體傳輸，請使用主機 IPC 模式來進行即時影片分析和推斷解決方案之間的共用記憶體存取。
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > JSON 中的「系結」區段有2個專案。 您可以隨意更新 edge 裝置系結，但請確定這些目錄存在。
    
    * "/var/lib/azuremediaservices：/var/lib/azuremediaservices"：這是用來系結容器中的持續性應用程式設定資料，並將它儲存在 edge 裝置上。
    * "/var/media：/var/media"：這會系結 edge 裝置與容器之間的媒體檔案夾。 當您執行可支援在 edge 裝置上儲存影片剪輯的 media graph 拓撲時，會使用此功能來儲存影片錄製。
        
1. 繼續檔中的步驟，並填寫模組對應項設定。
1. 選取 **[下一步**：路由] 以繼續前往 [路由] 區段。 指定路由。

    保留預設路由，然後選取 [下一步：檢查 + 建立] 以繼續進行審核區段。
1. [檢查並驗證您的部署](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a> (選用的) 設定 Docker 磁片區裝載

如果您想要查看工作目錄中的資料，請遵循下列步驟，在部署之前先設定 Docker 磁片區掛接。 

這些步驟涵蓋如何建立閘道使用者，以及如何設定檔案共用來查看即時影片分析工作目錄和即時影片分析媒體檔案夾的內容。

> [!NOTE]
> 支援系結裝載，但磁片區裝載允許資料可供查看，且需要遠端複製。 您可以同時使用 Bind 和磁片區裝載，但無法指向相同的容器路徑。

1. 開啟 Azure 入口網站並移至 Azure Stack Edge 資源。
1. 建立可存取共用的 **閘道使用者** 。
    
    1. 在左側流覽窗格中，按一下 [ **閘道->使用者**]。
    1. 按一下 [ **+ 新增使用者** ]，設定使用者名稱和密碼。 建議 (： `lvauser`) 。
    1. 按一下 [ **新增**]。
    
1. 建立即時影片分析持續性的 **本機共用** 。

    1. 按一下 [ **閘道->共用**]。
    1. 按一下 [ **+ 新增共用**]。
    1. 設定共用名稱。 建議 (： `lva`) 。
    1. 將共用類型保留為 SMB。
    1. 確定已核取 **[使用具有 Edge 計算的共用** ]。
    1. 確定已核取 [ **設定為 Edge 本機共用** ]。
    1. 在 [使用者詳細資料] 中，將共用的存取權授與最近建立的使用者。
    1. 按一下 [ **建立**]。
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="本機共用":::
    
1. 建立檔案同步儲存的遠端共用。

    1. 先在相同區域中建立 blob 儲存體帳戶。
    1. 按一下 [ **閘道->共用**]。
    1. 按一下 [ **+ 新增共用**]。
    1. 設定共用名稱。 建議 (：媒體) 。
    1. 將共用類型保留為 SMB。
    1. 確定已核取 **[使用具有 Edge 計算的共用** ]。
    1. 確定未核取 [ **設定為 Edge 本機共用** ]。
    1. 選取最近建立的儲存體帳戶。
    1. 設定容器名稱。
    1. 將儲存體類型設定為區塊 Blob。
    1. 在 [使用者詳細資料] 中，將共用的存取權授與最近建立的使用者。
    1. 按一下 [ **建立**]。    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="遠端共用":::
    
    > [!TIP]
    > 使用與您的 Azure Stack Edge 連線的 Windows 用戶端，請遵循 [本檔中所述](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share)的步驟連接到 SMB 共用。
    
1. 更新即時影片分析 Edge 模組的容器建立選項 (請參閱 [新增模組檔](deploy-iot-edge-device.md#add-modules)) 中的點4，以使用磁片區裝載。

   ```json
    // Original (Bind Mounts)
    "createOptions": {
        "HostConfig": {
            "Binds": [
                "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
                "/var/media:/var/media"
            ]
        }
    }
    // Updated (Volume Mounts)
    "createOptions": {
        "HostConfig": {
            "Mounts": [
            {
                "Target": "/var/lib/azuremediaservices",
                "Source": "lva",
                "Type": "volume"
            },
            {
                "Target": "/var/media",
                "Source": "media",
                "Type": "volume"
            }]
        }
    }
    ```

### <a name="verify-that-the-module-is-running"></a>確認模組正在執行

最後一個步驟是確定模組已如預期般連線並執行。 在 IoT 中樞資源中，模組應處於為您的 IoT Edge 裝置執行中的執行階段狀態。

若要確認模組正在執行中，請執行下列步驟：

1. 在 Azure 入口網站中，返回 Azure Stack Edge 資源
1. 選取 [模組] 磚。 這會帶您前往 [模組] 刀鋒視窗。 在模組清單中，找出您所部署的模組。 您新增的模組應處於「執行中」的執行階段狀態。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="自訂模組":::

### <a name="configure-the-azure-iot-tools-extension"></a>設定 Azure IoT Tools 延伸模組

請遵循下列指示，使用 Azure IoT Tools 延伸模組來連線到您的 IoT 中樞。

1. 在 Visual Studio Code 中，選取 [View > Explorer]。 或選取 Ctrl+Shift+E。
1. 在 [總管] 索引標籤的左下角，選取 [Azure IoT 中樞]。
1. 選取 [其他選項] 圖示，以查看捷徑功能表。 然後選取 [設定 IoT 中樞連接字串]。
1. 輸入方塊出現時，請輸入您的 IoT 中樞連接字串。 

   * 若要取得連接字串，請移至 Azure 入口網站中的 IoT 中樞，然後按一下左側流覽窗格中的 [共用存取原則]。
   * 按一下 [iothubowner 取得共用存取金鑰]。
   * 複製 [連接字串–主要金鑰]，並將它貼到 VSCode 的輸入方塊中。

   連接字串看起來會像這樣：<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   如果連線成功，便會出現邊緣裝置清單。 您應該會看到您的 Azure Stack Edge。 您現在可以透過捷徑功能表來管理 IoT Edge 裝置，並與 Azure IoT 中樞互動。 若要查看在 edge 裝置上部署的模組，請在 Azure Stack 裝置下展開 [模組] 節點。
    
## <a name="troubleshooting"></a>疑難排解

* Kubernetes API 存取 (kubectl) 。

    * 遵循檔來設定您的電腦，以 [存取 Kubernetes](https://review.docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-create-kubernetes-cluster?toc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Ftoc.json&bc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Fbreadcrumb%2Ftoc.json&branch=release-tzl#debug-kubernetes-issues)叢集。
    * 所有已部署的 IoT Edge 模組都會使用 `iotedge` 命名空間。 使用 kubectl 時，請務必包含該的。
* 模組記錄

    `iotedge`無法存取工具來取得記錄。 您必須使用 [kubectl 記錄](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)  檔來查看記錄檔或傳送至檔案的管道。 範例： <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`
* Pod 和節點計量

    使用 [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)  來查看 pod 和節點計量。  (這項功能將在下一個 Azure Stack Edge 版本中提供。 >v2007) <br/>`kubectl top pods -n iotedge`
* 模組網路上的模組探索 Azure Stack Edge 需要模組在 createOptions 中有主機埠系結。 然後就可以將模組定址 `moduleName:hostport` 。
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* 磁片區裝載

    如果容器嘗試將磁片區掛接到現有和非空白的目錄，模組將無法啟動。
* 共用記憶體

    使用主機 IPC，可在任何命名空間中的 pod 之間支援 Azure Stack Edge 資源上的共用記憶體。
    設定 edge 模組上的共用記憶體，以透過 IoT 中樞進行部署。
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    (Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API.
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
*  (Advanced) Pod 共同位置

    使用 K8s 來部署透過 gRPC 與即時影片分析進行通訊的自訂推斷解決方案時，您必須確定 pod 部署在與即時影片分析模組相同的節點上。

    * 選項 1-使用節點親和性和內建節點標籤來進行共置。

    目前 >nodeselector 的自訂設定似乎不是選項，因為使用者沒有存取權可以設定節點上的標籤。 不過，根據客戶的拓撲和命名慣例，他們可能可以使用 [內建的節點標籤](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)。 參考具有即時影片分析 Azure Stack Edge 資源的 nodeAffinity 區段可以新增至推斷 pod 資訊清單，以達成共置位置。
    * 選項 2-將 Pod 親和性用於共置 (建議的) 。
Kubernetes 具有 [Pod 親和性](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)  的支援，可在相同的節點上排程 Pod。 參考即時影片分析模組的 podAffinity 區段可以新增至推斷 pod 資訊清單，以達成共置位置。

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```

## <a name="next-steps"></a>下一步

您可以使用模組，藉由叫用直接方法來分析即時影片串流。 在模組上叫用[直接方法](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls)。
