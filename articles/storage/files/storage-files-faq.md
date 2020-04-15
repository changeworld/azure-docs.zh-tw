---
title: Azure 檔案服務的常見問題集 (FAQ) | Microsoft Docs
description: 尋找關於 Azure 檔案服務之常見問題集的解答。
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 82c516eeac6d3e88ca7b6ac1c97ebb638ba27979
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383920"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>關於 Azure 檔案服務的常見問題集 (FAQ)
[Azure 檔案](storage-files-introduction.md)提供雲端中完全受控的檔案共用，可透過業界標準[伺服器訊息區 (SMB) 通訊協定](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)來存取。 您可以同時在 Windows、Linux 和 macOS 的雲端或內部部署上掛接 Azure 檔案共用。 您也可以使用 Azure 檔案同步，在接近使用資料之處進行快速存取，藉以在 Windows Server 電腦上快取 Azure 檔案共用。

本文將回答有關 Azure 檔案服務特性與功能 (包括將 Azure 檔案同步與 Azure 檔案搭配使用) 的常見問題。 如果您找不到問題的答案，可透過下列管道 (依先後順序) 和我們連絡：

1. 本文的留言區。
2. [Azure 儲存論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)。
3. [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) \(英文\)。 
4. Microsoft 支援服務。 若要建立新的支援要求，在 Azure 入口網站的 [說明]**** 索引標籤上，選取 [說明 + 支援]**** 按鈕，然後選取 [新增支援要求]****。

## <a name="general"></a>一般
* <a id="why-files-useful"></a>
  **Azure 檔案服務有多實用？**  
   您可以使用 Azure 檔案服務，在雲端中建立檔案共用，而不需負責管理實體伺服器、裝置或應用裝置的額外負荷。 我們會為您執行單調的工作，包括套用 OS 更新和替換損毀的磁碟。 若要深入了解 Azure 檔案服務可協助處理的案例，請參閱 [Azure 檔案服務為何很實用](storage-files-introduction.md#why-azure-files-is-useful)。

* <a id="file-access-options"></a>
  **有哪些不同方式可以存取 Azure 檔案服務中的檔案？**  
    您可以使用 SMB 3.0 通訊協定，在本機電腦上掛接檔案共用，或者可以使用[儲存體總管](https://storageexplorer.com/)之類的工具來存取檔案共用中的檔案。 您可以從應用程式中，使用儲存體用戶端程式庫、REST API、PowerShell 或 Azure CLI 來存取 Azure 檔案共用中的檔案。

* <a id="what-is-afs"></a>
  **什麼是 Azure 檔案同步？**  
    您可以使用 Azure 檔案同步，將組織的檔案共用集中在 Azure 檔案服務中，同時保有內部部署檔案伺服器的靈活度、效能及相容性。 Azure 檔案同步會將您的 Windows Server 電腦轉換成 Azure 檔案共用的快速快取。 您可以使用 Windows Server 上可用的任何通訊協定以從本機存取資料，包括 SMB、網路檔案系統 (NFS) 和檔案傳輸通訊協定服務 (FTPS)。 您可以視需要存取多個散佈於世界各地的快取。

* <a id="files-versus-blobs"></a>
  **為什麼我要使用 Azure 檔案共用與 Azure Blob 儲存體來儲存我的資料？**  
    Azure 檔案服務和 Azure Blob 儲存體都會提供在雲端中儲存大量資料的方式，但其適用用途稍有不同。 
    
    Azure Blob 儲存體適用於需要儲存非結構化資料的大規模雲端原生應用程式。 為了充分發揮效能並進行擴充，Azure Blob 儲存體是比真實檔案系統更為簡單的儲存體抽象概念。 您只能透過 REST 型用戶端程式庫 (或直接透過 REST 型通訊協定) 存取 Azure Blob 儲存體。

    具體而言，Azure 檔案服務是一個檔案系統。 Azure 檔案服務具備您多年來熟知且喜愛用來與內部部署作業系統搭配使用的所有檔案摘要。 就像 Azure Blob 儲存體，Azure 檔案服務可提供 REST 介面和 REST 型用戶端程式庫。 不同於 Azure Blob 儲存體，Azure 檔案服務提供對 Azure 檔案共用的 SMB 存取。 藉由使用 SMB，您可以在內部部署或雲端 VM 中，直接將 Azure 檔案共用掛接在 Windows、Linux 或 macOS 上，而不需撰寫任何程式碼，或對檔案系統附加任何特殊的驅動程式。 您也可以使用 Azure 檔案同步，在接近使用資料之處進行快速存取，藉以在內部部署檔案伺服器上快取 Azure 檔案共用。 
   
    有關 Azure 檔案和 Azure Blob 儲存之間的差異的更深入說明,請參閱核心[Azure 儲存服務簡介](../common/storage-introduction.md)。 若要深入了解 Azure Blob 儲存體，請參閱 [Blob 儲存體簡介](../blobs/storage-blobs-introduction.md)。

* <a id="files-versus-disks"></a>**為什麼我要使用 Azure 檔案共用，而不是 Azure 磁碟呢？**  
    Azure 磁碟中的磁碟就只是個磁碟。 若要從 Azure 磁碟取得值，您必須將磁碟連接至 Azure 中執行的虛擬機器。 Azure 磁碟適用於您要在內部部署伺服器上為其使用磁碟的所有項目。 您可以使用它作為 OS 系統磁碟、作為 OS 的交換空間，或者作為應用程式的專用儲存體。 Azure 磁碟的一個有趣用途是在雲端建立檔案伺服器，以便在您可能使用 Azure 檔案共用的相同位置中使用。 當您需要的部署選項 (例如，NFS 通訊協定支援或進階儲存體) 目前不受 Azure 檔案服務支援時，在 Azure 虛擬機器中部署檔案伺服器是在 Azure 中獲得檔案儲存體且具有極高效能的方式。 

    不過，基於數種理由，執行以 Azure 磁碟作為後端儲存體的檔案伺服器一般會比使用 Azure 檔案共用來得更昂貴。 首先，除了要支付磁碟儲存體的費用外，您還必須支付執行一或多個 Azure VM 的費用。 其次，您也必須管理用來執行檔案伺服器的 VM。 例如，您必須負責 OS 升級。 最後，如果您最終需要在內部部署中快取資料，就必須自行設定和管理複寫技術 (例如，分散式檔案系統複寫 (DFSR)) 以實現此目的。

    若要從 Azure 檔案服務和 Azure 虛擬機器中所裝載 (除了使用 Azure 磁碟作為後端儲存體) 的檔案伺服器獲取最大效益，其中一個方法是在雲端 VM 上所裝載的檔案伺服器上安裝 Azure 檔案同步。 如果 Azure 檔案共用位於和您檔案伺服器相同的區域，您可以啟用雲端分層，並將磁碟區可用空間百分比設定為最大值 (99%)。 這確保資料重複出現的機率最低。 您也可以使用任何您想要與檔案伺服器搭配使用的應用程式，例如，需要 NFS 通訊協定支援的應用程式。

    如需在 Azure 中設定高效能和高可用性檔案伺服器的選項相關資訊，請參閱[在 Microsoft Azure 中部署 IaaS VM 客體叢集](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/) \(英文\)。 有關 Azure 檔和 Azure 磁碟之間的差異的更深入說明,請參閱核心 Azure[儲存服務簡介](../common/storage-introduction.md)。 若要深入了解 Azure 磁碟，請參閱 [Azure 受控磁碟概觀](../../virtual-machines/windows/managed-disks-overview.md)。

* <a id="get-started"></a>
  **如何開始使用 Azure 檔案服務？**  
   Azure 檔案很容易就能開始使用。 首先，[建立檔案共用](storage-how-to-create-file-share.md)，然後在您慣用的作業系統中掛接它： 

  * [在 Windows 中掛接](storage-how-to-use-files-windows.md)
  * [在 Linux 中掛接](storage-how-to-use-files-linux.md)
  * [在 macOS 中掛接](storage-how-to-use-files-mac.md)

    如需更深入了解部署 Azure 檔案共用來取代組織中生產環境檔案共用的指引，請參閱[規劃 Azure 檔案服務部署](storage-files-planning.md)。

* <a id="redundancy-options"></a>
  **Azure 檔案服務支援哪些儲存體備援選項？**  
    目前,Azure 檔案支援本地冗餘存儲 (LRS)、區域冗餘存儲 (ZRS)、異地冗餘存儲 (GRS) 和地理位置區域冗餘存儲 (GZRS)(預覽)。 我們計劃在未來支援讀取權限異地備援 (RA-GRS) 儲存體，但目前尚無確切時間表。

* <a id="tier-options"></a>
  **Azure 檔案服務中支援哪些儲存層？**  
    Azure 檔案支援兩個儲存層:進階儲存層和標準儲存層。 標準檔案共用是一般用途 (GPv1 或 GPv2) 儲存帳戶創建的,進階檔案共用是在檔案存儲存儲帳戶中創建的。 詳細瞭解如何建立[標準檔案共享](storage-how-to-create-file-share.md)與[進階檔案共享](storage-how-to-create-premium-fileshare.md)。 
    
    > [!NOTE]
    > 不能從 Blob 儲存帳戶或*進階*通用 (GPv1 或 GPv2) 儲存帳戶創建 Azure 檔案共用。 標準 Azure 檔案共享必須僅在*標準*通用帳戶中創建,並且必須僅在檔案存儲儲存帳戶中創建高級 Azure 檔案共用。 *進階*通用 (GPv1 和 GPv2) 儲存帳戶僅適用於進階頁面 Blob。 

* <a id="give-us-feedback"></a>
  **我真的希望看到一個特定的功能添加到 Azure 檔。你能添加它嗎?**  
    Azure 檔案服務小組很樂意聽到您對於我們所提供之服務的所有意見反應。 請在 [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) \(英文\) 上投票支持您認同的功能要求！ 我們期待能透過諸多新的功能讓您感到滿意。

  **Azure 檔是否支援文件鎖定?**  
    是的,Azure 檔案完全支援 SMB/Windows 樣式的檔案鎖定,[請參考詳細資訊](https://docs.microsoft.com/rest/api/storageservices/managing-file-locks)。 
    
## <a name="azure-file-sync"></a>Azure 檔案同步

* <a id="afs-region-availability"></a>
  **哪些區域支援 Azure 檔案同步？**  
    如需可用區域的清單，請參閱 Azure 檔案同步規劃指南的[區域可用性](storage-sync-files-planning.md#azure-file-sync-region-availability)一節。 我們會持續新增其他區域的支援，包括非公用區域。

* <a id="cross-domain-sync"></a>
  **相同的同步群組中是否可以同時有已加入網域和未加入網域的伺服器？**  
    是。 同步群組可以包含具有不同 Active Directory 成員資格的伺服器端點，即使它們是未加入網域的端點也一樣。 雖然就技術上來說，這樣的設定是可行的，但我們不建議您將此作為一般設定，因為針對某部伺服器上的檔案和資料夾所定義的存取控制清單 (ACL)，可能無法由同步群組中的其他伺服器強制執行。 為獲得最佳結果，建議您在相同 Active Directory 樹系的伺服器之間、在位於不同 Active Directory 樹系但已建立信任關係的伺服器之間，或是在不在網域中的伺服器之間進行同步。 我們建議您避免混用這些設定。

* <a id="afs-change-detection"></a>
  **我使用 SMB 或在門戶中直接在 Azure 檔案共用中創建了一個檔。檔與同步組中的伺服器同步需要多長時間?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**如果同一文件幾乎同時在兩台伺服器上更改,會發生什麼情況?**  
    Azure 檔案同步會使用簡單的衝突解決策略：我們會對已同時在兩部伺服器上變更的檔案，保留這兩個變更。 最新寫入的變更會保留原始檔案名稱。 較舊的檔案則會在名稱後面附加「來源」機器和衝突號碼。 它會遵循此分類法： 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\<ext\>  

    例如，如果 CentralServer 是發生較舊寫入的位置，則 CompanyReport.docx 的第一個衝突會變成 CompanyReport CentralServer.docx。 第二個衝突會命名為 CompanyReport-CentralServer-1.docx。 Azure 檔案同步支援每個檔 100 個衝突檔。 達到衝突檔的最大數量後,該檔將無法同步,直到衝突文件數小於 100。

* <a id="afs-storage-redundancy"></a>
  **Azure 檔案同步是否支援異地備援儲存體？**  
    是，Azure 檔案服務同時支援本機備援儲存體 (LRS) 和異地備援儲存體 (GRS)。 如果您從針對 GRS 設定的帳戶起始配對區域之間的儲存體帳戶容錯移轉，Microsoft 會建議您將新的區域視為僅只資料的備份。 Azure 檔案同步不會自動開始同步處理新的主要區域。 

* <a id="sizeondisk-versus-size"></a>
  **為什麼使用 Azure 檔案同步後,檔的*磁碟屬性的大小*與*Size*屬性不匹配?**  
  請參閱[了解雲端階層處理](storage-sync-cloud-tiering.md#sizeondisk-versus-size)。

* <a id="is-my-file-tiered"></a>
  **如何判斷檔是否已分層?**  
  請參閱[了解雲端階層處理](storage-sync-cloud-tiering.md#is-my-file-tiered)。

* <a id="afs-recall-file"></a>**要使用的檔已分層。如何將檔撤回到磁碟以在本地使用?**  
  請參閱[了解雲端階層處理](storage-sync-cloud-tiering.md#afs-recall-file)。

* <a id="afs-force-tiering"></a>
  **如何強制對檔或目錄進行分層?**  
  請參閱[了解雲端階層處理](storage-sync-cloud-tiering.md#afs-force-tiering)。

* <a id="afs-effective-vfs"></a>
  **當我在捲上有多個伺服器終結點時,如何解釋*無容量空間*?**  
  請參閱[了解雲端階層處理](storage-sync-cloud-tiering.md#afs-effective-vfs)。

* <a id="afs-files-excluded"></a>
  **Azure 檔案同步會自動排除哪些檔案或資料夾？**  
  請參考[跳過的檔案](storage-sync-files-planning.md#files-skipped)。

* <a id="afs-os-support"></a>
  **我可以將 Azure 檔案同步與 Windows Server 2008 R2、Linux 或網路連接儲存裝置 (NAS) 搭配使用嗎？**  
    目前,Azure 檔同步僅支援 Windows 伺服器 2019、Windows 伺服器 2016 和 Windows 伺服器 2012 R2。 現階段我們沒有其他計劃，但會保持開放態度，並根據客戶需求來支援其他平台。 請前往 [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) \(英文\)，讓我們知道您希望我們支援哪些平台。

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **分層的檔案為何存在於伺服器端點命名空間以外？**  
    在 Azure 檔案同步代理程式版本 3 之前，Azure 檔案同步會禁止移動位於伺服器端點以外外、但與伺服器端點位於相同磁碟區上的分層檔案。 非分層檔案的複製作業和移動，以及從分層磁碟區到其他磁碟區的移動，均不受影響。 之所以會有此行為，是因為系統隱含地假設在相同磁碟區上執行這些移動作業的檔案總管和其他 Windows API (幾近於) 是 即時的重新命名作業。 這表示，當 Azure 檔案同步回復雲端中的資料時，移動作業將會使檔案總管或其他的移動方法 (例如命令列或 PowerShell) 呈現為無回應的狀態。 從 [Azure 檔案同步代理程式 3.0.12.0 版](storage-files-release-notes.md#supported-versions)開始，Azure 檔案同步將可讓您移動伺服器端點以外的分層檔案。 我們讓分層的檔案以分層的形式存在於伺服器端點以外，然後在背景中回復檔案，以避免產生負面影響。 這表示，相同磁碟區上的移動會即時執行，而我們會在移動完成後，再執行所有將檔案回復至磁碟的工作。 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **伺服器上的 Azure 檔同步(同步、雲端等)出現問題。我應該刪除並重新創建伺服器終結點嗎?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **是否可以將儲存體同步服務及/或儲存體帳戶移至不同的資源群組或訂用帳戶？**  
   是，可在現有的 Azure AD 租用戶內將儲存體同步服務和/或儲存體帳戶移至不同的資源群組或訂用帳戶。 如果移動儲存體帳戶，您需要將儲存體帳戶的存取權給予混合式檔案同步服務 (請參閱[確保 Azure 檔案同步有儲存體帳戶的存取權](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac))。

    > [!Note]  
    > Azure 檔案同步不支援將訂用帳戶移至不同的 Azure AD 租用戶。
    
* <a id="afs-ntfs-acls"></a>
  **Azure 檔案同步是否會在 Azure 檔案中隨著資料保留目錄/檔案層級 NTFS AC？**

    自 2020 年 2 月 24 日起,Azure 檔同步分層的新 ACL 和現有 ACL 將以 NTFS 格式保留,直接對 Azure 檔案共享進行的 ACL 修改將同步到同步群組中的所有伺服器。 對 Azure 檔所做的 ACL 所做的任何更改都將透過 Azure 檔同步同步。將資料複製到 Azure 檔時,請確保使用 SMB 存取共享並保留 ACL。 現有的基於 REST 的工具(如 AzCopy 或儲存資源管理員)不會保留 ACL。

    如果在檔同步託管檔共用上啟用了 Azure 備份,則檔 ACL 可以繼續還原,作為備份還原工作流的一部分。 這適用於整個共用或單個檔/目錄。

    如果將快照用作檔同步管理的檔共用的自管理備份解決方案的一部分,則如果快照是在 2020 年 2 月 24 日之前拍攝的,則可能無法將 ACL 正確還原到 NTFS ACL。 如果發生這種情況,請考慮聯繫 Azure 支援。
    
## <a name="security-authentication-and-access-control"></a>安全性、驗證和存取控制
* <a id="ad-support"></a>
**Azure 檔案是否支援基於標識的身份驗證和存取控制?**  
    
    是的,Azure 檔支援基於身份的身份驗證和訪問控制。 您可以選擇使用基於標識的存取控制的兩種方法之一:活動目錄 (AD)(預覽)或 Azure 活動目錄域服務 (Azure AD DS) (GA)。 AD 支援使用 AD 網域聯接的電腦(本地或 Azure 中)進行身份驗證,以便透過 SMB 存取 Azure 檔案共用。 Azure AD DS 透過 SMB 對 Azure 檔進行身分驗證,使 Azure AD DS 功能中加入的 Windows VM 能夠使用 Azure AD 認證共享、目錄和檔。 有關詳細資訊,請參閱對[SMB 存取的基於 Azure 檔的身份驗證支援的概述](storage-files-active-directory-overview.md)。 

    Azure 檔案服務提供兩種管理存取控制的額外方式：

    - 您可以使用共用存取簽章 (SAS)，來產生具有特定權限且在指定時間間隔內有效的權杖。 例如，您可以產生具有指定檔案唯讀存取權且會在 10 分鐘到期的權杖。 擁有權杖的任何人，在該權杖的 10 分鐘有效時間內，具有該檔案的唯讀存取權。 共用訪問簽名金鑰僅通過 REST API 或用戶端庫中支援。 您必須使用儲存體帳戶金鑰，透過 SMB 掛接 Azure 檔案共用。

    - Azure 檔案同步會保留並複製所有判別 ACL 或 DACL (不論是以 Active Directory 為基礎或本機) 到它要同步的所有伺服器端點。 由於 Windows Server 已經能夠利用 Active Directory 進行驗證，因此，在提供 Active Directory 型驗證的完整支援和 ACL 支援之前，Azure 檔案同步是一個有效的權宜選項。
    
    您可以參考[授權存取 Azure 儲存](https://docs.microsoft.com/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json),以全面表示 Azure 儲存服務上支援的所有協定。 

* <a id="ad-support-devices"></a>
**Azure 檔案 Azure 活動目錄網域服務 (Azure AD DS) 身份驗證是否支援使用從連接到 Azure AD 或註冊 Azure AD 的裝置中的 Azure AD 認證進行 SMB 存取?**

    否，不支援此案例。

* <a id="ad-support-rest-apis"></a>
**是否有 REST API 支援取得/設定/複製目錄/檔案 NTFS ACL？**

    是的,我們支援 REST API,這些 API 在使用[2019-07-07(](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07)或更高版本)REST API時獲取、設置或複製目錄或檔的 NTFS ACL。

* <a id="ad-vm-subscription"></a>
**我是否可以從不同訂用帳戶下的 VM 使用 Azure AD 認證來存取 Azure 檔案？**

    若檔案共用部署所在的訂用帳戶與和部署到 VM 所加入之網域之 Azure AD Domain Services 相同的 Azure AD 租用戶關聯，則您可以使用相同的 Azure AD 認證來存取 Azure 檔案。 不僅訂用帳戶有限制，關聯的 Azure AD 租用戶也有。
    
* <a id="ad-support-subscription"></a>
**是否可以使用與檔案共享關聯的主租戶不同的 Azure AD 租戶啟用 Azure 檔案 Azure AD DS 或 AD 身份驗證?**

    否,Azure 檔僅支援 Azure AD DS 或 AD 整合與駐留在與檔案共用相同的訂閱中的 Azure AD 租戶。 只有一個訂用帳戶能與 Azure AD 租用戶關聯。 此限制同時適用於 Azure AD DS 和 AD 身份驗證方法。 使用 AD 進行身份驗證時,AD 認證必須同步到儲存帳戶關聯的 Azure AD。

* <a id="ad-linux-vms"></a>
**Azure 檔案 Azure AD DS 或 AD 認證是否支援 Linux VM?**

    否,不支援來自 Linux VM 的身份驗證。

* <a id="ad-aad-smb-afs"></a>
**是否可以在 Azure 檔案同步管理的檔案分享上利用 Azure 檔案 Azure 檔案 AD DS 身份驗證或活動目錄 (AD) 身份驗證(預覽)?**

    可以,您可以在 Azure 檔同步管理的檔案共享上啟用 Azure AD DS 或 AD 身份驗證。對本地檔案伺服器上的目錄/檔案 NTFS ACL 的更改將分層到 Azure 檔,反之亦然。

* <a id="ad-aad-smb-files"></a>
**如何檢查我的儲存帳戶和 AD 網域資訊是否啟用了 AD 身份驗證?**

    您可以參考[此處](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#1-enable-ad-authentication-for-your-account)提供的說明,以驗證儲存帳戶上是否啟用了 Azure 檔案 AD 身份驗證並檢索 AD 域資訊。

* <a id="encryption-at-rest"></a>
**如何確保我的 Azure 檔案共用會進行待用加密？**  

    是。 有關詳細資訊,請參閱[Azure 儲存服務加密](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* <a id="access-via-browser"></a>
**如何使用網頁瀏覽器來提供對特定檔案的存取權？**  

    您可以使用共用存取簽章，來產生具有特定權限且在指定時間間隔內有效的權杖。 例如，您可以產生可在一段特定時間內提供特定檔案唯讀存取權限的權杖。 任何人只要擁有此 URL，就可以在權杖有效時間內，直接從任何網頁瀏覽器存取檔案。 您可以從類似儲存體總管的 UI，輕易產生共用存取簽章金鑰。

* <a id="file-level-permissions"></a>
**是否可以對共用中的資料夾指定唯讀或只寫許可權?**  

    如果您使用 SMB 來掛接檔案共用，則您對權限沒有資料夾層級的控制。 不過，如果您使用 REST API 或用戶端程式庫建立共用存取簽章，就可以在共用中的資料夾上指定唯讀或唯寫權限。

* <a id="ip-restrictions"></a>
**我是否可以對 Azure 檔案共用實作 IP 限制？**  

    是。 您可以在儲存體帳戶層級限制對 Azure 檔案共用的存取。 有關詳細資訊,請參閱設定[Azure 儲存防火牆和虛擬網路](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

* <a id="data-compliance-policies"></a>
**Azure 檔案服務支援哪些資料合規性原則？**  

   Azure 檔案服務和 Azure 儲存體的其他儲存體中所使用的服務都是在相同的儲存體架構上運作。 Azure 檔案服務會套用其他 Azure 儲存體服務中所使用的相同資料合規性原則。 如需 Azure 儲存體資料合規性的詳細資訊，您可以參閱 [Azure 儲存體合規性供應項目](https://docs.microsoft.com/azure/storage/common/storage-compliance-offerings)，並前往 [Microsoft 信任中心](https://microsoft.com/trustcenter/default.aspx)。
   
### <a name="ad-authentication"></a>AD 驗證
* <a id=""></a>
**Azure 檔案 Azure AD 認證是否支援 Linux VM?**

    否,不支援來自 Linux VM 的身份驗證。

* <a id="ad-multiple-forest"></a>
**Azure 檔案 AD 認證是否支援使用多個林與 AD 環境整合?**    

    Azure 檔案 AD 身份驗證僅與儲存帳戶註冊的 AD 網域服務的林整合。 要支援來自其他 AD 林的身份驗證,您的環境必須正確配置林信任。 Azure 檔案註冊到 AD 網域服務的方式與常規檔案伺服器大致相同,在 AD 中創建標識(電腦或服務登錄帳戶)以進行身份驗證。 唯一的區別是,存儲帳戶的已註冊的 SPN 以與域後綴不匹配的"file.core.windows.net"結尾。 請諮詢域管理員,瞭解是否需要對 DNS 路由策略進行任何更新,才能由於不同的域後綴而啟用多個林身份驗證。

* <a id=""></a>
**哪些區域可用於 Azure 檔案 AD 身份驗證(預覽)?**

    有關詳細資訊,請參閱[AD 區域可用性](storage-files-identity-auth-active-directory-enable.md#regional-availability)。

* <a id="ad-aad-smb-afs"></a>
**是否可以在 Azure 檔案同步管理的檔案分享上利用 Azure 檔案活動目錄 (AD) 身份驗證(預覽)?**

    可以,您可以在 Azure 檔案同步管理的檔案共享上啟用 AD 身份驗證。對本地檔案伺服器上的目錄/檔案 NTFS ACL 的更改將分層到 Azure 檔,反之亦然。

* <a id="ad-aad-smb-files"></a>
**如何檢查我的儲存帳戶和 AD 網域資訊是否啟用了 AD 身份驗證?**

    您可以參考[此處](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#enable-ad-authentication-for-your-account)提供的說明,以驗證儲存帳戶上是否啟用了 Azure 檔案 AD 身份驗證並檢索 AD 域資訊。

* <a id="ad-aad-smb-files"></a>
**在 AD 中創建電腦帳戶或服務登錄帳戶以表示我的儲存帳戶是否有任何區別?**

    創建[計算機帳戶](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)(預設)[或服務登錄帳戶](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)與身份驗證如何使用 Azure 檔沒有區別。 您可以自行選擇如何在 AD 環境中將儲存帳戶表示為標識。 在聯接-AzStorage 帳戶"cmdlet"中設置的默認域帳戶類型是計算機帳戶。 但是,在 AD 環境中設定的密碼過期期限對於電腦或服務登入帳戶可能不同,您需要考慮在[AD 中更新儲存帳戶識別的密碼](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable#5-update-ad-account-password)。

## <a name="on-premises-access"></a>內部部署存取

* <a id="port-445-blocked"></a>
**我的 ISP 或 IT 阻止埠 445,因為埠 445 正在阻止 Azure 檔裝載失敗。我該怎麼辦?**

    你可以在這裡了解[各種方法來解決阻塞埠445。](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked) Azure 檔僅允許使用 SMB 3.0(支援加密)從區域或資料中心外部進行連接。 SMB 3.0 協定引入了許多安全功能,包括通道加密,在 Internet 上使用非常安全。 但是,由於在較低 SMB 版本中發現的漏洞的歷史原因,埠 445 可能已被阻止。 在理想情況下,埠應僅針對 SMB 1.0 流量阻塞,並且所有用戶端上應關閉 SMB 1.0。

* <a id="expressroute-not-required"></a>
**我必須使用 Azure ExpressRoute 來連線到 Azure 檔案服務，還是必須在內部部署中使用 Azure 檔案同步？**  

    否。 不需要 ExpressRoute 就能存取 Azure 檔案共用。 如果您要直接在內部部署掛接 Azure 檔案共用，只需開啟連接埠 445 (TCP 輸出) 以進行網際網路存取 (這是 SMB 用來進行通訊的連接埠)。 如果您使用 Azure 檔案同步，只需連接埠 443 (TCP 輸出) 以進行 HTTPS 存取 (不需要 SMB)。 不過，您可以** 將 ExpressRoute 與這些其中一個選項搭配使用。

* <a id="mount-locally"></a>
**如何在本地電腦上裝載 Azure 檔共用?**  

    如果已開啟連接埠 445 (TCP 輸出) 且您的用戶端支援 SMB 3.0 通訊協定 (例如，若您使用的是 Windows 10 或 Windows Server 2016)，即可使用 SMB 通訊協定掛接檔案共用。 如果組織的原則或您的 ISP 會封鎖連接埠 445，您可以使用 Azure 檔案同步來存取 Azure 檔案共用。

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**如何備份我的 Azure 檔案共用？**  
    您可以使用定期[共用快照集](storage-snapshots-files.md)來防範意外刪除的情況。 您也可以使用 AzCopy、RoboCopy，或是可備份已掛接檔案共用的協力廠商備份工具。 Azure 備份會提供 Azure 檔案的備份。 深入了解如何[使用 Azure 備份來備份 Azure 檔案共用](https://docs.microsoft.com/azure/backup/backup-azure-files)。

## <a name="share-snapshots"></a>共用快照集

### <a name="share-snapshots-general"></a>共用快照集：一般
* <a id="what-are-snaphots"></a>
**檔案共用快照集有哪些？**  
    您可以使用 Azure 檔案共用快照集，來建立檔案共用的唯讀版本。 您也可以使用 Azure 檔案服務，將較舊版本的內容複製回到相同的共用或是 Azure 或內部部署的替代位置，以進行更多修改。 若要深入了解共用快照集，請參閱[共用快照集概觀](storage-snapshots-files.md)。

* <a id="where-are-snapshots-stored"></a>
**共用快照集會儲存在哪裡？**  
    共用快照集會儲存在與檔案共用相同的儲存體帳戶中。

* <a id="snapshot-consistency"></a>
**共用快照集是否具有應用程式一致性？**  
    否，共用快照集不具應用程式一致性。 使用者必須先將寫入從應用程式排清到共用，然後再擷取共用快照集。

* <a id="snapshot-limits"></a>
**我可以使用的共用快照集數目是否有限制？**  
    是。 Azure 檔案服務最多可保留 200 個共用快照集。 共用快照集不會計入共用配額，因此，所有共用快照集所使用的總空間沒有每個共用的限制。 但儲存體帳戶限制依然有效。 保留 200 個共用快照集之後，必須先刪除舊的快照集，才能建立新的共用快照集。

* <a id="snapshot-cost"></a>
**共用快照集需要多少成本？**  
    使用快照集的費用包括標準交易成本和標準儲存體的費用。 快照集具有累加性質。 基底快照集便是共用本身。 所有後續的快照集都是累加的，而且只會儲存與上一個快照集相異之處。 這表示如果您的工作負載變換程度不高，帳單中列示的差異變更也不會太多。 如果需要標準 Azure 檔案的定價資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/storage/files/)。 現在判斷共用快照集耗用大小的方式，是比較計費的容量與已使用的容量。 目前正在設法開發改善報告功能的工具。

* <a id="ntfs-acls-snaphsots"></a>
**目錄與檔案上的 NTFS ACL 在共用快照中是否會保留下來？**  
    目錄與檔案上的 NTFS ACL 在共用快照中會保留下來。

### <a name="create-share-snapshots"></a>建立共用快照集
* <a id="file-snaphsots"></a>
**我可以建立個別檔案的共用快照集嗎？**  
    共用快照集是在檔案共用層級建立的。 您可以從檔案共用快照集還原個別檔案，但您無法建立檔案層級的共用快照集。 不過，如果您已擷取共用層級的共用快照集，而且想要列出已變更特定檔案的共用快照集，就可以在 Windows 所掛接之共用上的 [舊版]**** 下方執行此動作。 
    
    如果您需要檔案快照集功能，請前往 [Azure 檔案服務 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) \(英文\)，讓我們知道。

* <a id="encrypted-snapshots"></a>
**我可以建立已加密檔案共用的共用快照集嗎？**  
    您可以對已啟用待用加密的 Azure 檔案共用擷取共用快照集。 您可以將共用快照集內的檔案還原至已加密的檔案共用。 如果您的共用已加密，則共用快照集也會加密。

* <a id="geo-redundant-snaphsots"></a>
**我的共用快照集是否會異地備援？**  
    共用快照集擁有和目標 Azure 檔案共用相同的備援。 如果您為帳戶選取了異地備援儲存體，則共用快照集也會在配對區域中進行備援儲存。

### <a name="manage-share-snapshots"></a>管理共用快照集
* <a id="browse-snapshots-linux"></a>
**我可以從 Linux 瀏覽共用快照集嗎？**  
    您可以使用 Azure CLI，在 Linux 中建立、列出、瀏覽及還原共用快照集。

* <a id="copy-snapshots-to-other-storage-account"></a>
**我可以將共用快照集複製到不同的儲存體帳戶嗎？**  
    您可以將共用快照集的檔案複製到其他位置，但無法複製共用快照集本身。

### <a name="restore-data-from-share-snapshots"></a>從共用快照集還原資料
* <a id="promote-share-snapshot"></a>
**我可以將共用快照集升階到基底共用嗎？**  
    您可以將共用快照集的資料複製到任何其他目的地。 您無法將共用快照集升階到基底共用。

* <a id="restore-snapshotted-file-to-other-share"></a>
**我可以將共用快照集的資料還原到不同的儲存體帳戶嗎？**  
    是。 您可以將共用快照集的檔案複製到原始位置或替代位置，其中包含相同區域或不同區域中的同一個儲存體帳戶或不同的儲存體帳戶。 您也可以將檔案複製到內部部署位置或任何其他雲端。    
  
### <a name="clean-up-share-snapshots"></a>清除共用快照集
* <a id="delete-share-keep-snapshots"></a>
**我是否可以刪除共用，而不刪除共用快照集？**  
    如果您的共用上有使用中的共用快照集，就無法刪除共用。 您可以使用 API 來刪除共用快照集以及該共用。 您也可以在 Azure 入口網站中同時刪除共用快照集和共用。

* <a id="delete-share-with-snapshots"></a>
**如果我刪除儲存體帳戶，共用快照集會發生什麼情況？**  
    如果您刪除儲存體帳戶，也會一併刪除共用快照集。

## <a name="billing-and-pricing"></a>計費和定價
* <a id="vm-file-share-network-traffic"></a>
**Azure VM 和 Azure 檔案共用之間的網路流量，會計算為向訂用帳戶收費的外部頻寬嗎？**  
    如果檔案共用和 VM 位於相同的 Azure 區域，就不會針對檔案共用和 VM 之間的流量收取額外費用。 如果檔案共用和 VM 位於不同的區域，則兩者之間的流量會以外部頻寬收費。

* <a id="share-snapshot-price"></a>
**共用快照集需要多少成本？**  
     在預覽期間，共用快照集容量是免費的。 適用標準儲存體的輸出和交易成本。 正式運作之後，將會針對共用快照集上的容量和交易來向訂用帳戶收費。
     
     共用快照集具有累加性質。 基底共用快照集便是共用本身。 所有後續的共用快照集都是累加的，而且只會儲存與上一個共用快照集相異之處。 變更的內容才會計費。 如果您的共用有 100 GiB 資料，但在上一個共用快照集之後只變更了 5 GiB，則共用快照集只會再耗用 5 GiB，並以 105 GiB 來計費。 如需交易和標準輸出費用的詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/storage/files/)。

## <a name="scale-and-performance"></a>規模和效能
* <a id="files-scale-limits"></a>
**Azure 檔案服務有何規模限制？**  
    如需 Azure 檔案服務的延展性和效能目標相關資訊，請參閱 [Azure 檔案服務延展性和效能目標](storage-files-scale-targets.md)。

* <a id="need-larger-share"></a>
**Azure 檔案共用可以使用哪些大小?**  
    Azure 檔案共用大小(進階和標準)可以擴展到 100 TiB。 有關將說明載入標準層的較大檔共用,請參閱規劃指南的[「板載到較大檔共用」](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib)部分。

* <a id="lfs-performance-impact"></a>
**擴展檔案共用配額是否會影響工作負荷或 Azure 檔同步?**
    
    否。 擴展配額不會影響工作負荷或 Azure 檔同步。

* <a id="open-handles-quota"></a>
**多少個用戶端可以同時存取相同的檔案？**   
    單一檔案的開啟控制代碼配額為 2,000 個。 當您擁有 2,000 個開啟控制代碼時，會顯示一則錯誤訊息以指出已達到配額。

* <a id="zip-slow-performance"></a>
**當我在 Azure 檔中解壓縮檔時,我的性能很慢。我該怎麼辦?**  
    若要將大量檔案傳輸到 Azure 檔案服務，建議您使用 AzCopy (適用於 Windows；針對 Linux/Unix 則是在預覽版中) 或 Azure PowerShell。 這些工具已針對網路傳輸最佳化。

* <a id="slow-perf-windows-81-2012r2"></a>
**當我在 Windows Server 2012 R2 或 Windows 8.1 上掛接 Azure 檔案共用之後，為什麼我的效能變慢了？**  
    在 Windows Server 2012 R2 和 Windows 8.1 上掛接 Azure 檔案共用時，有一個已知的問題。 此問題已在 Windows 8.1 和 Windows Server 2012 R2 的 2014 年 4 月累計更新中加以修補。 若要取得最佳效能，請確定 Windows Server 2012 R2 和 Windows 8.1 的所有執行個體都已套用這個修補程式 (應始終通過 Windows 更新接收 Windows 修補程式。有關詳細資訊,請參閱關聯的 Microsoft 知識庫文章[,當您從 Windows 8.1 或伺服器 2012 R2 存取 Azure 檔時效能降低](https://support.microsoft.com/kb/3114025)。

## <a name="features-and-interoperability-with-other-services"></a>功能及與其他服務的互通性
* <a id="cluster-witness"></a>
**我可以使用 Azure 檔案共用作為 Windows Server 容錯移轉叢集的「檔案共用見證」** 嗎？**  
    Azure 檔案共用目前不支援此設定。 如需如何為 Azure Blob 儲存體設定此項的詳細資訊，請參閱[為容錯移轉叢集部署雲端見證](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) \(機器翻譯\)。

* <a id="containers"></a>
**我是否可以在 Azure 容器執行個體上掛接 Azure 檔案共用？**  
    是，當您想要保存超過容器執行個體存留期的資訊時，Azure 檔案共用是一個絕佳的選項。 如需詳細資訊，請參閱[使用 Azure 容器執行個體來掛接 Azure 檔案共用](../../container-instances/container-instances-mounting-azure-files-volume.md)。

* <a id="rest-rename"></a>
**REST API 中是否有重新命名操作?**  
    目前沒有。

* <a id="nested-shares"></a>
**我可以設置嵌套共用嗎?換句話說,一份股票?**  
    否。 檔案分享*是*您可以裝載的虛擬驅動程式,因此不支援嵌套共用。

* <a id="ibm-mq"></a>
**如何將 Azure 檔案服務與 IBM MQ 搭配使用？**  
    IBM 已發行文件來協助 IBM MQ 客戶，利用 IBM 服務來設定 Azure 檔案服務。 如需詳細資訊，請參閱[如何使用 Microsoft Azure 檔案服務來設定 IBM MQ 多重執行個體佇列管理員](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service) \(英文\)。

## <a name="see-also"></a>另請參閱
* [針對 Windows 中的 Azure 檔案服務進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)
* [針對 Linux 中的 Azure 檔案服務進行疑難排解](storage-troubleshoot-linux-file-connection-problems.md)
* [針對 Azure 檔案同步進行移難排解](storage-sync-files-troubleshoot.md)
