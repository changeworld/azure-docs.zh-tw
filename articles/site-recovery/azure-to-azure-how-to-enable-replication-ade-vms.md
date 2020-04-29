---
title: 在 Azure Site Recovery 中為加密的 Azure Vm 啟用複寫
description: 本文說明如何使用 Site Recovery，將 Azure 磁碟加密啟用的 Vm 的複寫設定至另一個 Azure 區域。
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 2bbb02df782439d934e96e7c16f28b9c11cc01fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408628"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>將已啟用 Azure 磁碟加密的虛擬機器複寫至另一個 Azure 區域

本文說明如何將已啟用 Azure 磁碟加密（ADE）的 Azure Vm 複寫到另一個 Azure 區域。

>[!NOTE]
> Site Recovery 目前支援 ADE，而在執行 Windows 作業系統的 Vm 上則不會有 Azure Active Directory （AAD）。 針對 Linux 作業系統，我們只支援不含 AAD 的 ADE。 此外，對於執行 ADE 1.1 （不含 AAD）的機器，Vm 必須使用受控磁片。 不支援具有非受控磁片的 Vm。 如果您從 ADE 0.1 （含 AAD）切換到1.1，您必須在啟用1.1 之後，停用複寫並啟用 VM 的複寫。


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a>必要的使用者權限
Site Recovery 要求使用者必須擁有在目的地區域中建立金鑰保存庫的許可權，並將金鑰從來源區域金鑰保存庫複製到目的地區域金鑰保存庫。

若要啟用從 Azure 入口網站複寫已啟用磁片加密的 Vm，使用者需要**來源區域和目的地區域**金鑰保存庫的下列許可權。

- 金鑰保存庫權限
    - 列出、建立和取得
    
- 金鑰保存庫祕密權限
    - 秘密管理作業
        - 取得、列出及設定
    
- 金鑰保存庫金鑰許可權（只有在 Vm 使用金鑰加密金鑰來加密磁片加密金鑰時才需要）
    - 金鑰管理作業
        - 取得、列出及建立
    - 密碼編譯作業
        - 解密並加密

若要管理許可權，請移至入口網站中的金鑰保存庫資源。 為使用者新增必要的許可權。 下列範例示範如何對來源區域中的金鑰保存庫*ContosoWeb2Keyvault*啟用許可權。

1. 前往**Home** > **Keyvaults** > **ContosoWeb2KeyVault > 存取原則**。

   ![金鑰保存庫許可權視窗](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. 您會看到沒有任何使用者權限。 選取 [新增]****。 輸入使用者和許可權資訊。

   ![Keyvault 許可權](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

如果啟用嚴重損壞修復（DR）的使用者沒有複製金鑰的許可權，具有適當許可權的安全性系統管理員可以使用下列腳本，將加密秘密和金鑰複製到目的地區域。

若要對許可權進行疑難排解，請參閱本文稍後的[key vault 許可權問題](#trusted-root-certificates-error-code-151066)。

>[!NOTE]
>若要啟用從入口網站複寫已啟用磁片加密的 Vm，您至少需要金鑰保存庫、秘密和金鑰的「列出」許可權。

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>使用 PowerShell 腳本將磁片加密金鑰複製到 DR 區域

1. [開啟 "CopyKeys" 原始腳本程式碼](https://aka.ms/ade-asr-copy-keys-code)。
2. 將腳本複製到檔案中，並將它命名為**Copy-keys**。
3. 開啟 Windows PowerShell 應用程式，然後移至您儲存檔案的資料夾。
4. 執行 Copy-keys。
5. 提供 Azure 認證來登入。
6. 選取您 VM 的 **Azure 訂用帳戶**。
7. 等待資源群組載入，然後選取 Vm 的**資源群組**。
8. 從顯示的清單中選取 Vm。 只有針對磁片加密啟用的 Vm 才會列在清單中。
9. 選取**目標位置**。

    - **磁片加密金鑰保存庫**
    - **金鑰加密金鑰保存庫**

   根據預設，Site Recovery 會在目標區域中建立新的金鑰保存庫。 保存庫的名稱具有以來源 VM 磁片加密金鑰為基礎的 "asr" 尾碼。 如果 Site Recovery 所建立的金鑰保存庫已經存在，就會重複使用它。 如有需要，請從清單中選取不同的金鑰保存庫。

## <a name="enable-replication"></a>啟用複寫

在此範例中，主要 Azure 區域是東亞，而次要地區則是南部東亞。

1. 在保存庫中，選取 [ **+** 複寫]。
2. 請注意下欄欄位。
    - **來源**：VM 的起點，在此案例中為 **Azure**。
    - **來源位置**：您要保護虛擬機器的 Azure 區域。 在此範例中，來源位置為「東亞」。
    - **部署模型**：來源機器的 Azure 部署模型。
    - **來源訂用帳戶**：來源虛擬機器所屬的訂用帳戶。 它可以是與您的復原服務保存庫位於相同 Azure Active Directory 租使用者中的任何訂用帳戶。
    - **資源群組**：來源虛擬機器所屬的資源群組。 在下一個步驟中，會列出所選資源群組中的所有 Vm 以提供保護。

3. 在**虛擬機器** > **選取 [虛擬機器**] 中，選取您要複寫的每個 VM。 您只能選取可以啟用複寫的機器。 然後選取 **[確定]**。

4. 在 [**設定**] 中，您可以設定下列目標網站設定。

    - **目標位置**：將複寫來源虛擬機器資料的位置。 Site Recovery 會根據選取的電腦位置，提供適當的目的地區域清單。 我們建議您使用與復原服務保存庫位置相同的位置。
    - **目標訂**用帳戶：用於嚴重損壞修復的目標訂用帳戶。 根據預設，目標訂用帳戶會與來源訂用帳戶相同。
    - **目標資源群組**：所有已複寫虛擬機器所屬的資源群組。 根據預設，Site Recovery 會在目的地區域中建立新的資源群組。 名稱會取得 "asr" 尾碼。 如果 Azure Site Recovery 所建立的資源群組已經存在，就會重複使用它。 您也可以選擇進行自訂，如下一節所示。 目標資源群組的位置可以是任何 Azure 區域，但裝載來源虛擬機器所在的區域除外。
    - **目標虛擬網路**：根據預設，Site Recovery 會在目的地區域中建立新的虛擬網路。 名稱會取得 "asr" 尾碼。 它會對應至您的來源網路，並用於任何未來的保護。 [深入了解](site-recovery-network-mapping-azure-to-azure.md)網路對應。
    - **目標儲存體帳戶（如果您的來源 vm 不使用受控磁片）**：根據預設，Site Recovery 會藉由模擬您的來源 vm 儲存體設定來建立新的目標儲存體帳戶。 如果儲存體帳戶已經存在，就會重複使用它。
    - **複本受控磁片（如果您的來源 VM 使用受控磁片）**： Site Recovery 會在目的地區域中建立新的複本受控磁片，以反映來源 vm 的受控磁片與來源 vm 的受控磁片相同的儲存體類型（標準或高階）。
    - 快取**儲存體帳戶**： Site Recovery 需要在來源區域中有額外的儲存體帳戶（稱為快取*儲存體*）。 系統會追蹤來源 Vm 上的所有變更，並將其傳送至快取儲存體帳戶。 然後，它們會複寫到目標位置。
    - **可用性設定組**：根據預設，Site Recovery 會在目的地區域中建立新的可用性設定組。 名稱具有 "asr" 尾碼。 如果 Site Recovery 所建立的可用性設定組已經存在，則會重複使用。
    - **磁片加密金鑰保存庫**：根據預設，Site Recovery 會在目的地區域中建立新的金鑰保存庫。 它具有以來源 VM 磁片加密金鑰為基礎的 "asr" 尾碼。 如果 Azure Site Recovery 所建立的金鑰保存庫已經存在，則會重複使用。
    - **金鑰加密金鑰保存庫**：根據預設，Site Recovery 會在目標區域中建立新的金鑰保存庫。 名稱具有以來源 VM 金鑰加密金鑰為基礎的 "asr" 尾碼。 如果 Azure Site Recovery 所建立的金鑰保存庫已經存在，則會重複使用。
    - **複寫原則**：定義復原點保留歷程記錄和應用程式一致快照集頻率的設定。 根據預設，Site Recovery 會使用*24 小時*的預設設定來建立新的複寫原則，以用於復原點保留，而*60 分鐘*會用於應用程式一致快照集頻率。

## <a name="customize-target-resources"></a>自訂目標資源

請遵循下列步驟來修改 Site Recovery 預設目標設定。

1. 選取 [目標訂閱] 旁的 [**自訂**]，以修改預設的目標訂用帳戶。 從 Azure AD 租使用者中可用的訂用帳戶清單中選取訂用帳戶。

2. 選取 [資源群組、網路、儲存體和可用性設定組] 旁的 [**自訂**] 來修改下列預設設定：
    - 針對 [**目標資源群組**]，從訂用帳戶的目標位置中的資源群組清單選取資源群組。
    - 針對 [**目標虛擬網路**]，從目標位置的虛擬網路清單中選取網路。
    - 針對**可用性設定組**，您可以將可用性設定組設定新增至 VM （如果它們是來源區域中可用性設定組的一部分）。
    - 針對 [**目標儲存體帳戶**]，選取要使用的帳戶。

2. 選取 [加密設定] 旁的 [**自訂**] 來修改下列預設設定：
   - 針對 [**目標磁片加密金鑰保存庫**]，從訂用帳戶的目標位置中的金鑰保存庫清單選取目標磁片加密金鑰保存庫。
   - 針對 [**目標金鑰加密金鑰保存庫**]，從訂用帳戶的目標位置中的金鑰保存庫清單選取目標金鑰加密金鑰保存庫。

3. 選取 [**建立目標資源** > ] [**啟用**複寫]。
4. 在 Vm 啟用複寫之後，您可以在 [複寫的**專案**] 底下檢查 vm 的健全狀態。

>[!NOTE]
>在初始複寫期間，狀態可能需要一些時間才能重新整理，而不會有明顯的進度。 按一下 **[** 重新整理] 以取得最新狀態。

## <a name="update-target-vm-encryption-settings"></a>更新目標 VM 加密設定
在下列案例中，您將需要更新目標 VM 加密設定：
  - 您已在 VM 上啟用 Site Recovery 複寫。 稍後，您已在來源 VM 上啟用磁片加密。
  - 您已在 VM 上啟用 Site Recovery 複寫。 稍後，您已變更來源 VM 上的磁片加密金鑰或金鑰加密金鑰。

您可以使用[腳本](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script)將加密金鑰複製到目的地區域，然後在 [復原**服務保存庫** > ] [複寫的*專案* > ]**屬性** > [**計算與網路**] 中更新目標加密設定。

![[更新 ADE 設定] 對話方塊視窗](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>針對 Azure 至 Azure VM 複寫期間的 key vault 許可權問題進行疑難排解

Azure Site Recovery 至少需要來源區域金鑰保存庫的讀取權限，以及目的地區域金鑰保存庫的寫入權限，才能讀取秘密並將其複製到目的地區域金鑰保存庫。 

**原因1：** 您沒有**來源區域金鑰保存庫**的「取得」許可權，無法讀取金鑰。 </br>
**修正方式：** 不論您是否為訂用帳戶管理員，請務必擁有金鑰保存庫的「取得」許可權。

1. 前往來源區域金鑰保存庫，在此範例中為 "ContososourceKeyvault" >**存取原則** 
2. 在 [**選取主體**] 底下，新增您的使用者dradmin@contoso.com名稱，例如： ""
3. 在 [**金鑰許可權**] 下選取 [取得] 
4. 在 [**秘密許可權**] 下選取 [取得] 
5. 儲存存取原則

**原因2：** 您沒有**目的地區域金鑰保存庫**的必要許可權，無法寫入金鑰。 </br>

*例如*：您嘗試複寫的 VM 在來源區域上具有 Key vault *ContososourceKeyvault* 。
您擁有來源區域金鑰保存庫的擁有權限。 但在保護期間，您會選取已建立的金鑰保存庫 ContosotargetKeyvault，這不具有許可權。 發生錯誤。

[目標金鑰保存庫](#required-user-permissions)上所需的許可權

**修正方式：** 移至**Home** > **Keyvaults** > **ContosotargetKeyvault** > **Access 原則**並新增適當的許可權。

## <a name="next-steps"></a>後續步驟

[深入了解](site-recovery-test-failover-to-azure.md)執行測試容錯移轉。
