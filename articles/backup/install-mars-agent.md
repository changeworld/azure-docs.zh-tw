---
title: 安裝 Microsoft Azure 復原服務（MARS）代理程式
description: 瞭解如何安裝 Microsoft Azure 復原服務（MARS）代理程式來備份 Windows 電腦。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: cf6b332c308bb0224dbfed546f8b3ba819abcb02
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673102"
---
# <a name="install-the-azure-backup-mars-agent"></a>安裝 Azure 備份 MARS 代理程式

本文說明如何安裝 Microsoft Azure 復原服務（MARS）代理程式。 MARS 也稱為 Azure 備份代理程式。

## <a name="about-the-mars-agent"></a>關於 MARS 代理程式

Azure 備份使用 MARS 代理程式，從內部部署機器和 Azure Vm 備份檔案、資料夾和系統狀態。 這些備份會儲存在 Azure 的復原服務保存庫中。 您可以執行代理程式：

* 直接在內部部署 Windows 機器上。 這些機器可以直接備份至 Azure 中的復原服務保存庫。
* 在與 Azure VM 備份延伸模組並存執行 Windows 的 Azure Vm 上。 代理程式會備份 VM 上的特定檔案和資料夾。
* 在 Microsoft Azure 備份伺服器（MABS）實例或 System Center Data Protection Manager （DPM）伺服器上。 在此案例中，機器和工作負載會備份至 MABS 或 Data Protection Manager。 然後，MABS 或 Data Protection Manager 會使用 MARS 代理程式來備份至 Azure 中的保存庫。

可用來進行備份的資料取決於代理程式的安裝位置。

> [!NOTE]
> 一般而言，您會使用 VM 上的 Azure 備份擴充功能來備份 Azure VM。 這個方法會備份整個 VM。 如果您想要備份 VM 上的特定檔案和資料夾，請在擴充功能旁安裝並使用 MARS 代理程式。 如需詳細資訊，請參閱[內建 AZURE VM 備份的架構](backup-architecture.md#architecture-built-in-azure-vm-backup)。

![備份程序步驟](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>開始之前

* 瞭解[Azure 備份如何使用 MARS 代理程式來備份 Windows 電腦](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
* 瞭解在次要 MABS 或 Data Protection Manager 伺服器上執行 MARS 代理程式的[備份架構](backup-architecture.md#architecture-back-up-to-dpmmabs)。
* 請參閱 MARS 代理程式[支援的內容，以及您可以備份的內容](backup-support-matrix-mars-agent.md)。
* 如果您需要將伺服器或用戶端備份至 Azure，請確定您有 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立一個[免費](https://azure.microsoft.com/free/)帳戶。
* 確認您要備份之電腦上的網際網路存取權。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>修改儲存體複寫

根據預設，保存庫會使用[異地多餘儲存體（GRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

* 如果保存庫是您的主要備份機制，我們建議您使用 GRS。
* 您可以使用[本機多餘的儲存體（LRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)來降低 Azure 儲存體成本。

若要修改儲存體複寫類型：

1. 在新的保存庫中，選取 [**設定**] 區段下的 [**屬性**]。

1. 在 [**屬性**] 頁面的 [**備份**設定] 底下，選取 [**更新**]。

1. 選取儲存體複寫類型，然後選取 [**儲存]。**

    ![更新備份設定](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 設定保存庫並包含備份專案之後，即無法修改儲存體複寫類型。 如果您想要這樣做，您必須重新建立保存庫。
>

### <a name="verify-internet-access"></a>確認網際網路存取

如果您的電腦具有有限的網際網路存取權，請確定電腦或 proxy 上的防火牆設定允許下列 Url 和 IP 位址：

* URL
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* IP 位址
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>使用 Azure ExpressRoute

您可以使用公用對等互連（適用于舊的線路）和 Microsoft 對等互連，透過 Azure ExpressRoute 來備份您的資料。 不支援透過私用對等互連進行備份。

若要使用公用對等互連，請先確定存取下列網域和位址：

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

若要使用 Microsoft 對等互連，請選取下列 [服務]、[區域] 和 [相關的社區] 值：

* Azure Active Directory （12076:5060）
* Azure 區域（根據復原服務保存庫的位置）
* 根據復原服務保存庫的位置 Azure 儲存體

如需詳細資訊，請參閱[ExpressRoute 路由需求](https://docs.microsoft.com/azure/expressroute/expressroute-routing)。

> [!NOTE]
> 新線路的公用對等互連已被取代。

所有先前的 Url 和 IP 位址都會在埠443上使用 HTTPS 通訊協定。

## <a name="download-the-mars-agent"></a>下載 MARS 代理程式

下載 MARS 代理程式，讓您可以將它安裝在您想要備份的電腦上。

如果您已在任何電腦上安裝代理程式，請確定您正在執行最新版的代理程式。 在入口網站中尋找最新版本，或直接移至[下載](https://aka.ms/azurebackup_agent)。

1. 在保存庫的 [**消費者入門**] 底下，選取 [**備份**]。

    ![開啟備份目標](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. 在 [**您的工作負載在何處執行？** ] 底下，選取 [內部**部署**]。 即使您想要在 Azure VM 上安裝 MARS 代理程式，也請選取此選項。
1. 在 [**您要備份什麼？** ] 底下，選取 [檔案**和資料夾**]。 您也可以選取 [**系統狀態**]。 還有許多其他選項可供使用，但是只有當您執行次要備份伺服器時，才支援這些選項。 選取 [**準備基礎結構**]。

    ![設定檔案和資料夾](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. 針對 [**準備基礎結構**]，在 [**安裝復原服務代理程式**] 下，下載 MARS 代理程式。

    ![準備基礎結構](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. 在 [下載] 功能表中，選取 [**儲存**]。 根據預設，*MARSagentinstaller.exe* 檔案會儲存至 [下載] 資料夾。

1. 選取**已下載或使用最新的復原服務代理程式**，然後下載保存庫認證。

    ![下載保存庫認證](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. 選取 [儲存]。 檔案會下載到您的 [下載] 資料夾。 您無法開啟保存庫認證檔案。

## <a name="install-and-register-the-agent"></a>安裝和註冊代理程式

1. 在您要備份的電腦上執行*marsagentinstaller.exe。*
1. 在 [MARS 代理程式安裝精靈] 中，選取 [**安裝設定**]。 在這裡，選擇要安裝代理程式的位置，然後選擇快取的位置。 然後選取 [下一步]。
   * Azure 備份會先使用快取來儲存資料快照集，然後再將其傳送至 Azure。
   * 快取位置的可用空間應該等於您要備份的資料大小至少5%。

    ![選擇 MARS 代理程式安裝精靈中的安裝設定](./media/backup-configure-vault/mars1.png)

1. 在 [ **Proxy**設定] 中，指定在 Windows 電腦上執行的代理程式將如何連線到網際網路。 然後選取 [下一步]。

   * 如果您使用自訂 proxy，請指定任何必要的 proxy 設定和認證。
   * 請記住，代理程式需要存取[特定的 url](#before-you-start)。

    ![在 MARS wizard 中設定網際網路存取](./media/backup-configure-vault/mars2.png)

1. 若要**安裝**，請檢查必要條件，然後選取 [**安裝**]。
1. 安裝代理程式之後，選取 [**繼續註冊**]。
1. 在 [**註冊伺服器]** [ > 保存**庫識別**] 中，流覽至並選取您下載的認證檔案。 然後選取 [下一步]。

    ![使用註冊伺服器嚮導來新增保存庫認證](./media/backup-configure-vault/register1.png)

1. 在 [**加密設定**] 頁面上，指定將用來加密和解密電腦備份的複雜密碼。

    * 將複雜密碼儲存在安全的位置。 您需要它來還原備份。
    * 如果您遺失或忘記複雜密碼，Microsoft 將無法協助您復原備份資料。

1. 選取 [完成]。 現在已安裝代理程式，且您的電腦已註冊到保存庫。 您已準備好可以設定及排程備份。

## <a name="next-steps"></a>後續步驟

瞭解如何[使用 AZURE 備份 MARS 代理程式來備份 Windows 機器](backup-windows-with-mars-agent.md)
