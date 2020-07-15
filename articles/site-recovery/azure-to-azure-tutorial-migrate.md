---
title: 使用 Azure Site Recovery 將 Azure VM 移至另一個區域
description: 使用 Azure Site Recovery，將 Azure IaaS VM 從一個 Azure 區域移至另一個區域。
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 09a00d2c6a889f396e5c18da29530c94a624568b
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134424"
---
# <a name="move-azure-vms-to-another-region"></a>將 Azure VM 移動到另一個區域

有許多情況會讓您想要將現有的 Azure IaaS 虛擬機器 (VM) 從某個區域移到另一個。 例如，您會想要提高現有 VM 的可靠性和可用性，以改善管理性，或基於治理原因而將它移動。 如需詳細資訊，請參閱 [Azure VM 移動概觀](azure-to-azure-move-overview.md)。 

您可以針對商務持續性和災害復原 (BCDR)，使用 [Azure Site Recovery](site-recovery-overview.md) 服務來管理和協調內部部署電腦和 Azure VM 的災害復原。 也可以使用站台復原管理將 Azure VM 移動至次要地區的作業。

在本教學課程中，您將：

> [!div class="checklist"]
> 
> * 驗證移動的先決條件
> * 準備來源 VM 和目標區域
> * 複製資料並啟用複寫
> * 測試設定並執行移動
> * 刪除來源區域中的資源
> 
> [!NOTE]
> 本教學課程顯示如何將 Azure VM 原封不動移到另一個區域。 如果需要將可用性設定組中的 VM 移到不同區域中固定區域的 VM，藉此改善可用性，請參閱[將 Azure VM 移到可用性區域教學課程](move-azure-vms-avset-azone.md)。

## <a name="prerequisites"></a>Prerequisites

- 請確定 Azure VM 確實位在您要從中移動 Azure 區域中。
- 確認您選擇的[來源區域 - 目標區域組合受到支援](./azure-to-azure-support-matrix.md#region-support)，並做出與目標區域有關的明智決策。
- 請確定您了解[情節架構和元件](azure-to-azure-architecture.md)。
- 檢閱[支援限制和需求](azure-to-azure-support-matrix.md)。
- 驗證帳戶權限。 如果您已建立免費的 Azure 帳戶，那您就是自己的訂用帳戶管理員。 如果您不是訂用帳戶管理員，請與系統管理員合作以指派您所需的權限。 若要使用 Azure Site Recovery 啟用 VM 複寫並複製必要資料，您必須具備：

    - 在 Azure 資源中建立 VM 的權限。 「虛擬機器參與者」內建角色具有以下權限：
    - 在所選取資源群組中建立 VM 的權限
    - 在所選取虛擬網路中建立 VM 的權限
    - 寫入所選取儲存體帳戶的權限
    
    - 管理 Azure Site Recovery 作業的權限。 「Site Recovery 參與者」角色具有在復原服務保存庫中管理 Site Recovery 作業所需的所有權限。

- 確定您要移動的 Azure VM 中有所有最新的根憑證。 如果 VM 中沒有最新的根憑證，則會因為安全性條件約束而無法將資料複製到目標區域。

- 若為 Windows VM，請安裝所有最新的 Windows 更新，讓所有的受信任根憑證都在機器上。 在中斷連線的環境中，請遵循您組織的標準 Windows Update 和憑證更新程序。
    
- 若為 Linux VM，請遵循 Linux 散發者提供的指引，以取得最新的受信任根憑證及 VM 的憑證撤銷清單。
- 請確定您未使用驗證 Proxy 來控制所要移動 VM 的網路連線能力。

- 如果嘗試移動的 VM 無法存取網際網路，或使用防火牆 Proxy 控制輸出存取，請[檢查需求](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)。

- 識別來源網路配置，以及您目前使用的所有資源。 這包括但不限於負載平衡器、網路安全性群組 (NSG)、公用 IP 等。

- 確認您的 Azure 訂用帳戶允許您在用於災害復原的目標區域中建立 VM。 請連絡支援人員啟用所需的配額。

- 確定您的訂用帳戶有足夠的資源，可支援大小與來源 VM 相符的 VM。 如果您使用 Site Recovery 將資料複製到目標，Site Recovery 將會為目標 VM 選擇相同或盡可能接近的大小。

- 務必為來源網路配置中識別的每個元件建立目標資源。 這個步驟很重要，因為這可確定在將 VM 完全移轉至目標區域後，將具備與來源相同的所有功能和特性。

     > [!NOTE] 
     > 當您啟用來源 VM 的複寫時，Azure Site Recovery 會自動探索和建立虛擬網路。 您也可以在啟用複寫的使用者流程中，預先建立網路並指派給 VM。 如後面內容所述，您需要以手動方式在在目標區域中建立任何其他資源。

    若要根據來源 VM 設定建立最常用且符合需求的網路資源，請參閱下列文件：
    - [網路安全性群組](../virtual-network/manage-network-security-group.md)
    - [負載平衡器](../load-balancer/index.yml)
    -  [公用 IP](../virtual-network/virtual-network-public-ip-address.md)
    - 如需任何其他網路元件，請參閱[網路文件](../index.yml?pivot=products&panel=network)。



## <a name="prepare"></a>準備
下列步驟說明如何使用 Azure Site Recovery 作為解決方案來準備要進行移動的虛擬機器。 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>在來源區域以外的任何區域中建立保存庫

1. 登入 [Azure 入口網站](https://portal.azure.com) > [復原服務]  。
1. 選取 [建立資源]   > [管理工具]   > [備份和 Site Recovery]  。
1. 在 [名稱]  中，指定易記名稱 [ContosoVMVault]  。 如果您有多個訂用帳戶，請選取適當的一個。
1. 建立資源群組 **ContosoRG**。
1. 指定 Azure 區域。 若要查看支援的區域，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
1. 在 [Recovery Services 保存庫]  中，選取 [概觀]   > [ConsotoVMVault]   > [+複寫]  。
1. 在 [來源]  中，選取 [Azure]  。
1. 在 [來源位置]  中，選取 VM 目前執行所在的來源 Azure 區域。
1. 選取 Resource Manager 部署模型。 然後，選取 [來源訂用帳戶]  和 [來源資源群組]  。
1. 選取 [確定]  來儲存設定。

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>為 Azure VM 啟用複寫並開始複製資料

Site Recovery 會擷取與訂用帳戶和資源群組建立關聯的 VM 清單。

1. 在下一個步驟中，選取想要移動的 VM，然後選取 [確定]  。
1. 在 [設定]  中，選取 [災害復原]  。
1. 在 [設定災害復原]   > [目標區域]  中，選取要作為複寫目的地的目標區域。
1. 在本教學課程中，接受其他預設設定。
1. 選取 [啟用複寫]  。 這個步驟會啟動一個作業來啟用 VM 的複寫。

    ![啟用複寫](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="move"></a>移動

下列步驟說明如何執行移至目標區域的作業。

1. 請移至保存庫。 按一下 [設定]   > [複寫的項目]  ，選取 VM，然後選取 [容錯移轉]  。
2. 在 [容錯移轉]  中，選取 [最新]  。
3. 選取 [Shut down machine before beginning failover] \(先將機器關機再開始容錯移轉)  。 Site Recovery 嘗試先關閉來源 VM，再觸發容錯移轉。 即使關機失敗，仍會繼續容錯移轉。 您可以 [作業]  頁面上追蹤容錯移轉進度。
4. 作業完成後，請確認 VM 如預期出現在目標 Azure 區域中。


## <a name="discard"></a>捨棄 

如果您已檢查過移動的 VM，而且需要變更容錯移轉復原點或想要回到先前的復原點，請在 [複寫的項目]  中，以滑鼠右鍵選取 VM > [變更復原點]  。 此步驟可讓您選擇指定不同復原點，並容錯移轉至該復原點。 


## <a name="commit"></a>Commit 

檢查過移動的 VM 並準備好認可變更後，請在 [複寫的項目]  中，以滑鼠右鍵選取 VM > [認可]  。 此步驟會完成移至目標區域的程序。 請等候認可作業完成。

## <a name="clean-up"></a>清除

下列步驟會引導您了解如何清除來源區域以及用於移動的相關資源。

針對用於移動的所有資源：

- 移至 VM。 選取 [停用複寫]  。 此步驟會停止複製 VM 資料的程序。

   > [!IMPORTANT]
   > 請務必執行此步驟，以避免產生 Azure Site Recovery 複寫費用。

如果不打算重複使用任何來源資源，請完成這些額外步驟：

1. 在您於[必要條件](#prerequisites)中識別的來源區域內，刪除所有相關的網路資源。
1. 刪除來源區域中對應的儲存體帳戶。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure VM 移到不同的 Azure 區域。 您現在可以為所移動的 VM 設定災害復原。

> [!div class="nextstepaction"]
> [在移轉之後設定災害復原](azure-to-azure-quickstart.md)
