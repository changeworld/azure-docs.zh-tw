---
title: 準備 Azure 以使用 Azure Site Recovery 進行 Hyper-V 災害復原
description: 了解如何準備 Azure，以使用 Azure Site Recovery 進行內部部署 Hyper-V VM 的災害復原
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4e28a46b8d231b3f228a95ea06b7b9cada50de54
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498985"
---
# <a name="prepare-azure-resources-for-hyper-v-disaster-recovery"></a>準備 Azure 資源以進行 Hyper-V 災害復原

 [Azure Site Recovery](site-recovery-overview.md) 可藉由確保商務應用程式在計劃性與非計劃性中斷期間持續執行，來協助提供商務持續性和災害復原 (BCDR)。 Site Recovery 會管理並協調內部部署機器和 Azure 虛擬機器 (VM) 的災害復原，包括複寫、容錯移轉和復原。

本文是一系列中的第一個教學課程，將說明如何為內部部署 Hyper-V VM 設定災害復原。

> [!NOTE]
> 教學課程的設計訴求是示範案例的最簡單部署路徑。 這些教學課程會使用預設選項 (如果有的話)，而不會顯示所有可能的設定與路徑。 如需詳細資訊，請參閱每個對應案例的「操作說明」一節。

本教學課程會說明如何在您想要將內部部署 VM (Hyper-V) 複寫至 Azure 時，準備好 Azure 元件。 您將學習如何：

> [!div class="checklist"]
> * 確認您的 Azure 帳戶具有複寫權限。
> * 建立 Azure 儲存體帳戶，以儲存複寫機器的映像。
> * 建立復原服務保存庫，以保存 VM 的中繼資料和組態資訊，以及其他複寫元件。
> * 設定 Azure 網路。 在容錯移轉後建立的 Azure VM 會加入此網路。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="sign-in"></a>登入

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="verify-account-permissions"></a>確認帳戶權限

如果您剛建立免費的 Azure 帳戶，您就是該訂用帳戶的管理員。 如果您不是管理員，請與管理員合作以指派您所需的權限。 若要啟用新虛擬機器的複寫，您必須具有權限才能：

- 在所選的資源群組中建立 VM。
- 在所選的虛擬網路中建立 VM。
- 寫入所選取的儲存體帳戶。

若要完成這些工作，應將虛擬機器參與者內建角色指派至您的帳戶。 若要管理保存庫中的 Site Recovery 作業，應將 Site Recovery 參與者內建角色指派至您的帳戶。

## <a name="create-a-storage-account"></a>建立儲存體帳戶

複寫機器的映像會保留在 Azure 儲存體中。 當您從內部部署容錯移轉至 Azure 時，會從儲存體建立 Azure VM。 儲存體帳戶與復原服務保存庫必須位於相同的區域。

1. 在 [Azure 入口網站](https://portal.azure.com)功能表中，選取 [建立資源]   > [儲存體]   > [儲存體帳戶 - Blob、檔案、資料表、佇列]  。
2. 在 [建立儲存體帳戶]  中，輸入帳戶的名稱。  您所選擇的名稱必須是 Azure 中唯一的，長度為 3 到 24 個字元，而且只能使用小寫字母和數字。 本教學課程使用 **contosovmsacct1910171607**。
3. 在 [部署模型]  中選取 [Resource Manager]  。
4. 在 [帳戶種類]  中選取 [儲存體 (一般用途 v1)]  。 請勿選取 Blob 儲存體。
5. 在 [複寫]  中，針對儲存體備援選取預設的 [讀取權限異地備援儲存體]  。 讓「需要安全傳輸」設定維持「停用」。
6. 在 [效能]  中選取 [標準]  。 接下來，在 [存取層]  中，選取預設選項 [經常性存取層]  。
7. 在 [訂用帳戶]  中，選擇您要在其中建立新儲存體帳戶的訂用帳戶。
8. 在 [資源群組]  中，輸入新的資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 本教學課程使用 **ContosoRG**。
9. 在 [位置]  中，選擇儲存體帳戶的地理位置。 本教學課程使用**西歐**。
10. 選取 [建立]  以建立儲存體帳戶。

   ![建立儲存體帳戶選項的螢幕擷取畫面。](media/tutorial-prepare-azure/create-storageacct.png)

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫。

1. 在 Azure 入口網站中選取 [+建立資源]  ，然後在 Azure Marketplace 中搜尋 [復原服務]。
2. 選取 [備份與復原 (OMS)]  。 接下來，在 [備份與復原]  頁面上，選取 [建立]  。
1. 在 [復原服務保存庫] > [名稱]  中，輸入用來識別保存庫的自訂名稱。 在此教學課程中，使用 **ContosoVMVault**。
2. 在 [資源群組]  中選取現有的資源群組，或建立新的資源群組。 本教學課程使用 **contosoRG**。
3. 在 [位置]  中，選取要設置保存庫的區域。 本教學課程使用**西歐**。
4. 若要從儀表板快速存取保存庫，請按一下 [釘選到儀表板]   > [建立]  。

![建立復原服務保存庫頁面的螢幕擷取畫面。](./media/tutorial-prepare-azure/new-vault-settings.png)

新的保存庫會出現在 [儀表板]   > [所有資源]  上，以及 [復原服務保存庫]  主頁面上。

## <a name="set-up-an-azure-network"></a>設定 Azure 網路

當 Azure VM 在容錯移轉後從儲存體建立時，會加入此網路。

1. 在 [Azure 入口網站](https://portal.azure.com)中，選取 **[建立資源]**  >  **[網路]**  >  **[虛擬網路]** 。 保持選取 [Resource Manager] 作為部署模型。
2. 在 [名稱]  中，輸入網路名稱。 此名稱必須是 Azure 資源群組中的唯一名稱。 本教學課程使用 **ContosoASRnet**。
3. 指定要在其中建立網路的資源群組。 此教學課程使用現有的資源群組 **contosoRG**。
4. 在 [位址範圍]  中，輸入 **10.0.0.0/24** 作為網路範圍。 此網路沒有子網路。
5. 在 [訂用帳戶]  中，選取要在其中建立網路的訂用帳戶。
6. 在 [位置]  中，選擇 [西歐]  。 此網路必須位於與復原服務保存庫相同的區域中。
7. 保留基本 DDoS 保護的預設選項，且網路上沒有任何服務端點。
8. 選取 [建立]  。

![建立虛擬網路選項的螢幕擷取畫面。](media/tutorial-prepare-azure/create-network.png)

虛擬網路的建立會耗用幾秒鐘時間。 建立後，您會在 Azure 入口網站儀表板中看到它。

## <a name="useful-links"></a>實用的連結

了解：
- [Azure 網路](../virtual-network/virtual-networks-overview.md)
- [受控磁碟](../virtual-machines/windows/managed-disks-overview.md)



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [準備內部部署 Hyper-V 基礎結構以進行 Azure 的災害復原](hyper-v-prepare-on-premises-tutorial.md)
