---
title: 在 Azure Site Recovery 中啟用加密 Azure Vm 的複寫
description: 本文說明如何使用 Site Recovery，設定從某個 Azure 區域到另一個 Azure 區域的已啟用 Azure 磁碟加密 Vm 的複寫。
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: fa4d61599e102f9a2580e704ee7a02486067daa2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86135791"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>將已啟用 Azure 磁碟加密的虛擬機器複寫至另一個 Azure 區域

本文說明如何將已啟用 Azure 磁碟加密 (ADE) 的 Azure Vm 從一個 Azure 區域複寫至另一個 Azure 區域。

>[!NOTE]
> 對於執行 Windows 作業系統的 VM，Site Recovery 目前支援 ADE (無論是否具有 Azure Active Directory (AAD))。 對於 Linux 作業系統，我們僅支援不具 AAD 的 ADE。 此外，機器若執行 ADE 1.1 (不具 AAD)，則 VM 必須使用受控磁碟。 使用非受控磁碟的 VM 不受支援。 如果您從 ADE 0.1 (具有 AAD) 切換至 1.1，則必須在啟用 1.1 後停用 VM 的複寫，然後再啟用複寫。


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a> 必要的使用者權限
Site Recovery 需要使用者具有在目的地區域中建立金鑰保存庫的許可權，並將金鑰從來源區域金鑰保存庫複製到目的地區域金鑰保存庫。

若要從 Azure 入口網站複寫啟用磁片加密的 Vm，使用者需要 **來源區域和目的地區域** 金鑰保存庫的下列許可權。

- 金鑰保存庫權限
    - 列出、建立和取得
    
- 金鑰保存庫祕密權限
    - 秘密管理作業
        - 取得、列出及設定
    
- 只有當 Vm 使用金鑰加密金鑰來加密磁片加密金鑰時，才需要金鑰保存庫金鑰許可權 () 
    - 金鑰管理作業
        - 取得、列出及建立
    - 密碼編譯作業
        - 解密和加密

若要管理許可權，請移至入口網站中的金鑰保存庫資源。 為使用者新增必要的許可權。 下列範例會示範如何啟用 *ContosoWeb2Keyvault*來源區域中的金鑰保存庫的許可權。

1. 移至**Home**  >  **庫]**  >  **ContosoWeb2KeyVault > 存取原則**。

   ![Key vault 許可權視窗](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. 您可以看到沒有任何使用者權限。 選取 [新增]****。 輸入使用者和許可權資訊。

   ![Keyvault 許可權](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

如果啟用嚴重損壞修復的使用者 (DR) 沒有複製金鑰的許可權，具有適當許可權的安全性系統管理員可以使用下列腳本，將加密密碼和金鑰複製到目的地區域。

若要針對許可權進行疑難排解，請參閱本文稍後的 [金鑰保存庫許可權問題](#trusted-root-certificates-error-code-151066) 。

>[!NOTE]
>若要從入口網站啟用已啟用磁片加密的 Vm 複寫，您至少需要金鑰保存庫、秘密和金鑰的「清單」許可權。

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>使用 PowerShell 腳本將磁片加密金鑰複製到 DR 區域

1. [開啟 "CopyKeys" 原始腳本](https://aka.ms/ade-asr-copy-keys-code)。
2. 將腳本複製到檔案，並將它命名為 **Copy-keys.ps1**。
3. 開啟 Windows PowerShell 的應用程式，然後移至您儲存檔案的資料夾。
4. 執行 Copy-keys.ps1。
5. 提供 Azure 認證來登入。
6. 選取您 VM 的 **Azure 訂用帳戶**。
7. 等候資源群組載入，然後選取 Vm 的 **資源群組** 。
8. 從顯示的清單中選取 Vm。 只有針對磁片加密啟用的 Vm 會在清單中。
9. 選取 **目標位置**。

    - **磁片加密金鑰保存庫**
    - **金鑰加密金鑰保存庫**

   根據預設，Site Recovery 會在目標區域中建立新的金鑰保存庫。 保存庫的名稱會有 "asr" 尾碼（以來源 VM 磁片加密金鑰為基礎）。 如果 Site Recovery 所建立的金鑰保存庫已經存在，則會重複使用。 如有必要，請從清單中選取不同的金鑰保存庫。

## <a name="enable-replication"></a>啟用複寫

在此範例中，主要 Azure 區域是東亞，而次要區域是南部東亞。

1. 在保存庫中，選取 [ **+** 複寫]。
2. 請注意下欄欄位。
    - **來源**：VM 的起點，在此案例中為 **Azure**。
    - **來源位置**：您想要保護虛擬機器的 Azure 區域。 在此範例中，來源位置為「東亞」。
    - **部署模型**：來源機器的 Azure 部署模型。
    - **來源訂用帳戶**：來源虛擬機器所屬的訂用帳戶。 它可以是與您的復原服務保存庫相同的 Azure Active Directory 租使用者中的任何訂用帳戶。
    - **資源群組**：來源虛擬機器所屬的資源群組。 在下一個步驟中，會列出所選資源群組中的所有 Vm 以進行保護。

3. 在 [**虛擬機器**] 中，選取 [  >  **虛擬機器**]，選取您要複寫的每部 VM。 您只能選取可以啟用複寫的機器。 然後選取 [確定]  。

4. 在 [ **設定**] 中，您可以設定下列目標網站設定。

    - **目標位置**：將複寫來源虛擬機器資料的位置。 Site Recovery 會根據所選電腦的位置，提供適合的目的地區域清單。 建議您使用與復原服務保存庫位置相同的位置。
    - **目標訂**用帳戶：用於損毀修復的目標訂用帳戶。 根據預設，目標訂用帳戶會與來源訂用帳戶相同。
    - **目標資源群組**：所有已複寫虛擬機器所屬的資源群組。 根據預設，Site Recovery 會在目的地區域中建立新的資源群組。 名稱會取得 "asr" 尾碼。 如果已有 Azure Site Recovery 所建立的資源群組，則會重複使用。 您也可以選擇進行自訂，如下一節所示。 目標資源群組的位置可以是任何 Azure 區域，除了裝載來源虛擬機器的區域以外。
    - **目標虛擬網路**：根據預設，Site Recovery 會在目的地區域中建立新的虛擬網路。 名稱會取得 "asr" 尾碼。 它會對應至您的來源網路，並用於任何未來的保護。 [深入了解](./azure-to-azure-network-mapping.md)網路對應。
    - **目標儲存體帳戶 (如果您的來源 vm 未使用受控磁片) **：根據預設，Site Recovery 會模擬您的來源 vm 儲存體設定，以建立新的目標儲存體帳戶。 如果儲存體帳戶已經存在，就會重複使用。
    - **複本受控磁片 (如果您的來源 vm 使用受控磁片) **： Site Recovery 會在目的地區域中建立新的複本受控磁片，以便將相同儲存體類型 (標準或 premium) 的來源 vm 受控磁片鏡像為來源 vm 的受控磁片。
    - 快取**儲存體帳戶**： Site Recovery 需要在來源區域中有額外的儲存體帳戶（稱為快取*儲存體*）。 來源 Vm 上的所有變更都會受到追蹤，並傳送到快取儲存體帳戶。 然後，它們會複寫到目標位置。
    - **可用性設定組**：根據預設，Site Recovery 會在目的地區域中建立新的可用性設定組。 名稱具有 "asr" 尾碼。 如果 Site Recovery 所建立的可用性設定組已經存在，則會重複使用。
    - **磁片加密金鑰保存庫**：根據預設，Site Recovery 會在目的地區域中建立新的金鑰保存庫。 它有一個以來源 VM 磁片加密金鑰為基礎的 "asr" 尾碼。 如果 Azure Site Recovery 所建立的金鑰保存庫已經存在，則會重複使用。
    - **金鑰加密金鑰保存庫**：根據預設，Site Recovery 會在目標區域中建立新的金鑰保存庫。 名稱具有 "asr" 尾碼（以來源 VM 金鑰加密金鑰為基礎）。 如果 Azure Site Recovery 所建立的金鑰保存庫已經存在，則會重複使用。
    - **複寫原則**：定義復原點保留歷程記錄和應用程式一致快照集頻率的設定。 根據預設，Site Recovery 會建立新的複寫原則，其預設設定為 *24 小時* 的復原點保留期，而 *60 分鐘* 表示應用程式一致快照集頻率。

## <a name="customize-target-resources"></a>自訂目標資源

請遵循下列步驟來修改 Site Recovery 預設目標設定。

1. 選取 [目標訂用帳戶] 旁的 [ **自訂** ]，以修改預設的目標訂用帳戶。 從 Azure AD 租使用者中可用的訂用帳戶清單中選取訂用帳戶。

2. 選取 [資源群組、網路、儲存體和可用性設定組] 旁的 [ **自訂** ]，以修改下列預設設定：
    - 針對 [ **目標資源群組**]，從訂用帳戶目標位置中的資源群組清單選取資源群組。
    - 針對 [ **目標虛擬網路**]，請從目標位置的虛擬網路清單中選取網路。
    - 針對 **可用性設定組**，如果 VM 是來源區域中可用性設定組的一部分，您可以將可用性設定組設定新增至 VM。
    - 針對 [ **目標儲存體帳戶**]，選取要使用的帳戶。

2. 選取 [加密設定] 旁的 [ **自訂** ]，以修改下列預設設定：
   - 針對 **目標磁片加密金鑰保存庫**，請從訂用帳戶目標位置中的金鑰保存庫清單中選取目標磁片加密金鑰保存庫。
   - 針對 **目標金鑰加密金鑰保存庫**，請從訂用帳戶目標位置中的金鑰保存庫清單中選取目標金鑰加密金鑰保存庫。

3. 選取 [**建立目標資源**  >  **啟用**複寫]。
4. 啟用 Vm 進行複寫之後，您可以在 [複寫的 **專案**] 下檢查 vm 的健康狀態。

>[!NOTE]
>在初始複寫期間，狀態可能需要一些時間才能重新整理，而不會有明顯的進度。 按一下 **[**  重新整理] 以取得最新狀態。

## <a name="update-target-vm-encryption-settings"></a>更新目標 VM 加密設定
在下列案例中，您將需要更新目標 VM 加密設定：
  - 您已在 VM 上啟用 Site Recovery 複寫。 稍後，您已在來源 VM 上啟用磁片加密。
  - 您已在 VM 上啟用 Site Recovery 複寫。 稍後，您已變更來源 VM 上的磁片加密金鑰或金鑰加密金鑰。

您可以使用[腳本](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script)將加密金鑰複製到目的地區域，然後在 [復原**服務保存庫**複寫的  >  *專案*]  >  **屬性**[  >  **計算和網路**] 中更新目標加密設定。

![更新 ADE 設定對話方塊視窗](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>針對 Azure 至 Azure VM 複寫期間的金鑰保存庫許可權問題進行疑難排解

Azure Site Recovery 至少需要來源區域金鑰保存庫的讀取權限，以及目的地區域金鑰保存庫的寫入權限，才能讀取秘密並將其複製到目的地區域金鑰保存庫。 

**原因1：** 您沒有 **來源區域金鑰保存庫** 的「取得」許可權，無法讀取金鑰。 </br>
**如何修正：** 無論您是訂用帳戶管理員，都一定要具備金鑰保存庫的 get 許可權。

1. 移至來源區域金鑰保存庫，此範例中的「[Contososourcekeyvault」 > **存取原則** 
2. 在 [ **選取主體** ] 下，新增您的使用者名稱，例如： " dradmin@contoso.com "
3. 在 [ **金鑰許可權** ] 下，選取 GET 
4. 在 [ **秘密許可權** ] 下，選取 [取得] 
5. 儲存存取原則

**原因2：** 您沒有 **目的地區域金鑰保存庫** 的必要許可權，無法寫入金鑰。 </br>

*例如*：您嘗試複寫在來源區域上有 Key vault *[contososourcekeyvault* 的 VM。
您擁有來源區域金鑰保存庫的擁有權限。 但在保護期間，您會選取已建立但沒有許可權的金鑰保存庫 ContosotargetKeyvault。 發生錯誤。

[目標金鑰保存庫](#required-user-permissions)所需的許可權

**如何修正：** 移至**Home**  >  **庫]**  >  **ContosotargetKeyvault**  >  **存取原則**，並新增適當的許可權。

## <a name="next-steps"></a>接下來的步驟

[深入了解](site-recovery-test-failover-to-azure.md)執行測試容錯移轉。
