---
title: 在 Microsoft Azure FXT Edge Filer 叢集上掛接用戶端
description: NFS 用戶端機器要如何掛接 Azure FXT Edge Filer 混合式儲存體快取
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: ea963b143cedf36137d9c36bc57d323353da6786
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231347"
---
# <a name="tutorial-mount-the-cluster"></a>教學課程：掛接叢集

本教學課程會說明如何將 NFS 用戶端掛接到 Azure FXT Edge Filer 叢集。 用戶端會掛接您在新增後端儲存體時所指派的虛擬命名空間路徑。

本教學課程會說明：

> [!div class="checklist"]
> * 將用戶端負載平衡到面向用戶端 IP 位址範圍的策略
> * 如何透過面向用戶端的 IP 位址和命名空間連接點來建構掛接路徑
> * 要在掛接命令中使用哪些引數

本教學課程需要約 45 分鐘才能完成。

## <a name="steps-to-mount-the-cluster"></a>掛接叢集的步驟

請遵循下列步驟將用戶端機器連線至 Azure FXT Edge Filer 叢集。

1. 決定如何在您的叢集節點之間進行用戶端流量的負載平衡。 如需詳細資訊，請參閱[平衡用戶端負載](#balance-client-load)。
1. 識別要掛接的叢集 IP 位址和連接點路徑。
1. 確定要用於掛接的面向用戶端路徑。
1. 使用適當的引數發出[掛接命令](#use-recommended-mount-command-options)。

## <a name="balance-client-load"></a>平衡用戶端負載

為了在叢集中的所有節點之間平衡用戶端要求，您應將用戶端掛接至面相用戶端的所有 IP 位址。 有幾個方式可將這項工作自動化。

若要了解叢集的循環配置資源 DNS 負載平衡，請閱讀[設定 Azure FXT Edge Filer 叢集的 DNS](fxt-configure-network.md#configure-dns-for-load-balancing)。 若要使用此方法，您必須維護 DNS 伺服器，但這些文章不會有這方面的說明。

對於小型安裝來說，較簡單的方法是在掛接用戶端時，使用指令碼來指派整個範圍的 IP 位址。

其他負載平衡方法則可能適用於大型或複雜的系統。 請洽詢 Microsoft 業務代表或建立[支援票證](fxt-support-ticket.md)以尋求協助。 (Azure FXT Edge Filer 目前「不支援」Azure Load Balancer。)

## <a name="create-the-mount-command"></a>建立掛接命令

在您的用戶端上，``mount`` 命令會將 Azure FXT Edge Filer 叢集上的虛擬伺服器 (vserver) 對應至本機檔案系統上的路徑。

其格式為 ``mount <FXT cluster path> <local path> {options}``

掛接命令有三個元素：

* 叢集路徑 - IP 位址與命名空間連接點路徑的組合，說明如下
* 本機路徑 - 用戶端上的路徑
* 掛接命令選項 - (列於[使用建議的掛接命令選項](#use-recommended-mount-command-options)中)

### <a name="create-the-cluster-path"></a>建立叢集路徑

叢集路徑由 vserver 的「IP 位址」加上「命名空間連接點」的路徑組合而成。 命名空間連接點是您在[新增儲存體系統](fxt-add-storage.md#create-a-junction)時所定義的虛擬路徑。

例如，如果您使用 ``/fxt/files`` 作為命名空間路徑，則用戶端會將 *IP_address*:/fxt/files 掛接至其本機掛接點。

![在命名空間路徑欄位中選取了的 /avere/files 的 [新增連接點] 對話方塊](media/fxt-mount/fxt-junction-example.png)

該 IP 位址是為 vserver 定義的其中一個面向用戶端的 IP 位址。 您可以在叢集控制台中的兩個位置找到面向用戶端的 IP 位址範圍：

* **VServers** 資料表 ([儀表板] 索引標籤) -

  ![控制台的 [儀表板] 索引標籤；已在圖形下方的資料表中選取了 [VServer] 索引標籤，並將 [IP 位址] 區段圈選起來](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **面向用戶端的網路**設定頁面 -

  ![[設定 > VServer > 面向用戶端的網路組態] 頁面；已將特定 vserver 之資料表的 [位址範圍] 區段圈選起來](media/fxt-mount/fxt-ip-addresses-settings.png)

結合 IP 位址和命名空間路徑來形成掛接命令的叢集路徑。

用戶端掛接命令範例：``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>建立本機路徑

掛接命令的本機路徑由您決定。 您可以將想要的任何路徑結構設定為虛擬命名空間的一部分。 請設計方便您用戶端工作流程的命名空間和本機路徑。

如需面向用戶端命名空間的詳細資訊，請閱讀《叢集設定指南》的[命名空間概觀](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)。

除了路徑以外，也請在掛接每個用戶端時加入[掛接命令選項](#use-recommended-mount-command-options)，如下所述。

### <a name="use-recommended-mount-command-options"></a>使用建議的掛接命令選項

若要確保用戶端能順暢地掛接，請在掛接命令中傳入下列設定和引數：

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| 必要的設定 | 描述 |
--- | ---
``hard`` | 對 Azure FXT Edge Filer 叢集執行軟掛接可能會產生應用程式失敗和資料遺失的狀況。
``proto=netid`` | 此選項支援適當處理 NFS 網路錯誤的功能。
``mountproto=netid`` | 此選項支援在掛接作業中適當處理網路錯誤的功能。
``retry=n`` | 設定 ``retry=30`` 可避免暫時性的掛接失敗。 (執行前景掛接時建議使用不同的值)。

| 慣用設定  | 描述 |
--- | ---
``nointr``            | 如果用戶端使用舊版作業系統核心 (2008 年 4 月之前) 但可支援此選項，請加以使用。 "intr" 是預設選項。

## <a name="next-steps"></a>後續步驟

在掛接用戶端之後，您可以測試工作流程並開始使用叢集。

如果您需要將資料移至新的雲端核心檔案管理工具，請使用平行資料擷取來利用快取結構。 [將資料移至 vFXT 叢集](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest)中會說明一些策略。 (Avere vFXT for Azure 是雲端式產品，會使用與 Azure FXT Edge Filer 非常類似的快取技術。)

如果您需要針對任何硬體問題進行疑難排解，請閱讀[監視 Azure FXT Edge Filer 硬體狀態](fxt-monitor.md)。
