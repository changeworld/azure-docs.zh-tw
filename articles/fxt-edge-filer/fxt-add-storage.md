---
title: 教學課程：將儲存體新增至 Azure FXT Edge Filer 叢集
description: 如何針對 Azure FXT Edge Filer 設定後端儲存體和面向用戶端的虛擬命名空間
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 3f736942627d088e3a639f89bef5438714c2608b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "79223135"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>教學課程：新增後端儲存體並設定虛擬命名空間 

此教學課程說明如何針對您的快取新增後端儲存體，以及如何設定面向用戶端的虛擬檔案系統。 

叢集會連線到後端儲存體系統來存取資料用戶端要求，並且比在快取中更永久地儲存變更。 

命名空間是面向用戶端的虛擬檔案系統，允許您在不變更用戶端工作流程的情況下交換後端儲存體。 

在本教學課程中，您將了解： 

> [!div class="checklist"]
> * 如何將後端儲存體新增至 Azure FXT Edge Filer 叢集 
> * 如何針對儲存體定義面向用戶端的路徑

## <a name="about-back-end-storage"></a>關於後端儲存體

Azure FXT Edge Filer 叢集會使用核心檔案管理工具  定義，將後端儲存體系統連結至 FXT 叢集。

Azure FXT Edge Filer 與數個熱門 NAS 硬體系統相容，且可以使用 Azure Blob 或其他雲端儲存體中的空的容器。 

雲端儲存體容器新增時必須為空的，以便 FXT 作業系統可以完全管理雲端儲存體磁碟區上的所有資料。 將容器作為核心檔案管理工具新增至叢集後，可以將現有的資料移至雲端容器。

使用 [控制台] 將核心檔案管理工具新增至您的系統。

> [!NOTE]
> 
> 如果您想要使用 Amazon AWS 或 Google 雲端儲存空間，則必須安裝 FlashCloud<sup>TM</sup> 功能授權。 請連絡您的 Microsoft 代表以取得授權金鑰，然後按照舊版設定指南中的指示[新增或移除功能授權](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses) \(英文\)。
> 
> Azure FXT Edge Filer 軟體授權中包含 Azure Blob 儲存體的支援。 

如需新增核心檔案管理工具的詳細資訊，請參閱《叢集設定指南》的這些章節：

* 如需有關選擇和準備新增核心檔案管理工具的詳細資訊，請參閱[使用核心檔案管理工具](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview) \(英文\)。
* 如需詳細的先決條件和逐步指示，請參閱這些文章：

  * [新增新的 NAS 核心檔案管理工具](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas) \(英文\)
  * [新增新的雲端核心檔案管理工具](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud) \(英文\)

新增核心檔案管理工具後，您可以在 [核心檔案管理工具詳細資訊] 設定頁面上更新其設定。

## <a name="add-a-core-filer"></a>新增核心檔案管理工具

按一下 [核心檔案管理工具]   > [管理核心檔案管理工具]  設定頁面上的 [建立]  按鈕來定義核心檔案管理工具。

![按一下 [管理核心檔案管理工具] 頁面上核心檔案管理工具清單上方的 [建立] 按鈕](media/fxt-cluster-config/create-core-filer-button.png)

[新增新的核心檔案管理工具]  精靈會引導您完成建立連結至後端儲存體之核心檔案管理工具的流程。 《叢集設定指南》提供了有關該流程的逐步說明，該流程對於 NFS/NAS 存放裝置和雲端儲存體 (上面的連結) 是不同的。 

子工作包括：

* 指定核心檔案管理工具的類型 (NAS 或雲端)

  ![硬體 NAS 新核心檔案管理工具精靈的第一頁。 [雲端核心檔案管理工具] 的選項已停用，並顯示有關遺漏的授權的錯誤訊息。](media/fxt-cluster-config/new-nas-1.png)

* 設定核心檔案管理工具的名稱。 選擇一個名稱，以協助叢集系統管理員了解它所代表之儲存體系統。

* 針對 NAS 核心檔案管理工具，請提供完整的網域名稱 (FQDN) 或 IP 位址。 建議所有核心檔案管理工具都使用 FQDN，且針對 SMB 存取為必要。

* 選取快取原則 - 精靈的第二頁列出了新的核心檔案管理工具的可用快取原則。 如需詳細資訊，請參閱《叢集設定指南》的[快取原則](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html)一節。 

  ![硬體 NAS 新核心檔案管理工具精靈的第二頁；[快取原則] 下拉式功能表已開啟，其中顯示了數個已停用的選項和三個有效的快取原則選項 (略過、讀取快取和讀取/寫入快取)。](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* 針對雲端儲存體，您必須指定雲端服務和存取認證以及其他參數。 如需詳細資訊，請參閱《叢集設定指南》中的[雲端服務和通訊協定](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) \(英文\)。

  ![[新的核心檔案管理工具] 精靈中的雲端核心檔案管理工具資訊](media/fxt-cluster-config/new-core-filer-cloud3.png) 
  
  如果您已為此叢集新增了雲端存取認證，它們會出現在清單中。 在 [叢集]   > [雲端認證]  設定頁面中更新並新增認證。 

在精靈中填寫所有必要設定後，按一下 [新增檔案管理工具]  按鈕以提交變更。

幾分鐘後，儲存體系統將顯示在 [儀表板]  核心檔案管理工具清單中，並可透過 [核心檔案管理工具] 設定頁面進行存取。

![[管理核心檔案管理工具設定] 頁面中的核心檔案管理工具 "Flurry-NAS"，其中包含展開的檔案管理工具詳細資訊檢視](media/fxt-cluster-config/core-filer-in-manage-page.png)

此螢幕擷取畫面中的核心檔案管理工具遺漏 VServer。 您必須將核心檔案管理工具連結至 VServer 並建立連接點，讓用戶端可以存取儲存體。 下面在[設定命名空間](#configure-the-namespace)中介紹了這些步驟。

## <a name="configure-the-namespace"></a>設定命名空間

Azure FXT Edge Filer 叢集會建立一個名為「叢集命名空間」  的虛擬檔案系統，可簡化用戶端儲存在各種後端系統上的資料的存取。 因為用戶端要求檔案使用虛擬路徑，因此可以新增或取代儲存體系統，而不需要變更用戶端工作流程。 

叢集命名空間也允許您以類似的檔案結構呈現雲端和 NAS 儲存體系統。 

叢集的 VServer 會維護命名空間並向用戶端提供內容。 建立叢集命名空間有兩個步驟： 

1. 建立 VServer 
1. 在後端儲存體系統和面向用戶端的檔案系統路徑之間設定連接點 

### <a name="create-a-vserver"></a>建立 VServer

VServer 是虛擬的檔案伺服器，用於控制用戶端與叢集的核心檔案管理工具之間的資料流動方式：

* VServer 可託管面向用戶端的 IP 位址
* VServer 會建立命名空間，並定義可將面向用戶端的虛擬目錄結構對應至後端儲存體上的匯出的連接點
* VServer 會強制執行檔案存取控制，包括核心檔案管理工具匯出原則和使用者驗證系統
* VServer 提供 SMB 基礎結構

在開始設定叢集 VServer 之前，請參閱連結的文件與諮詢您的 Microsoft 代表，以協助了解命名空間和 VServer。 如果使用 VLAN，請在建立 VServer 之前[建立它們](fxt-configure-network.md#adjust-network-settings)。 

《叢集設定指南》的這些章節將協助您熟悉 FXT VServer 和全域命名空間功能：

* [建立和使用 VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [使用全域命名空間](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [建立 VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

您的叢集至少需要一個 VServer。 

若要建立新的 VServer，您需要下列資訊︰

* 要為 VServer 設定的名稱

* VServer 將處理的面向用戶端的 IP 位址範圍

  當您建立 VServer 時，您必須提供單一連續的 IP 位址範圍。 您可以稍後使用 [面向用戶端的網路]  設定頁面新增更多位址。

* 如果您的網路具有 VLAN，則將此 VLAN 用於此 VServer

使用 [VServer]   > [管理VServer]  設定頁面建立新的 VServer。 如需詳細資訊，請參閱叢集設定指南中的[建立 VServer](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) \(英文\)。 

![[建立新的 VServer] 快顯視窗](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>建立連接點

連接點  會將後端儲存體路徑對應至用戶端可見的命名空間。

您可以使用此系統簡化用戶端掛接點中使用的路徑，並順暢地調整容量，因為一個虛擬路徑可容納多個核心檔案管理工具的儲存體。

![[新增新的連接點] 精靈頁面，其中已填入設定](media/fxt-cluster-config/add-junction-full.png)

如需建立命名空間連接點的完整詳細資訊，請參閱《叢集設定指南》中的 [**VServer** > **命名空間**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html)。

![顯示連接點詳細資料的 [VServer] > [命名空間] 設定頁面](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>設定匯出規則

同時擁有 VServer 和核心檔案管理工具後，您應該自訂匯出規則與匯出原則，以控制用戶端如何存取核心檔案管理工具匯出上的檔案。

首先，使用 [VServer]   > [匯出規則]  頁面加入新的規則、修改預設原則或建立您自己的自訂匯出原則。

其次，在透過該 VServer 存取時，使用 [VServer]   > [匯出原則]  頁面將自訂原則套用至核心檔案管理工具的匯出。

如需詳細資訊，請閱讀《叢集設定指南》文章[控制對核心檔案管理工具匯出的存取](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) \(英文\)。


## <a name="next-steps"></a>後續步驟

新增儲存體並設定面向用戶端的命名空間之後，請完成叢集的初始設定： 

> [!div class="nextstepaction"]
> [ 設定叢集的網路設定](fxt-configure-network.md)
