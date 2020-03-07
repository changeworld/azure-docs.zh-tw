---
title: 部署適用於 Azure 的 Avere vFXT
description: 在 Azure 中部署 Avere vFXT 叢集的步驟
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: e70d1dfebcf25ee8f4e90a062cee6dd72a663e02
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372151"
---
# <a name="deploy-the-vfxt-cluster"></a>部署 vFXT 叢集

此程序會引導您使用 Azure Marketplace 中提供的部署精靈。 精靈會自動使用 Azure Resource Manager 範本來部署叢集。 在表單中輸入參數並按一下 [**建立**] 之後，Azure 會自動完成下列工作：

* 建立叢集控制器，這是基本 VM，其中包含部署和管理叢集所需的軟體。
* 設定資源群組和虛擬網路基礎結構，包括建立新的元素。
* 建立叢集節點 Vm，並將其設定為 Avere 叢集。
* 如有要求，會建立新的 Azure Blob 容器，並將其設定為叢集核心檔案管理工具。

依照本檔中的指示進行之後，您將會有一個虛擬網路、一個子網、一個叢集控制器和一個 vFXT 叢集，如下圖所示。 此圖顯示選擇性的 Azure Blob 核心檔案管理工具，其中包含新的 Blob 儲存體容器（在新的儲存體帳戶中，未顯示），以及子網內 Microsoft 儲存體的服務端點。

![此圖顯示三個具有 Avere 叢集元件的同心圓矩形。 外部矩形會標示為「資源群組」，並包含標示為「Blob 儲存體（選擇性）」的六邊形。 中的下一個矩形標示為「虛擬網路： 10.0.0.0/16」，而且不包含任何唯一的元件。 最內層的矩形標示為「子網： 10.0.0.0/24」，其中包含標示為「叢集控制器」的 VM、三個標示為「vFXT 節點（vFXT Cluster）」的 Vm 堆疊，以及標示為「服務端點」的六邊形。 有一個箭號可連接服務端點（位於子網內）和 blob 儲存體（在資源群組中的子網和 vnet 外部）。 箭頭會通過子網和虛擬網路界限。](media/avere-vfxt-deployment.png)

使用建立範本之前，請確定您已滿足下列先決條件：  

* [新的訂用帳戶](avere-vfxt-prereqs.md#create-a-new-subscription)
* [訂用帳戶擁有者權限](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [vFXT 叢集配額](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [儲存體服務端點（如有需要）](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) -使用現有虛擬網路並建立 blob 儲存體的部署所需

如需有關叢集部署步驟和規劃的詳細資訊，請參閱[規劃您的 Avere vFXT 系統](avere-vfxt-deploy-plan.md)和[部署概觀](avere-vfxt-deploy-overview.md)。

## <a name="create-the-avere-vfxt-for-azure"></a>建立 Avere vFXT for Azure

藉由搜尋 Avere 並選取 [Avere vFXT for Azure ARM 範本]，在 Azure 入口網站中存取建立範本。

![使用階層連結 [新增 > Marketplace > 全部內容] 瀏覽顯示的 Azure 入口網站的視窗。 在 [所有專案] 頁面中，搜尋欄位具有「avere」一詞，而第二個結果「Avere vFXT for Azure ARM 範本」則以紅色標示，以反白顯示。](media/avere-vfxt-template-choose.png)

閱讀 [Avere vFXT for Azure ARM 範本] 頁面上的詳細資料之後，按一下其 [**建立**] 按鈕以開始。

![使用部署範本的 Azure Marketplace 第一頁顯示畫面](media/avere-vfxt-deploy-first.png)

範本會分割成四個步驟 - 兩個資訊收集頁面，再加上驗證和確認步驟。

* 第一頁會收集叢集控制器 VM 的設定。
* 第二頁會收集用來建立叢集和其他資源（例如子網和儲存體）的參數。
* 第三頁會摘要說明您的選擇，並驗證設定。
* 第四頁會說明軟體的條款及條件，並讓您開始叢集建立程序。

## <a name="page-one-parameters---cluster-controller-information"></a>第一頁的參數 - 叢集控制器資訊

部署範本的第一頁著重在叢集控制器上。

![部署範本的第一頁](media/avere-vfxt-deploy-1.png)

填寫下列資訊︰

* **叢集控制器名稱** - 設定叢集控制器 VM 的名稱。

* **控制器使用者名稱**-設定叢集控制器 VM 的根使用者名稱。

* **驗證類型** - 選擇使用 [密碼] 或 [SSH 公開金鑰] 驗證來連線到控制器。 建議使用 SSH 公開金鑰方法；如果需要協助，請參閱[如何建立和使用 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

* **密碼**或 **SSH 公開金鑰** - 根據您選取的驗證類型，您必須在下方欄位中提供 RSA 公開金鑰或密碼。 此認證會與稍早提供的使用者名稱搭配使用。

* **訂用帳戶** - 選取 Avere vFXT 的訂用帳戶。

* **資源群組**- 選取 Avere vFXT 叢集的現有空資源群組，或按一下 [新建] 並輸入新的資源群組名稱。

* **位置** - 選取您叢集和資源的 Azure 位置。

完成後，按一下 [確定]。

> [!NOTE]
> 如果您想要讓叢集控制器有公開的 IP 位址，請為叢集建立新的虛擬網路，而不是選取現有的網路。 這項設定位在第二頁。

## <a name="page-two-parameters---vfxt-cluster-information"></a>第二頁的參數 - vFXT 叢集資訊

部署範本的第二頁可讓您設定叢集大小、節點類型、快取大小和儲存體參數等其他設定。

![部署範本的第二頁](media/avere-vfxt-deploy-2.png)

* **Avere vFXT 叢集節點計數**-選擇叢集中的節點數目。 最小值是三個節點，而最大值是十二個。

* **叢集管理密碼** - 建立用於管理叢集的密碼。 此密碼會與 [使用者名稱] ```admin``` 用來登入叢集控制台，您可以在其中監視叢集及設定叢集設定。

* **Avere vFXT 叢集名稱** - 為叢集提供唯一名稱。

* **大小**-此區段會顯示將用於叢集節點的 VM 類型。 雖然只有一個建議選項，[**變更大小**] 連結會開啟一個資料表，其中包含此實例類型的詳細資料，以及價格計算機的連結。

* **每個節點**的快取大小-叢集快取會分散在叢集節點上，因此 Avere vFXT 叢集上的快取大小總計會乘以節點數目。

  建議的設定：針對 Standard_E32s_v3 節點，每個節點使用 4 TB。

* **虛擬網路**-定義要存放叢集的新虛擬網路，或選取符合[規劃您的 Avere vFXT 系統](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure)中所述之必要條件的現有網路。

  > [!NOTE]
  > 如果您建立新的虛擬網路，叢集控制器將會有公用 IP 位址，讓您可以存取新的私人網路。 如果您選擇現有的虛擬網路，則會在沒有公用 IP 位址的情況下設定叢集控制器。
  >
  > 叢集控制器上可見的公用 IP 位址可提供較簡便的 vFXT 存取方式，但會產生些微安全性風險。
  >* 叢集控制器上的公用 IP 位址可讓您使用它作為跳躍點主機，以從私人子網路外連線到 Avere vFXT 叢集。
  >* 如果您的控制器上沒有公用 IP 位址，您需要另一個跳躍主機、VPN 連線或 ExpressRoute 來存取叢集。 例如，使用已設定 VPN 連線的現有虛擬網路。
  >* 如果您建立具有公用 IP 位址的控制器，您應該使用網路安全性群組來保護該控制器 VM。 根據預設，Avere vFXT for Azure 部署會建立網路安全性群組，將具有公用 IP 位址之控制器的輸入存取限制為僅限埠22。 若要進一步保護系統，您可以鎖定 IP 來源位址範圍的存取，也就是只允許從要用來存取叢集的機器進行連線。

  新的虛擬網路也設定了 Azure Blob 儲存體的儲存體服務端點，並已鎖定網路存取控制，只允許來自叢集子網的 Ip。

* **子網**-選擇子網或建立一個新的。

* **建立和使用 blob 儲存體**-選擇 [ **true** ] 以建立新的 Azure blob 容器，並將其設定為新 Avere vFXT 叢集的後端儲存體。 此選項也會在叢集的資源群組中建立新的儲存體帳戶，並在叢集子網內建立 Microsoft 儲存體服務端點。
  
  如果您提供現有的虛擬網路，則在建立叢集之前，它必須具有儲存體服務端點。 （如需詳細資訊，請參閱[規劃您的 Avere vFXT 系統](avere-vfxt-deploy-plan.md)）。

  如果您不想建立新的容器，請將此欄位設為 [false]。 在此情況下，您必須在建立叢集之後，附加和設定存放裝置。 參閱[設定儲存體](avere-vfxt-add-storage.md)的指示。

* **（新）儲存體帳戶**-如果建立新的 Azure Blob 容器，請輸入新儲存體帳戶的名稱。

## <a name="validation-and-purchase"></a>驗證及購買

第三頁會摘要說明設定並驗證參數。 驗證成功之後，請檢查 [摘要]，然後按一下 [**確定]** 按鈕。

> [!TIP]
> 您可以按一下 [**確定]** 按鈕旁的 [**下載範本和參數**] 連結，以儲存此叢集的建立設定。 如果您稍後需要建立類似的叢集，這項資訊會很有用，例如，在嚴重損壞修復案例中建立替代叢集。 （如需深入瞭解，請參閱嚴重損壞[修復指引](disaster-recovery.md)）。

![部署範本的第三頁 - 驗證](media/avere-vfxt-deploy-3.png)

第四頁提供使用規定以及隱私權和定價資訊的連結。

輸入任何遺失的連絡人資訊，然後按一下 [**建立**] 按鈕以接受條款，並建立 Avere vFXT for Azure 叢集。

![部署範本的第四頁 - 條款和條件、建立按鈕](media/avere-vfxt-deploy-4.png)

叢集部署大約需要 15 到 20 分鐘。

## <a name="gather-template-output"></a>收集範本輸出

當 Avere vFXT 範本完成建立叢集時，它會輸出新叢集的重要資訊。

> [!TIP]
> 請務必從範本輸出複製**管理 IP 位址**。 您需要此位址來管理叢集。

若要尋找資訊：

1. 移至您叢集控制器的資源群組。

1. 在左側按一下 [部署]，然後選取 [microsoft-avere.vfxt-template]。

   ![資源群組入口網站頁面，其中左側已選取 [部署]，而 [microsoft-avere.vfxt-template] 則顯示在 [部署名稱] 底下的表格中](media/avere-vfxt-outputs-deployments.png)

1. 在左側按一下 [輸出]。 複製每個欄位中的值。

   ![輸出頁面的標籤右側欄位中顯示 SSHSTRING、RESOURCE_GROUP、LOCATION、NETWORK_RESOURCE_GROUP、NETWORK、SUBNET、SUBNET_ID、VSERVER_IPs 和 MGMT_IP 值](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>後續步驟

既然叢集正在執行，而且您知道其管理 IP 位址，請[連接到](avere-vfxt-cluster-gui.md)叢集設定工具。

使用設定介面來自訂您的叢集，包括下列安裝工作：

* [啟用支援](avere-vfxt-enable-support.md)
* [新增儲存體](avere-vfxt-add-storage.md)（如有需要）
