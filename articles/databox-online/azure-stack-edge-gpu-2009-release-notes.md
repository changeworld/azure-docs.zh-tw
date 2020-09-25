---
title: Azure Stack Edge Pro GA 版本資訊 |Microsoft Docs
description: 描述執行正式發行版本 Azure Stack Edge Pro 的重大開啟問題和解決方式。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 09/24/2020
ms.author: alkohli
ms.openlocfilehash: 9d3aafad457f5c72a8c45f4f98f8f03eb8d978d7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324258"
---
# <a name="azure-stack-edge-pro-with-gpu-general-availability-ga-release-notes"></a>Azure Stack Edge Pro 和 GPU 正式運作 (正式推出) 版本資訊

下列版本資訊指出已解決的重大問題，以及適用于使用 GPU 的 Azure Stack Edge Pro 裝置 (GA) 版本正式推出的解決問題。

版本資訊會持續更新，並在發現需要提出因應措施的重大問題時有所增補。 在您部署 Azure Stack Edge Pro 裝置之前，請仔細檢查版本資訊中所包含的資訊。

本文適用于與軟體版本號碼**2.1.1358.2075**對應的**Azure Stack Edge Pro 2009**版本。

## <a name="whats-new"></a>最新消息

Azure Stack Edge 2009 版提供下列新功能。 

- **儲存類別** -在此版本中，可使用儲存類別讓您以動態方式布建儲存體。 如需詳細資訊，請參閱 [Azure Stack Edge PRO GPU 裝置上的 Kubernetes 儲存體管理](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning)。 
- **Kubernetes 儀表板與度量伺服器** -在此版本中，會使用計量伺服器附加元件來新增 Kubernetes 儀表板。 您可以使用儀表板來概覽 Azure Stack Edge Pro 裝置上執行的應用程式、查看 Kubernetes 叢集資源的狀態，以及查看裝置上發生的任何錯誤。 計量伺服器會在裝置上匯總 Kubernetes 資源的 CPU 和記憶體使用量。 如需詳細資訊，請參閱 [使用 Kubernetes 儀表板來監視您的 Azure Stack Edge PRO GPU 裝置](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)。
- **Azure Arc 已啟用 Azure Stack Edge Pro 的 Kubernetes** -從這個版本開始，您可以透過 Azure Arc 啟用的 Kubernetes，在 Azure Stack Edge Pro 裝置上部署應用程式工作負載。 Azure Arc 是一種混合式管理工具，可讓您在 Kubernetes 叢集上部署應用程式。 如需詳細資訊，請參閱 [Azure Stack Edge Pro 裝置上的 Azure Arc 部署工作負載](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。  

## <a name="known-issues"></a>已知問題 

下表提供 Azure Stack Edge Pro 裝置已知問題的摘要。

| 否。 | 功能 | 問題 | 因應措施/註解 |
| --- | --- | --- | --- |
|**1.**|預覽功能 |針對此 GA 版本，下列功能：本機 Azure Resource Manager、Vm、Kubernetes、Azure Arc 啟用的 Kubernetes、多進程服務 (MP) （適用于 GPU）全都可供您的 Azure Stack Edge Pro 裝置預覽。  |這些功能將在稍後的版本中正式推出。 |
| **2.** |Azure Stack Edge Pro + Azure SQL | 建立 SQL database 需要系統管理員存取權。   |請執行下列步驟，而不是中的步驟 1-2 [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) 。 <ul><li>在裝置的本機 UI 中，啟用計算介面。 選取計算 **> 適用的計算 > 埠 # > 啟用。**</li><li>`sqlcmd`從您的用戶端電腦下載https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>連接到您的計算介面 IP 位址 (已啟用) 的埠，並將 "，1401" 新增至位址的結尾。</li><li>最後一個命令看起來會像這樣： sqlcmd-S {Interface IP}，1401-U SA-P "強！Passw0rd」。</li>在此之後，來自目前檔的步驟3-4 應該相同。 </li></ul> |
| **3.** |重新整理| 不支援透過重新整理還原 **的 blob** 增量變更 |在重新整理之後，Blob 端點（blob 的部分更新）可能會導致更新未上傳至雲端。 例如，動作的順序如下：<ul><li>在雲端中建立 blob。 或從裝置刪除先前上傳的 blob。</li><li>使用重新整理功能將 blob 從雲端重新整理至設備。</li><li>使用 Azure SDK REST Api 只更新 blob 的一部分。</li></ul>這些動作可能會導致 blob 的更新區段無法在雲端中更新。 <br>因應**措施：透過**Explorer 或命令列使用 robocopy 之類的工具或一般檔案複製來取代整個 blob。|
|**4.**|節流|在節流期間，如果裝置不允許新的寫入，NFS 用戶端所完成的寫入會失敗，並出現「許可權被拒」錯誤。| 錯誤會顯示如下：<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir：無法建立目錄 ' test '：已拒絕許可權|
|**5.**|Blob 儲存體內嵌|針對 Blob 儲存體內嵌使用 AzCopy 第10版時，請使用下列引數來執行 AzCopy： `Azcopy <other arguments> --cap-mbps 2000`| 如果未針對 AzCopy 提供這些限制，則可能會將大量的要求傳送至裝置，並導致服務發生問題。|
|**6.**|階層式儲存體帳戶|下列適用于使用分層式儲存體帳戶時：<ul><li> 只支援區塊 blob。 不支援分頁 Blob。</li><li>沒有快照集或複製 API 支援。</li><li> `distcp`因為會大量使用複製作業，所以不支援透過來內嵌 Hadoop 工作負載。</li></ul>||
|**7.**|NFS 共用連線|如果有多個進程複製到相同的共用，且 `nolock` 未使用此屬性，您可能會在複製期間看到錯誤。|`nolock`屬性必須傳遞至掛接命令，才能將檔案複製到 NFS 共用。 例如：`C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`。|
|**8.**|Kubernetes 叢集|在執行 kubernetes 叢集的裝置上套用更新時，kubernetes 的虛擬機器將會重新開機並重新啟動。 在此情況下，只會在更新之後自動還原以指定複本部署的 pod。  |如果您已在未指定複本集的情況下，于複寫控制器外部建立個別 pod，則在裝置更新之後，將不會自動還原這些 pod。 您將需要還原這些 pod。<br>複本集會取代因為任何原因而刪除或終止的 pod，例如節點失敗或干擾性節點升級。 基於這個理由，即使您的應用程式只需要單一 pod，仍建議您使用複本集。|
|**9.**|Kubernetes 叢集|只有 Helm v3 或更新版本才支援 Azure Stack Edge Pro 上的 Kubernetes。 如需詳細資訊，請移至 [常見問題：移除 Tiller](https://v3.helm.sh/docs/faq/)。|
|**10.**|已啟用 Azure Arc 的 Kubernetes |針對 GA 版本，Azure Arc 啟用的 Kubernetes 會從版本0.1.18 更新為0.2.9。 因為 Azure Stack Edge 裝置不支援 Azure Arc 啟用的 Kubernetes 更新，所以您必須重新部署 Azure Arc 啟用 Kubernetes。|請遵循下列步驟：<ol><li>套用[裝置軟體和 Kubernetes 更新](azure-stack-edge-gpu-install-update.md)。</li><li>連接到 [裝置的 PowerShell 介面](azure-stack-edge-gpu-connect-powershell-interface.md)。</li><li>移除現有的 Azure Arc 代理程式。 輸入：`Remove-HcsKubernetesAzureArcAgent`。</li><li>[將 Azure Arc 部署到新的資源](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。 請勿使用現有的 Azure Arc 資源。</li></ol>|
|**rj-11.**|已啟用 Azure Arc 的 Kubernetes|如果您的 Azure Stack Edge Pro 裝置上已設定 web proxy，則不支援 Azure Arc 部署。||
|**全年.**|Kubernetes |埠31000已保留供 Kubernetes 儀表板之用。 同樣地，在預設設定中，IP 位址172.28.0.1 和172.28.0.10 會分別保留給 Kubernetes 服務和核心 DNS 服務。|請勿使用保留的 Ip。|
|**.13.**|Kubernetes |Kubernetes 目前不允許多重通訊協定的 LoadBalancer 服務。 例如，DNS 服務必須同時接聽 TCP 和 UDP。 |為了解決這項 Kubernetes 與 MetalLB 的限制，有兩個服務 (一個用於 TCP，一個用於 UDP) 可在相同的 pod 選取器上建立。 這些服務會使用相同的共用金鑰和 loadBalancerIP 共用相同的 IP 位址。 如果您的服務數目超過可用的 IP 位址，也可以共用 Ip。 <br> 如需詳細資訊，請參閱 [IP 位址共用](https://metallb.universe.tf/usage/#ip-address-sharing)。|
|**日.**|Kubernetes 叢集|現有 Azure IoT Edge marketplace 模組可能需要進行修改，才能在 Azure Stack Edge 裝置上的 IoT Edge 上執行。|如需詳細資訊，請參閱從 marketplace 修改 Azure IoT Edge 模組，以便在 Azure Stack Edge 裝置上執行。<!-- insert link-->|
|**長.**|Kubernetes |在 Azure Stack Edge 裝置上，Kubernetes 上的 Azure IoT Edge 不支援以檔案為基礎的系結裝載。|IoT Edge 使用轉譯層來將 `ContainerCreate` 選項轉譯為 Kubernetes 的結構。 建立 `Binds` 對應至 hostpath 目錄，因此以檔案為基礎的系結裝載無法系結至 IoT Edge 容器中的路徑。 可能的話，請對應上層目錄。|
|**16.**|Kubernetes |如果您攜帶自己的憑證進行 IoT Edge，並在裝置上設定計算之後將其新增至 Azure Stack Edge 裝置，則不會收取新的憑證。|若要解決此問題，您應該先上傳憑證，再于裝置上設定計算。 如果已設定計算，請 [連接到裝置的 PowerShell 介面，然後執行 IoT Edge 命令](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands)。 重新開機 `iotedged` 和 pod `edgehub` 。|
|**至.**|憑證 |在某些情況下，本機 UI 中的憑證狀態可能需要數秒鐘的時間來更新。 |本機 UI 中的下列案例可能會受到影響。<ul><li>[**憑證**] 頁面中的 [**狀態**] 資料行。</li><li>[**開始**使用] 頁面中的 [**安全性**] 磚。</li><li>**[總覽**] 頁面中的 [設定 **] 磚。**</li></ul>  |
|**至.**|IoT Edge |透過 IoT Edge 部署的模組無法使用主機網路。 | |
|**達.**|計算 + Kubernetes |Compute/Kubernetes 不支援 NTLM web proxy。 ||
|**診斷.**|計算 + web proxy + 更新 |如果您有以 web proxy 設定的計算，則計算更新可能會失敗。 |建議您在更新前先停用計算。 |

<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->




## <a name="next-steps"></a>後續步驟

- [準備使用 GPU 部署 Azure Stack Edge Pro 裝置](azure-stack-edge-gpu-deploy-prep.md)

