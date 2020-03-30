---
title: Windows 虛擬桌面 FSLogix 設定檔容器檔案 - Azure
description: 本文介紹 Windows 虛擬桌面和 Azure 檔中的 FSLogix 設定檔容器。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1dc5d54fa24217c91e14a8f37e092888b2bb6474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127873"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix 設定檔容器和 Azure 檔案

Windows 虛擬桌面服務建議將 FSLogix 設定檔容器作為使用者設定檔解決方案。 FSLogix 設計用於在遠端計算環境中（如 Windows 虛擬桌面）中漫遊設定檔。 它將完整的使用者設定檔存儲在單個容器中。 登錄時，此容器使用本機支援的虛擬硬碟 （VHD） 和超 V 虛擬硬碟 （VHDX） 動態連接到計算環境。 使用者設定檔立即可用，並且與本機使用者設定檔完全一樣顯示在系統中。 本文介紹與 Azure 檔一起使用的 FSLogix 設定檔容器在 Windows 虛擬桌面中如何運行。

>[!NOTE]
>如果要查找有關 Azure 上不同 FSLogix 設定檔容器存儲選項的比較材料，請參閱[FSLogix 設定檔容器的存儲選項](store-fslogix-profile.md)。

## <a name="user-profiles"></a>使用者設定檔

使用者設定檔包含有關個人的資料元素，包括配置資訊，如桌面設置、持久網路連接和應用程式設定。 預設情況下，Windows 會創建與作業系統緊密集成的本機使用者設定檔。

遠端使用者設定檔在使用者資料和作業系統之間提供分區。 它允許更換或更改作業系統，而不會影響使用者資料。 在遠端桌面工作階段主機 （RDSH） 和虛擬桌面基礎結構 （VDI） 中，作業系統可能會由於以下原因被替換：

- 作業系統的升級
- 替換現有虛擬機器 （VM）
- 使用者是池式（非持久性）RDSH 或 VDI 環境的一部分

Microsoft 產品使用多種技術用於遠端使用者設定檔，包括以下技術：
- 漫遊使用者設定檔 （RUP）
- 使用者設定檔磁片 （UPD）
- 企業狀態漫遊 （ESR）

UPD 和 RUP 是遠端桌面工作階段主機 （RDSH） 和虛擬硬碟 （VHD） 環境中使用者設定檔使用最廣泛的技術。

### <a name="challenges-with-previous-user-profile-technologies"></a>以前使用者設定檔技術的挑戰

針對使用者設定檔的現有和遺留的 Microsoft 解決方案帶來了各種挑戰。 沒有以前的解決方案處理 RDSH 或 VDI 環境附帶的所有使用者設定檔需求。 例如，UPD 無法處理大型 OST 檔，RUP 不會保留現代設置。

#### <a name="functionality"></a>功能

下表顯示了以前使用者設定檔技術的優點和限制。

| 技術 | 現代設置 | Win32 設置 | 作業系統設置 | 使用者資料 | 支援伺服器 SKU | Azure 上的後端存儲 | 本地後端存儲 | 版本支援 | 後續登錄時間 |注意|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **使用者設定檔磁片 （UPD）** | 是 | 是 | 是 | 是 | 是 | 否 | 是 | 贏 7+ | 是 | |
| **漫遊使用者設定檔 （RUP），維護模式** | 否 | 是 | 是 | 是 | 是| 否 | 是 | 贏 7+ | 否 | |
| **企業狀態漫遊 （ESR）** | 是 | 否 | 是 | 否 | 請參閱附註 | 是 | 否 | Win 10 | 否 | 伺服器 SKU 上的功能，但沒有支援使用者介面 |
| **使用者體驗虛擬化 (UE-V)** | 是 | 是 | 是 | 否 | 是 | 否 | 是 | 贏 7+ | 否 |  |
| **OneDrive 雲檔** | 否 | 否 | 否 | 是 | 請參閱附註 | 請參閱附註  | 請參閱注釋 | 贏得 10 RS3 | 否 | 未在伺服器 SKU 上測試。 Azure 上的後端存儲取決於同步用戶端。 後端存儲上預存儲需要同步用戶端。 |

#### <a name="performance"></a>效能

UPD 要求[存儲空間直接 （S2D）](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/)來滿足性能要求。 UPD 使用伺服器訊息區 （SMB） 協定。 它將配置檔案複製到正在記錄使用者的 VM。 使用 S2D 的 UPD 是我們建議用於 Windows 虛擬桌面的解決方案。  

#### <a name="cost"></a>成本

雖然 S2D 群集實現了必要的性能，但對於企業客戶來說，成本是昂貴的，但對於中小型企業 （SMB） 客戶來說尤其昂貴。 對於此解決方案，企業會為存儲磁片付費，以及使用磁片進行共用的 VM 的成本。

#### <a name="administrative-overhead"></a>系統管理負荷

S2D 群集需要以安全狀態進行修補、更新和維護的作業系統。 這些流程和設置 S2D 災害復原的複雜性使得 S2D 僅適用于具有專職 IT 人員的企業。

## <a name="fslogix-profile-containers"></a>FSLogix 設定檔容器

2018年11月19日，[微軟收購了FSLogix。](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/) FSLogix 解決了許多設定檔容器挑戰。 其中的關鍵是：

- **性能：**[FSLogix 設定檔容器](/fslogix/configure-profile-container-tutorial/)具有高性能，可解決歷史上阻止緩存交換模式的性能問題。
- **OneDrive：** 如果沒有 FSLogix 設定檔容器，在非持久性 RDSH 或 VDI 環境中不支援 OneDrive 業務。 [OneDrive 業務和 FSLogix 最佳實踐](/fslogix/overview/)描述了它們如何交互。 有關詳細資訊，請參閱[在虛擬桌面上使用同步用戶端](/deployoffice/rds-onedrive-business-vdi/)。
- **其他資料夾：** FSLogix 提供擴展使用者設定檔以包括其他資料夾的功能。

自收購以來，微軟開始用FSLogix設定檔容器替換現有的使用者設定檔解決方案（如 UPD）。

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure 檔與 Azure 活動目錄域服務集成

FSLogix 設定檔容器的性能和功能利用了雲。 2019 年 8 月 7 日，Microsoft Azure 檔宣佈[Azure 檔身份驗證的通用版，Azure 活動目錄域服務 （AD DS）](../storage/files/storage-files-active-directory-overview.md). 通過解決成本和管理開銷，Azure 檔與 Azure AD DS 身份驗證是 Windows 虛擬桌面服務中使用者設定檔的高級解決方案。

## <a name="best-practices-for-windows-virtual-desktop"></a>Windows 虛擬桌面的最佳做法

Windows 虛擬桌面可完全控制客戶正在使用的 VM 的大小、類型和計數。 有關詳細資訊，請參閱什麼是[Windows 虛擬桌面？](overview.md)

為確保 Windows 虛擬桌面環境遵循最佳實踐，請：

- Azure 檔存儲帳戶必須與會話主機 VM 位於同一區域。
- Azure 檔許可權應與["要求 - 設定檔容器](/fslogix/fslogix-storage-config-ht)"中描述的許可權匹配。
- 每個主機池必須基於同一主映射構建相同類型和大小的 VM。
- 每個主機池 VM 必須位於同一資源組中，以説明管理、擴展和更新。
- 為獲得最佳性能，存儲解決方案和 FSLogix 設定檔容器應位於同一資料中心位置。
- 包含主映射的存儲帳戶必須位於預配 VM 的同一區域和訂閱中。

## <a name="next-steps"></a>後續步驟

使用以下指南設置 Windows 虛擬桌面環境。

- 要開始構建桌面虛擬化解決方案，請參閱[在 Windows 虛擬桌面中創建租戶](tenant-setup-azure-active-directory.md)。
- 要在 Windows 虛擬桌面租戶中創建主機池，請參閱[使用 Azure 應用商店創建主機池](create-host-pools-azure-marketplace.md)。
- 要在雲中設置完全託管的檔共用，請參閱[設置 Azure 檔共用](/azure/storage/files/storage-files-active-directory-enable/)。
- 要配置 FSLogix 設定檔容器，請參閱[使用檔共用為主機池創建設定檔容器](create-host-pools-user-profile.md)。
- 要將使用者分配給主機池，請參閱[管理 Windows 虛擬桌面的應用組](manage-app-groups.md)。
- 要從 Web 瀏覽器訪問 Windows 虛擬桌面資源，請參閱[連接到 Windows 虛擬桌面](connect-web.md)。
