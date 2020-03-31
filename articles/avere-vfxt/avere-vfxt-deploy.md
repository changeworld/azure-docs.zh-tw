---
title: 部署適用於 Azure 的 Avere vFXT
description: 在 Azure 中部署 Avere vFXT 叢集的步驟
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: e70d1dfebcf25ee8f4e90a062cee6dd72a663e02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252593"
---
# <a name="deploy-the-vfxt-cluster"></a>部署 vFXT 叢集

此程序會引導您使用 Azure Marketplace 中提供的部署精靈。 精靈會自動使用 Azure Resource Manager 範本來部署叢集。 在表單中輸入參數並按一下"**創建**"後，Azure 將自動完成以下任務：

* 創建叢集控制器，這是一個基本 VM，其中包含部署和管理群集所需的軟體。
* 設置資源組和虛擬網路基礎結構，包括創建新元素。
* 創建叢集節點 VM 並將其配置為 Avere 群集。
* 如果請求，請創建新的 Azure Blob 容器並將其配置為群集核心檔程式。

按照本文檔中的說明操作後，您將具有虛擬網路、子網、叢集控制器和 vFXT 群集，如下圖所示。 此圖顯示了可選的 Azure Blob 核心檔程式，其中包括新的 Blob 存儲容器（未顯示在新的存儲帳戶中）和在子網內為 Microsoft 存儲提供服務終結點。

![顯示三個同心矩形的 Avere 群集元件的圖。 外部矩形標記為"資源組"，並包含標記為"Blob 存儲（可選）"的六邊形。 中的下一個矩形標記為"虛擬網路：10.0.0.0/16"，不包含任何唯一的元件。 最內長方形標記為"Subnet：10.0.0.0/24"，包含標記為"叢集控制器"的 VM、標有"vFXT 節點（vFXT 群集）"的三個 VM 堆疊以及標記為"服務終結點"的六角形。 有一個箭頭連接服務終結點（位於子網內）和 blob 存儲（在資源組中的子網和 vnet 外部）。 箭頭通過子網和虛擬網路邊界。](media/avere-vfxt-deployment.png)

使用建立範本之前，請確定您已滿足下列先決條件：  

* [新的訂用帳戶](avere-vfxt-prereqs.md#create-a-new-subscription)
* [訂用帳戶擁有者權限](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [vFXT 叢集配額](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [存儲服務終結點（如果需要）](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) - 使用現有虛擬網路並創建 Blob 存儲的部署是必需的

如需有關叢集部署步驟和規劃的詳細資訊，請參閱[規劃您的 Avere vFXT 系統](avere-vfxt-deploy-plan.md)和[部署概觀](avere-vfxt-deploy-overview.md)。

## <a name="create-the-avere-vfxt-for-azure"></a>建立 Avere vFXT for Azure

通過搜索 Avere 並選擇"Azure ARM 範本的 Avere vFXT"訪問 Azure 門戶中的創建範本。

![使用階層連結 [新增 > Marketplace > 全部內容] 瀏覽顯示的 Azure 入口網站的視窗。 在"萬事萬事"頁中，搜索欄位的術語為"avere"，第二個結果"Azure ARM 範本的 Avere vFXT"以紅色突出顯示。](media/avere-vfxt-template-choose.png)

閱讀 Azure ARM 範本的 Avere vFXT 頁面上的詳細資訊後，按一下其 **"創建"** 按鈕開始。

![使用部署範本的 Azure Marketplace 第一頁顯示畫面](media/avere-vfxt-deploy-first.png)

範本會分割成四個步驟 - 兩個資訊收集頁面，再加上驗證和確認步驟。

* 第一頁收集叢集控制器 VM 的設置。
* 第二頁收集用於創建群集的參數和其他資源（如子網和存儲）。
* 第三頁總結了您的選擇並驗證了配置。
* 第四頁會說明軟體的條款及條件，並讓您開始叢集建立程序。

## <a name="page-one-parameters---cluster-controller-information"></a>第一頁的參數 - 叢集控制器資訊

部署範本的第一頁側重于叢集控制器。

![部署範本的第一頁](media/avere-vfxt-deploy-1.png)

填寫下列資訊︰

* **叢集控制器名稱** - 設定叢集控制器 VM 的名稱。

* **控制器使用者名**- 設置叢集控制器 VM 的根使用者名。

* **驗證類型** - 選擇使用 [密碼] 或 [SSH 公開金鑰] 驗證來連線到控制器。 建議使用 SSH 公開金鑰方法；如果需要協助，請參閱[如何建立和使用 SSH 金鑰](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)。

* **密碼**或 **SSH 公開金鑰** - 根據您選取的驗證類型，您必須在下方欄位中提供 RSA 公開金鑰或密碼。 此認證會與稍早提供的使用者名稱搭配使用。

* **訂用帳戶** - 選取 Avere vFXT 的訂用帳戶。

* **資源群組**- 選取 Avere vFXT 叢集的現有空資源群組，或按一下 [新建] 並輸入新的資源群組名稱。

* **位置** - 選取您叢集和資源的 Azure 位置。

完成後按一下 **"確定**"。

> [!NOTE]
> 如果您想要讓叢集控制器有公開的 IP 位址，請為叢集建立新的虛擬網路，而不是選取現有的網路。 這項設定位在第二頁。

## <a name="page-two-parameters---vfxt-cluster-information"></a>第二頁的參數 - vFXT 叢集資訊

部署範本的第二頁可讓您設定叢集大小、節點類型、快取大小和儲存體參數等其他設定。

![部署範本的第二頁](media/avere-vfxt-deploy-2.png)

* **Avere vFXT 叢集節點計數**- 選擇群集中的節點數。 最小值是三個節點，而最大值是十二個。

* **叢集管理密碼** - 建立用於管理叢集的密碼。 此密碼與使用者名```admin```一起使用以登錄到群集控制台，您可以在其中監視群集並配置群集設置。

* **Avere vFXT 叢集名稱** - 為叢集提供唯一名稱。

* **大小**- 本節顯示將用於叢集節點的 VM 類型。 儘管只有一個建議的選項，**但"更改大小**"連結將打開一個表，其中顯示有關此實例類型的詳細資訊，並打開指向價格計算機的連結。

* **每個節點的緩存大小**- 群集緩存分佈在叢集節點上，因此 Avere vFXT 群集上的總緩存大小將此大小乘以節點數。

  建議配置：對於節點Standard_E32s_v3每個節點使用 4 TB。

* **虛擬網路**- 定義新的虛擬網路來容納群集，或選擇滿足[計畫 Avere vFXT 系統](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure)中描述的先決條件的現有網路。

  > [!NOTE]
  > 如果創建新的虛擬網路，叢集控制器將具有公共 IP 位址，以便您可以訪問新的私人網路絡。 如果選擇現有虛擬網路，則叢集控制器配置時沒有公共 IP 位址。
  >
  > 叢集控制器上可見的公用 IP 位址可提供較簡便的 vFXT 存取方式，但會產生些微安全性風險。
  >* 叢集控制器上的公用 IP 位址可讓您使用它作為跳躍點主機，以從私人子網路外連線到 Avere vFXT 叢集。
  >* 如果控制器上沒有公共 IP 位址，則需要另一個跳轉主機、VPN 連接或 ExpressRoute 來訪問群集。 例如，使用已配置 VPN 連接的現有虛擬網路。
  >* 如果您建立具有公用 IP 位址的控制器，您應該使用網路安全性群組來保護該控制器 VM。 預設情況下，Azure 部署的 Avere vFXT 會創建一個網路安全性群組，該組僅將具有公共 IP 位址的控制器的入站訪問限制為埠 22。 若要進一步保護系統，您可以鎖定 IP 來源位址範圍的存取，也就是只允許從要用來存取叢集的機器進行連線。

  新的虛擬網路還配置了用於 Azure Blob 存儲的存儲服務終結點，並鎖定了網路存取控制，以僅允許群集子網中的 IP。

* **子網**- 選擇子網或創建新子網。

* **創建和使用 Blob 存儲**- 選擇**true**創建新的 Azure Blob 容器，並將其配置為新 Avere vFXT 群集的後端存儲。 此選項還會在群集的資源組中創建新的存儲帳戶，並在群集子網內創建 Microsoft 存儲服務終結點。
  
  如果提供現有虛擬網路，則在創建群集之前，它必須具有存儲服務終結點。 （有關詳細資訊，請閱讀規劃[Avere vFXT 系統](avere-vfxt-deploy-plan.md)。

  如果您不想建立新的容器，請將此欄位設為 [false]****。 在這種情況下，必須在創建群集後附加和配置存儲。 參閱[設定儲存體](avere-vfxt-add-storage.md)的指示。

* **（新建）存儲帳戶**- 如果創建新的 Azure Blob 容器，請輸入新存儲帳戶的名稱。

## <a name="validation-and-purchase"></a>驗證及購買

第三頁總結了配置並驗證了參數。 驗證成功後，請檢查摘要並按一下 **"確定**"按鈕。

> [!TIP]
> 您可以通過按一下"**確定"** 按鈕旁邊的 **"下載"範本和參數**連結來保存此群集的創建設置。 如果需要以後創建類似的群集（例如，在災害復原方案中創建替換群集），此資訊非常有用。 （閱讀[災害復原指南](disaster-recovery.md)以瞭解更多資訊。

![部署範本的第三頁 - 驗證](media/avere-vfxt-deploy-3.png)

第四頁提供了使用條款以及隱私和定價資訊的連結。

輸入任何缺少的聯繫資訊，然後按一下 **"創建**"按鈕以接受條款並為 Azure 群集創建 Avere vFXT。

![部署範本的第四頁 - 條款和條件、建立按鈕](media/avere-vfxt-deploy-4.png)

叢集部署大約需要 15 到 20 分鐘。

## <a name="gather-template-output"></a>收集範本輸出

當 Avere vFXT 範本完成創建群集時，它將輸出有關新群集的重要資訊。

> [!TIP]
> 請確保從範本輸出複製**管理 IP 位址**。 您需要此位址來管理叢集。

要查找資訊：

1. 移至您叢集控制器的資源群組。

1. 在左側按一下 [部署]****，然後選取 [microsoft-avere.vfxt-template]****。

   ![資源群組入口網站頁面，其中左側已選取 [部署]，而 [microsoft-avere.vfxt-template] 則顯示在 [部署名稱] 底下的表格中](media/avere-vfxt-outputs-deployments.png)

1. 在左側按一下 [輸出]****。 複製每個欄位中的值。

   ![輸出頁面的標籤右側欄位中顯示 SSHSTRING、RESOURCE_GROUP、LOCATION、NETWORK_RESOURCE_GROUP、NETWORK、SUBNET、SUBNET_ID、VSERVER_IPs 和 MGMT_IP 值](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>後續步驟

現在群集正在運行，並且您知道其管理 IP 位址，[請連接到群集組態工具](avere-vfxt-cluster-gui.md)。

使用配置介面自訂群集，包括以下設置任務：

* [啟用支援](avere-vfxt-enable-support.md)
* [添加存儲](avere-vfxt-add-storage.md)（如果需要）
