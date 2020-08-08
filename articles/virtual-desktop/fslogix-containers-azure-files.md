---
title: Windows 虛擬桌面 FSLogix 設定檔容器檔案-Azure
description: 本文說明 Windows 虛擬桌面和 Azure 檔案中的 FSLogix 設定檔容器。
author: Heidilohr
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 669f4baa723b78b8933f3a75fc361c468f9e2df9
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002397"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix 設定檔容器和 Azure 檔案

Windows 虛擬桌面服務會建議 FSLogix 設定檔容器做為使用者設定檔解決方案。 FSLogix 可用來漫遊遠端運算環境中的設定檔，例如 Windows 虛擬桌面。 其會將完整的使用者設定檔儲存在單一容器中。 在登入時，此容器會使用原生支援的虛擬硬碟（ (VHD) 和 Hyper-v 虛擬硬碟 (VHDX) ，以動態方式連接到計算環境。 使用者設定檔會立即可供使用，而且會與原生使用者設定檔完全出現在系統中。 本文說明如何在 Windows 虛擬桌面中搭配 Azure 檔案儲存體函式使用的 FSLogix 設定檔容器。

>[!NOTE]
>如果您要尋找 Azure 上不同 FSLogix 設定檔容器儲存體選項的比較資料，請參閱[FSLogix 設定檔容器的儲存體選項](store-fslogix-profile.md)。

## <a name="user-profiles"></a>使用者設定檔

使用者設定檔包含關於個人的資料元素，包括桌面設定、持續性網路連線和應用程式設定等設定資訊。 根據預設，Windows 會建立與作業系統緊密整合的本機使用者設定檔。

遠端使用者設定檔提供使用者資料與作業系統之間的磁碟分割。 它允許取代或變更作業系統，而不會影響使用者資料。 在遠端桌面工作階段主機 (RDSH) 和虛擬桌面基礎結構 (VDI) 中，作業系統可能會因下列原因而被取代：

- 作業系統的升級
- 取代現有的虛擬機器 (VM) 
- 屬於集區 (非持續) RDSH 或 VDI 環境的使用者

Microsoft 產品會針對遠端使用者策略檔運算元種技術，包括下列技術：
- 漫遊使用者設定檔 (的 RUP) 
-  (UPD 的使用者設定檔磁片) 
- 企業狀態漫遊 (ESR) 

UPD 和 RUP 是遠端桌面工作階段主機 (RDSH) 中的使用者設定檔最普遍使用的技術，以及 (VHD) 環境中的虛擬硬碟。

### <a name="challenges-with-previous-user-profile-technologies"></a>先前的使用者設定檔技術挑戰

適用于使用者設定檔的現有和舊版 Microsoft 解決方案會遇到各種挑戰。 先前的解決方案不會處理所有隨附于 RDSH 或 VDI 環境的使用者設定檔需求。 例如，UPD 無法處理大型的 OST 檔案，而 RUP 也不會保存現代化設定。

#### <a name="functionality"></a>功能

下表顯示先前的使用者設定檔技術的優點與限制。

| 技術 | 新式設定 | Win32 設定 | OS 設定 | 使用者資料 | 伺服器 SKU 上支援 | Azure 上的後端儲存體 | 內部部署的後端儲存體 | 版本支援 | 後續登入時間 |附註|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| ** (UPD 的使用者設定檔磁片) ** | 是 | 是 | 是 | 是 | 是 | 否 | 是 | Win 7 + | 是 | |
| **漫遊使用者設定檔 (的 RUP) ，維護模式** | 否 | 是 | 是 | 是 | 是| 否 | 是 | Win 7 + | 否 | |
| **企業狀態漫遊 (ESR) ** | 是 | 否 | 是 | 否 | 請參閱附註 | 是 | 否 | Win 10 | 否 | 伺服器 SKU 上的功能，但不支援使用者介面 |
| **使用者體驗虛擬化 (UE-V)** | 是 | 是 | 是 | 否 | 是 | 否 | 是 | Win 7 + | 否 |  |
| **OneDrive 雲端檔案** | 否 | 否 | 否 | 是 | 請參閱附註 | 請參閱附註  | 請參閱附注 | Win 10 RS3 | 否 | 未在伺服器 SKU 上測試。 Azure 上的後端儲存體取決於同步處理用戶端。 內部部署上的後端儲存體需要同步用戶端。 |

#### <a name="performance"></a>效能

UPD 需要[儲存空間直接存取 (S2D) ](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/)來處理效能需求。 UPD 會使用伺服器訊息區 (SMB) 通訊協定。 它會將配置檔案複製到要在其中記錄使用者的 VM。 UPD with S2D 是我們建議 Windows 虛擬桌面的解決方案。

#### <a name="cost"></a>成本

雖然 S2D 叢集達到所需的效能，但成本對於企業客戶而言是昂貴的，但對於中小型企業 (SMB) 客戶而言，特別昂貴。 針對此解決方案，企業會支付儲存體磁片的費用，以及使用共用磁片的 Vm 成本。

#### <a name="administrative-overhead"></a>系統管理負荷

S2D 叢集需要以安全狀態修補、更新及維護的作業系統。 這些程式和設定 S2D 嚴重損壞修復的複雜性使得 S2D 僅適用于具有專屬 IT 人員的企業。

## <a name="fslogix-profile-containers"></a>FSLogix 設定檔容器

Microsoft 已于2018年11月19日[取得 FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/)。 FSLogix 解決了許多設定檔容器的挑戰。 其中的索引鍵為：

- **效能：**[FSLogix 設定檔容器](/fslogix/configure-profile-container-tutorial/)是高效能，可解決過去已封鎖快取 exchange 模式的效能問題。
- **OneDrive：** 若沒有 FSLogix 設定檔容器，非持續性 RDSH 或 VDI 環境中不支援商務用 OneDrive。 [商務用 OneDrive 和 FSLogix 的最佳作法](/fslogix/overview/)說明它們的互動方式。 如需詳細資訊，請參閱在[虛擬桌面電腦上使用同步用戶端](/deployoffice/rds-onedrive-business-vdi/)。
- **其他資料夾：** FSLogix 提供擴充使用者設定檔以包含其他資料夾的功能。

自收購以來，Microsoft 已開始使用 FSLogix 設定檔容器取代現有的使用者設定檔解決方案，例如 UPD。

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure 檔案儲存體與 Azure Active Directory 網域服務整合

FSLogix 設定檔容器的效能和功能會利用雲端。 2019年8月7日，Microsoft Azure 檔案已宣佈[使用 Azure Active Directory 網域服務 (AD DS) 的 Azure 檔案儲存體驗證](../storage/files/storage-files-active-directory-overview.md)正式運作。 藉由解決成本和系統管理負擔，Azure AD DS 驗證的 Azure 檔案儲存體是 Windows 虛擬桌面服務中使用者設定檔的 premium 解決方案。

## <a name="best-practices-for-windows-virtual-desktop"></a>Windows 虛擬桌面的最佳做法

Windows 虛擬桌面提供客戶所使用 Vm 的大小、類型和計數的完整控制權。 如需詳細資訊，請參閱[什麼是 Windows 虛擬桌面？](overview.md)。

若要確保您的 Windows 虛擬桌面環境遵循最佳做法：

- Azure 檔案儲存體儲存體帳戶必須位於與會話主機 Vm 相同的區域中。
- Azure 檔案儲存體許可權應符合[需求-設定檔容器](/fslogix/fslogix-storage-config-ht)中所述的許可權。
- 每個主機集區都必須根據相同的主要映射，建立相同類型和大小的 VM。
- 每部主機集區 VM 都必須位於相同的資源群組中，以協助管理、調整和更新。
- 為了達到最佳效能，儲存體解決方案和 FSLogix 設定檔容器應該位於相同的資料中心位置。
- 包含主要映射的儲存體帳戶必須位於布建 Vm 所在的相同區域與訂用帳戶中。

## <a name="next-steps"></a>後續步驟

使用下列指南來設定 Windows 虛擬桌面環境。

- 若要開始建立您的桌面虛擬化解決方案，請參閱[在 Windows 虛擬桌面中建立租](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)使用者。
- 若要在 Windows 虛擬桌面租使用者內建立主機集區，請參閱[使用 Azure Marketplace 建立主機集](create-host-pools-azure-marketplace.md)區。
- 若要在雲端中設定完全受控的檔案共用，請參閱[設定 Azure 檔案儲存體共用](/azure/storage/files/storage-files-active-directory-enable/)。
- 若要設定 FSLogix 設定檔容器，請參閱[使用檔案共用建立主機集區的設定檔容器](create-host-pools-user-profile.md)。
- 若要將使用者指派給主機集區，請參閱[管理 Windows 虛擬桌面的應用程式群組](manage-app-groups.md)。
- 若要從網頁瀏覽器存取您的 Windows 虛擬桌面資源，請參閱[連接到 Windows 虛擬桌面](connect-web.md)。
