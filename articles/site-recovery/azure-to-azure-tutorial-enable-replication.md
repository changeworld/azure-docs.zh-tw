---
title: 使用 Azure Site Recovery 設定 Azure VM 災害復原
description: 了解如何使用 Azure Site Recovery 服務，設定 Azure VM 到不同 Azure 區域的災害復原。
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c5d2bbe920f87421550fadf30a7e7e9d23931bfd
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80292475"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>設定適用於 Azure VM 的災害復原

[Azure Site Recovery](site-recovery-overview.md) 服務可藉由管理及協調內部部署電腦與 Azure 虛擬機器 (VM) 的複寫、容錯移轉及容錯回復，為您的災害復原策略做出貢獻。

本教學課程說明如何為 Azure VM 設定災害復原，讓 VM 可以從一個 Azure 區域移至另一個。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立復原服務保存庫
> * 驗證目標資源設定
> * 設定 VM 的輸出網路連線
> * 啟用 VM 複寫

> [!NOTE]
> 本文將說明如何以最簡單的設定部署災害復原。 如果您想深入了解自訂設定，請檢閱[操作說明區段](azure-to-azure-how-to-enable-replication.md)中的文章。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程：

- 檢閱[案例架構和元件](concepts-azure-to-azure-architecture.md)。
- 開始之前，請檢閱[支援需求](site-recovery-support-matrix-azure-to-azure.md)。

## <a name="create-a-recovery-services-vault"></a>建立復原服務保存庫

在來源區域以外的任何區域中建立保存庫。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]  。 然後，選取 [IT 和管理工具]   > [備份和 Site Recovery]  。
1. 在 [名稱]  中，指定保存庫的易記識別名稱。 如果您有多個訂用帳戶，請選取適當的一個。
1. 建立資源群組，或選取現有的資源群組。 指定 Azure 區域。 若要查看支援的區域，請參閱 [Azure Site Recovery 定價詳細資料](https://azure.microsoft.com/pricing/details/site-recovery/)。
1. 若要從儀表板存取保存庫，請選取 [釘選到儀表板]  ，然後選取 [建立]  。

   ![新增保存庫](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

新的保存庫會新增到主要 [復原服務保存庫]  頁面上 [所有資源]  之下的 [儀表板]  。

## <a name="verify-target-resource-settings"></a>驗證目標資源設定

檢查您 Azure 訂用帳戶中的目標區域。

- 確認您的 Azure 訂用帳戶允許您在目標區域中建立 VM。 請連絡支援人員啟用所需的配額。
- 確定您的訂用帳戶具有足夠的資源，可支援大小與來源 VM 相符的 VM。 Site Recovery 會為目標 VM 挑選相同的大小或最接近的大小。

## <a name="set-up-outbound-network-connectivity-for-vms"></a>設定 VM 的輸出網路連線

若要 Site Recovery 如預期運作，必須從您需要複寫的 VM 中修改輸出網路連線能力。

> [!NOTE]
> Site Recovery 不支援使用驗證 Proxy 來控制網路連線能力。

### <a name="outbound-connectivity-for-urls"></a>URL 的輸出連線能力

如果您使用以 URL 為基礎的防火牆 Proxy 來控制輸出連線能力，請允許存取這些 URL：

| **URL** | **詳細資料** |
| ------- | ----------- |
| `*.blob.core.windows.net` | 允許將資料從 VM 寫入來源區域的快取儲存體帳戶中。 |
| `login.microsoftonline.com` | 提供 Site Recovery 服務 URL 的授權和驗證。 |
| `*.hypervrecoverymanager.windowsazure.com` | 允許 VM 與 Site Recovery 服務進行通訊。 |
| `*.servicebus.windows.net` | 允許 VM 寫入 Site Recovery 監視和診斷資料。 |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 位址範圍的輸出連線能力

如果您使用網路安全性群組 (NSG)，請針對 Azure 儲存體、Azure Active Directory、Site Recovery 服務和 Site Recovery 監視的存取權建立以服務標籤為基礎的 NSG 規則。 [深入了解](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags)。

## <a name="verify-azure-vm-certificates"></a>驗證 Azure VM 憑證

請檢查您想要複寫的 VM 是否具有最新的根憑證。 如果沒有的話，VM 會因安全性限制而無法註冊至 Site Recovery。

- 若為 Windows VM，請安裝所有最新的 Windows 更新，讓所有的受信任根憑證都在機器上。 在中斷連線的環境中，請遵循您組織的標準 Windows Update 和憑證更新程序。
- 若為 Linux VM，請遵循 Linux 散發者提供的指引，以取得最新的受信任根憑證及 VM 的憑證撤銷清單。

## <a name="set-permissions-on-the-account"></a>設定帳戶的權限

Azure Site Recovery 提供 3 種內建角色，以控制 Site Recovery 管理作業。

- **Site Recovery 參與者**：此角色具有在復原服務保存庫中管理 Azure Site Recovery 作業所需的所有權限。 不過，具有此角色的使用者無法建立或刪除復原服務保存庫，也無法為其他使用者指派存取權限。 此角色最適合災害復原系統管理員，他們可以為應用程式或整個組織啟用和管理災害復原。

- **Site Recovery 操作員** - 此角色具有執行和管理容錯移轉和容錯回復作業的權限。 具有此角色的使用者無法啟用或停用複寫、建立或刪除保存庫、註冊新的基礎結構，也無法為其他使用者指派存取權限。 此角色最適合災害復原操作員，在應用程式擁有者和 IT 系統管理員的指示下，操作員可以對虛擬機器或應用程式進行容錯移轉。 災害解決後，災害復原操作員可以重新保護和容錯回復虛擬機器。

- **Site Recovery 讀者**：此角色擁有可檢視所有 Site Recovery 管理作業的權限。 此角色最適合 IT 監督主管，以便監控目前的保護狀態並提出支援票證。

深入了解 [Azure RBAC 內建角色](../role-based-access-control/built-in-roles.md)。

## <a name="enable-replication-for-a-vm"></a>啟用 VM 複寫

以下各節將說明如何啟用複寫。

### <a name="select-the-source"></a>選取來源

若要開始進行複寫設定，請選擇您的 Azure VM 執行所在的來源。

1. 移至 [復原服務保存庫]  ，選取保存庫名稱，然後選取 [+複寫]  。
1. 針對 [來源]  ，選取 [Azure]  。
1. 在 [來源位置]  中，選取 VM 目前執行所在的來源 Azure 區域。
1. 選取虛擬機器執行所在的**來源訂用帳戶**。 這可以是您的復原服務保存庫所在的相同 Azure Active Directory 租用戶內的任何訂用帳戶。
1. 選取 [來源資源群組]  ，然後選取 [確定]  以儲存設定。

   ![設定來源](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>選取 VM

Site Recovery 會擷取與訂用帳戶和資源群組/雲端服務相關聯的 VM 清單。

1. 在 [虛擬機器]  中，選取您要複寫的 VM。
1. 選取 [確定]  。

### <a name="configure-replication-settings"></a>設定複寫設定

Site Recovery 會設定目標區域的預設設定和複寫原則。 您可以視需要變更這些設定。

1. 選取 [設定]  以檢視目標和複寫設定。

1. 若要覆寫預設目標設定，請選取 [資源群組、網路、儲存體和可用性]  旁的 [自訂]  。

   ![進行設定](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. 自訂目標設定，如下表所摘要的內容。

   | **設定** | **詳細資料** |
   | --- | --- |
   | **目標訂用帳戶** | 根據預設，目標訂用帳戶會與來源訂用帳戶相同。 選取 [自訂]  ，以選取相同 Azure Active Directory 租用戶內的不同目標訂用帳戶。 |
   | **目標位置** | 用於災害復原的目標區域。<br/><br/> 我們建議目標位置符合 Site Recovery 保存庫的位置。 |
   | **目標資源群組** | 目標區域中在容錯移轉後保留 Azure VM 的資源群組。<br/><br/> 根據預設，Site Recovery 會在目標區域中建立具有 `asr` 尾碼的新資源群組。 目標資源群組的位置可以是任何區域，但是裝載您來源虛擬機器所在的區域除外。 |
   | **目標虛擬網路** | 目標區域中 VM 在容錯移轉後所在的網路。<br/><br/> 根據預設，Site Recovery 會在目標區域中建立具有 `asr` 尾碼的新虛擬網路 (和子網路)。 |
   | **快取儲存體帳戶** | Site Recovery 會使用來源區域中的儲存體帳戶。 來源 VM 的變更會在複寫到目標位置之前，先傳送到此帳戶。<br/><br/> 如果您使用啟用防火牆的快取儲存體帳戶，請確定您已啟用 [允許信任的 Microsoft 服務]  。 [深入了解](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)。 此外，請確保您允許存取來源 Vnet 的至少一個子網路。 |
   | **目標儲存體帳戶 (來源 VM 使用非受控磁碟)** | 根據預設，Site Recovery 會在目標區域中建立新的儲存體帳戶，以反映來源 VM 儲存體帳戶。<br/><br/> 如果您使用啟用防火牆的快取儲存體帳戶，請啟用**允許信任的 Microsoft 服務**。 |
   | **複本受控磁碟 (如果來源 VM 使用受控磁碟)** | 根據預設，Site Recovery 會在目標區域中建立與來源 VM 受控磁碟具有相同儲存體類型 (標準或進階) 的複本受控磁碟，以反映來源 VM 的受控磁碟。 您只能自訂磁碟類型。 |
   | **目標可用性設定組** | 根據預設，Azure Site Recovery 會針對來源區域的可用性設定組中的 VM，在目標區域中建立名稱尾碼為 `asr` 的新可用性設定組。 如果 Azure Site Recovery 建立的可用性設定組已存在，則會重複使用。 |
   | **目標可用性區域** | 根據預設，如果目標區域支援可用性區域，則 Site Recovery 會在目標區域中指派與來源區域相同的區域編號。<br/><br/> 如果目標區域不支援可用性區域，則目標 VM 會預設為單一執行個體。<br/><br/> 選取 [自訂]  ，將 VM 設定為目標區域中可用性設定組的一部分。<br/><br/> 啟用複寫之後，便無法變更可用性類型 (單一執行個體、可用性設定組或可用性區域)。 若要變更可用性類型，請先停用複寫再重新加以啟用。 |

1. 若要自訂複寫原則設定，請選取 [複寫原則]  旁的 [自訂]  ，並根據需求修改設定。

   | **設定** | **詳細資料** |
   | --- | --- |
   | **複寫原則名稱** | 原則名稱。 |
   | **復原點保留期** | Site Recovery 預設會保留復原點 24 小時。 您可以設定介於 1 與 72 小時之間的值。 |
   | **應用程式一致的快照頻率** | Site Recovery 預設會每隔 4 小時建立一份應用程式一致快照集。 您可以設定介於 1 與 12 小時之間的任何值。<br/><br/> 應用程式一致快照集是 VM 內的應用程式資料的時間點快照集。 磁碟區陰影複製服務 (VSS) 可確保在建立快照集時，VM 上的應用程式處於一致狀態。 |
   | **複寫群組** | 如果您的應用程式需要 VM 之間的多部 VM 具有一致性，您可以為這些 VM 建立複寫群組。 根據預設，選取的 VM 不屬於任何複寫群組。 |

1. 如果您想要將 VM 新增至新的或現有的複寫群組，請在 [自訂]  中的多部 VM 一致性上選取 [是]  。 然後選取 [確定]  。

   > [!NOTE]
   > - 在容錯移轉時，複寫群組中的所有機器都會共用當機時保持一致和應用程式一致復原點。
   > - 啟用多 VM 一致性可能會影響工作負載的效能 (因為需耗用大量 CPU)。 應該只有在機器執行相同工作負載，且多部機器之間需要一致性時，才使用此功能。
   > - 在複寫群組中，您最多可以有 16 個 VM。
   > - 如果您啟用多部 VM 一致性，則複寫群組中的機器會透過連接埠 20004 彼此通訊。 請確定防火牆並未封鎖 VM 之間透過此連接埠進行的內部通訊。
   > - 針對複寫群組中的 Linux VM，請確定已根據 Linux 版本的指引手動開啟連接埠 20004 上的輸出流量。

### <a name="configure-encryption-settings"></a>加密設定

如果來源 VM 已啟用 Azure 磁碟加密 (ADE)，請檢閱設定。

1. 確認設定：
   1. **磁碟加密金鑰保存庫**：根據預設，Site Recovery 會在來源 VM 磁碟加密金鑰上建立新的金鑰保存庫，且加上 `asr` 尾碼。 如果金鑰保存庫已存在，則會重複使用。
   1. **金鑰加密金鑰保存庫**：根據預設，Site Recovery 會在目標區域中建立新的金鑰保存庫。 名稱會有 `asr` 尾碼，並且會以來源 VM 金鑰加密金鑰為基礎。 如果 Site Recovery 建立的金鑰保存庫已經存在，則會重複使用。
1. 選取 [自訂]  以選取自訂金鑰保存庫。

> [!NOTE]
> Azure Site Recovery 目前僅支援執行 Windows 作業系統及[可以使用 Azure AD 應用程式進行加密](https://aka.ms/ade-aad-app)的 Azure VM。

### <a name="track-replication-status"></a>追蹤複寫狀態

啟用複寫後，您可以追蹤作業的狀態。

1. 在 [設定]  中，選取 [重新整理]  以取得最新狀態。
1. 追蹤進度和狀態如下所示：
   1. 在 [設定]   > [作業]   > [Site Recovery 作業]  中，追蹤 [啟用保護]  作業的進度。
   1. 在 [設定]   > [複寫的項目]  中，您可以檢視 VM 的狀態和初始複寫進度。 選取 VM 可向下切入其設定。

## <a name="next-steps"></a>後續步驟

您已在本教學課程中設定 Azure VM 的災害復原。 現在，您可以執行災害復原演練，以確認容錯移轉是否如預期運作。

> [!div class="nextstepaction"]
> [執行災害復原演練](azure-to-azure-tutorial-dr-drill.md)
