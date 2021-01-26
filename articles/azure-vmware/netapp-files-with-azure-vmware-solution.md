---
title: 使用 Azure VMware 解決方案的 azure NetApp Files
description: 使用 Azure NetApp Files 搭配 Azure VMware 解決方案 Vm，在內部部署伺服器、Azure VMware 解決方案 Vm 和雲端基礎結構之間遷移和同步處理資料。
ms.topic: how-to
ms.date: 01/20/2021
ms.openlocfilehash: 97a9172dfbed2722d4af0abbd6231a0367dc4e3c
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798403"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>使用 Azure VMware 解決方案的 azure NetApp Files

在本文中，我們將逐步解說整合 Azure NetApp Files 與以 Azure VMware 解決方案為基礎的工作負載的步驟。 客體作業系統會在虛擬機器 (Vm) 存取 Azure NetApp Files 磁片區的虛擬機器中執行。 

## <a name="azure-netapp-files-overview"></a>Azure NetApp Files 總覽

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) 是 azure 的第一方服務，可在雲端中遷移和執行需求最高的企業檔案工作負載，包括資料庫、SAP 和高效能運算應用程式，而不需要變更程式碼。

### <a name="features"></a>功能
 (使用 Azure NetApp Files 的服務。 ) 

- **Active Directory 連接**： Azure NetApp Files 支援 [Active Directory Domain Services 和 Azure Active Directory Domain Services](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#decide-which-domain-services-to-use)。

- **共用通訊協定**： Azure NetApp Files 支援 Server Message BLOCK (SMB) 和網路檔案系統 (NFS) 通訊協定。 此支援表示可將磁片區掛接在 Linux 用戶端上，並可在 Windows 用戶端上進行對應。

- **Azure Vmware 解決方案**： Azure NetApp Files 共用可從 Azure VMware 解決方案環境中建立的 vm 掛接。

Azure NetApp Files 可在許多 Azure 區域中使用，並支援跨區域複寫。 如需 Azure NetApp Files 設定方法的詳細資訊，請參閱 [Azure Netapp files 的儲存體](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md)階層。

## <a name="reference-architecture"></a>參考架構

下圖說明透過 Azure ExpressRoute 連接至 Azure VMware 解決方案私人雲端的連線。 它會顯示 azure VMware 解決方案環境所存取的 azure NetApp Files 共用（掛接在 Azure VMware 解決方案 Vm 上）的使用方式。

![顯示 Azure VMware 解決方案架構 NetApp Files 的圖表。](media/net-app-files/net-app-files-topology.png)

本文涵蓋設定、測試及驗證 Azure NetApp Files 磁片區的指示，以作為 Azure VMware 解決方案 Vm 的檔案共用。 在此案例中，我們已使用 NFS 通訊協定。 Azure NetApp Files 和 Azure VMware 解決方案會建立在相同的 Azure 區域中。

## <a name="prerequisites"></a>先決條件 

> [!div class="checklist"]
> * 已啟用 Azure NetApp Files 的 azure 訂用帳戶
> * 適用于 Azure NetApp Files 的子網
> * Azure VMware 解決方案上的 Linux VM
> * Azure VMware 解決方案上的 Windows Vm

## <a name="regions-supported"></a>支援的區域

您可以在 [Azure 產品上依區域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)找到支援的區域清單。

## <a name="verify-pre-configured-azure-netapp-files"></a>確認預先設定的 Azure NetApp Files 

遵循下列文章中的逐步指示，建立 Azure NetApp Files 磁片區，並將其掛接到 Azure VMware 解決方案 Vm。

- [建立 NetApp 帳戶](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [設定容量集區](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [建立適用於 Azure NetApp Files 的 SMB 磁碟區](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [建立適用於 Azure NetApp Files 的 NFS 磁碟區](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [將子網路委派至 Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

下列步驟包括驗證在 azure NetApp Files Premium 服務層級的 Azure 中所建立的預先設定 Azure NetApp Files。

1. 在 [ **儲存體**] 下的 [Azure 入口網站] 中，選取 [ **Azure NetApp Files**]。 您已設定的 Azure NetApp Files 清單隨即顯示。 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="顯示預先設定之 Azure NetApp Files 清單的螢幕擷取畫面。"::: 

2. 選取已設定的 NetApp Files 帳戶以查看其設定。 例如，選取 [ **Contoso-anf2**]。 

3. 選取 **容量** 集區以驗證設定的集區。 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="螢幕擷取畫面，顯示用來查看已設定之 NetApp Files 帳戶的容量集區和磁片區選項。":::

    [容量集區] 頁面隨即開啟，其中顯示容量和服務層級。 在此範例中，存放集區設定為具有 Premium 服務層級的4個 TiB。

4. 選取 **磁片** 區以查看容量集區下建立的磁片區。  (請參閱先前的螢幕擷取畫面。 ) 

5. 選取磁片區以查看其設定。  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="螢幕擷取畫面，顯示容量集區下建立的磁片區。":::

    隨即開啟一個視窗，其中顯示磁片區的設定詳細資料。

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="顯示磁片區設定詳細資料的螢幕擷取畫面。":::

    您可以看到大小為 200 GiB 的磁片區 anfvolume 是在容量集區 anfpool1 中建立，並透過10.22.3.4：/ANFVOLUME. 匯出為 NFS 檔案共用。 Azure 虛擬網路中的一個私人 IP (VNet) 是針對 Azure NetApp Files 和要掛接在 VM 上的 NFS 路徑所建立。 如需有關 Azure NetApp Files 磁片區效能的詳細資訊，相對於大小 ( 「配額」 ) ，請參閱 [Azure Netapp Files 的效能考慮](../azure-netapp-files/azure-netapp-files-performance-considerations.md)。 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>確認預先設定的 Azure VMware 解決方案 VM 共用對應

在將 Azure NetApp Files 共用的可存取性展示至 Azure VMware 解決方案 VM 之前，請務必瞭解 SMB 和 NFS 共用對應。 只有在設定 SMB 或 NFS 磁片區之後，才可以將它們掛接，如這裡所述。

- SMB 共用：部署 SMB 磁片區之前，請先建立 Active Directory 連接。 指定的網域控制站必須可供 Azure NetApp Files 的委派子網存取，才能成功連線。 一旦在 Azure NetApp Files 帳戶內設定 Active Directory，就會在建立 SMB 磁片區時，顯示為可選取的專案。

- NFS 共用： Azure NetApp Files 有助於使用 NFS 或雙協定 (NFS 和 SMB) 來建立磁片區。 磁碟區的容量耗用量是根據其集區的佈建容量進行計算。 您可以使用命令列或/etc/fstab 專案，將 NFS 裝載至 Linux 伺服器。

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files 與 Azure VMware 解決方案的使用案例

以下是幾個引人注目的 Azure NetApp Files 使用案例。 
- 範圍設定檔管理
- Citrix 設定檔管理
- 遠端桌面服務設定檔管理
- Azure VMware 解決方案上的檔案共用

## <a name="next-steps"></a>後續步驟
- 瞭解 [Azure NetApp Files 的資源限制](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)。
- 請參閱 [Azure NetApp Files 網路規劃的指導方針](../azure-netapp-files/azure-netapp-files-network-topologies.md)。
- 瞭解 [Azure NetApp Files 磁片區的跨區域](../azure-netapp-files/cross-region-replication-introduction.md)複寫。 
- 查看 [有關 Azure NetApp Files 的常見問題](../azure-netapp-files/azure-netapp-files-faqs.md)。
