---
title: 安裝 Microsoft Azure 恢復服務 （MARS） 代理
description: 瞭解如何安裝 Microsoft Azure 恢復服務 （MARS） 代理以備份 Windows 電腦。
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: b9a6791709d5aff82d11bbf10e5f084fd8c1a000
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247757"
---
# <a name="install-the-azure-backup-mars-agent"></a>安裝 Azure 備份 MARS 代理

本文介紹如何安裝 Microsoft Azure 恢復服務 （MARS） 代理。 MARS 也稱為 Azure 備份代理。

## <a name="about-the-mars-agent"></a>關於 MARS 代理程式

Azure 備份使用 MARS 代理從本地電腦和 Azure VM 備份檔案、資料夾和系統狀態。 這些備份存儲在 Azure 中的恢復服務保存庫中。 您可以運行代理：

* 直接在本地 Windows 電腦上。 這些電腦可以直接備份到 Azure 中的恢復服務保存庫。
* 在與 Azure VM 備份擴展並行運行 Windows 的 Azure VM 上。 代理備份 VM 上的特定檔和資料夾。
* 在 Microsoft Azure 備份伺服器 （MABS） 實例或系統中心資料保護管理器 （DPM） 伺服器上。 在這種情況下，電腦和工作負載將備份到 MABS 或資料保護管理器。 然後，MABS 或資料保護管理器使用 MARS 代理備份到 Azure 中的保存庫。

可用於備份的資料取決於代理的安裝位置。

> [!NOTE]
> 通常，通過使用 VM 上的 Azure 備份擴展來備份 Azure VM。 此方法備份整個 VM。 如果要備份 VM 上的特定檔和資料夾，請在副檔名旁邊安裝並使用 MARS 代理。 有關詳細資訊，請參閱內置[Azure VM 備份的體系結構](backup-architecture.md#architecture-built-in-azure-vm-backup)。

![備份程序步驟](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>開始之前

* 瞭解[Azure 備份如何使用 MARS 代理備份 Windows 電腦](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)。
* 瞭解在輔助 MABS 或資料保護管理器伺服器上運行 MARS 代理的[備份體系結構](backup-architecture.md#architecture-back-up-to-dpmmabs)。
* 查看支援[的內容以及 MARS 代理可以備份的內容](backup-support-matrix-mars-agent.md)。
* 如果需要將伺服器或用戶端備份到 Azure，請確保具有 Azure 帳戶。 如果您沒有帳戶，只需幾分鐘即可創建[免費](https://azure.microsoft.com/free/)帳戶。
* 驗證要備份的電腦上的 Internet 訪問。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>修改存儲複製

預設情況下，保存庫使用[異地冗余存儲 （GRS）。](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)

* 如果保存庫是您的主要備份機制，我們建議您使用 GRS。
* 您可以使用[本地冗余存儲 （LRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)來降低 Azure 存儲成本。

要修改存儲複製類型，

1. 在新保存庫中，在 **"設置"** 部分下選擇 **"屬性**"。

1. 在 **"屬性"** 頁上，在 **"備份配置**"下，選擇 **"更新**"。

1. 選擇存儲複製類型，然後選擇 **"保存**"。

    ![更新備份配置](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> 設置保存庫並包含備份項後，無法修改存儲複製類型。 如果要執行此操作，則需要重新創建保存庫。
>

### <a name="verify-internet-access"></a>確認網際網路存取

如果您的電腦的 Internet 訪問受限，請確保電腦或代理上的防火牆設置允許以下 URL 和 IP 位址：

* URL
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* IP 位址
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>使用 Azure 快速路由

您可以使用公共對等互連（可用於舊電路）和 Microsoft 對等互連，通過 Azure ExpressRoute 備份資料。 不支援通過私有對等互連進行備份。

要使用公共對等互連，首先確保訪問以下域和位址：

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

要使用 Microsoft 對等互連，請選擇以下服務、區域和相關社區值：

* Azure 活動目錄 （12076：5060）
* Azure 區域，根據恢復服務保存庫的位置
* Azure 存儲，根據恢復服務保存庫的位置

有關詳細資訊，請參閱[快速路由路由要求](https://docs.microsoft.com/azure/expressroute/expressroute-routing)。

> [!NOTE]
> 公共對等互連被棄用用於新電路。

所有前面的 URL 和 IP 位址都使用埠 443 上的 HTTPS 協定。

### <a name="private-endpoints"></a>專用終結點

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>下載 MARS 代理

下載 MARS 代理，以便可以在要備份的電腦上安裝它。

如果您已在任何電腦上安裝了代理，請確保正在運行最新版本的代理。 在門戶中查找最新版本，或直接轉到[下載](https://aka.ms/azurebackup_agent)。

1. 在保存庫中，**在"入門"** 下，選擇 **"備份**"。

    ![打開備份目標](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. 在 **"工作負荷在何處運行？"** 下，選擇 **"本地**"。 即使要在 Azure VM 上安裝 MARS 代理，也會選擇此選項。
1. 在"**要備份什麼"下，** 選擇 **"檔和資料夾**"。 您還可以選擇**系統狀態**。 許多其他選項都可用，但僅在運行輔助備份伺服器時支援這些選項。 選擇 **"準備基礎結構**"。

    ![設定檔和資料夾](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. 對於**準備基礎結構**，在**安裝恢復服務代理**下，下載 MARS 代理。

    ![準備基礎結構](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. 在下載功能表中，選擇 **"保存**"。 根據預設，*MARSagentinstaller.exe* 檔案會儲存至 [下載] 資料夾。

1. 選擇 **"已下載或使用最新的恢復服務代理**"，然後下載保存庫憑據。

    ![下載保存庫認證](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. 選取 [儲存]****。 該檔將下載到您的下載資料夾。 無法打開保存庫憑據檔。

## <a name="install-and-register-the-agent"></a>安裝和註冊代理程式

1. 在要備份的電腦上運行*MARSagent 安裝程式.exe*檔。
1. 在 MARS 代理設置嚮導中，選擇**安裝設置**。 在此處，選擇安裝代理的位置，然後選擇緩存的位置。 然後選擇 **"下一步**"。
   * Azure 備份使用緩存在將資料快照發送到 Azure 之前存儲資料快照。
   * 緩存位置的可用空間應至少等於要備份的資料大小的 5%。

    ![在 MARS 代理設置嚮導中選擇安裝設置](./media/backup-configure-vault/mars1.png)

1. 對於**代理配置**，指定在 Windows 電腦上運行的代理如何連接到 Internet。 然後選擇 **"下一步**"。

   * 如果使用自訂代理，請指定任何必要的代理設置和憑據。
   * 請記住，代理需要訪問特定的[URL。](#before-you-start)

    ![在 MARS 嚮導中設置互聯網接入](./media/backup-configure-vault/mars2.png)

1. 對於**安裝**，請查看先決條件，然後選擇 **"安裝**"。
1. 安裝代理後，選擇"**繼續註冊**"。
1. 在**註冊伺服器嚮導** > **保存庫標識中**，流覽並選擇要下載的憑據檔。 然後選擇 **"下一步**"。

    ![使用註冊伺服器嚮導添加保存庫憑據](./media/backup-configure-vault/register1.png)

1. 在 **"加密設定"** 頁上，指定將用於加密和解密電腦備份的密碼。

    * 將密碼保存在安全位置。 您需要它來還原備份。
    * 如果您丟失或忘記了密碼，Microsoft 無法説明您恢復備份資料。

1. 選取 [完成]****。 代理現已安裝，並且您的電腦已註冊到保存庫。 您已準備好可以設定及排程備份。

## <a name="next-steps"></a>後續步驟

瞭解如何使用[Azure 備份 MARS 代理備份 Windows 電腦](backup-windows-with-mars-agent.md)
