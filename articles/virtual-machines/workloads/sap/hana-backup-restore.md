---
title: 在 SAP HANA on Azure (大型執行個體) 上進行 HANA 備份和還原 | Microsoft Docs
description: 如何在 SAP HANA on Azure (大型執行個體) 上執行 HANA 備份和還原
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72430086"
---
# <a name="backup-and-restore"></a>備份與還原

>[!IMPORTANT]
>本文不能替代 SAP HANA 管理文檔或 SAP 說明。 我們希望您在 SAP HANA 管理和操作方面有扎實的瞭解和專業知識，尤其是在備份、恢復、高可用性和災害復原方面。 在本文中，將顯示 SAP HANA 工作室的螢幕截圖。 SAP 管理工具和工具本身的畫面內容、結構和性質，可能會因 SAP HANA 的版本而異。

請務必使用您的 HANA 版本和版次，在您的環境中執行步驟和程序。 本文中描述的某些過程被簡化，以便更好的一般理解。 它們不應用作最終操作手冊的詳細步驟。 如果要為配置創建操作手冊，請測試和練習流程，並記錄與您的特定配置相關的流程。 

運算元據庫最重要的一個方面是保護它們免受災難性事件的影響。 這些事件可能由任何原因造成，從天然災害到簡單的使用者錯誤都有可能。

備份資料庫，並能夠將其還原到任何時間點（例如，在某人刪除關鍵資料之前），可以還原到盡可能接近中斷前的狀態。

必須執行兩種類型的備份才能實現還原功能：

- 資料庫備份：完整、增量或差異備份
- 交易記錄備份

除了在應用程式層級執行的完整資料庫備份之外，您可以透過儲存體快照集執行備份。 存儲快照不會替換事務記錄備份。 對於「將資料庫還原到特定時間點」或是「從已經認可的交易清空記錄」來說，交易記錄備份仍然很重要。 存儲快照可以通過快速提供資料庫的滾動映射來加快恢復。 

SAP HANA on Azure (大型執行個體) 提供兩個備份和還原選項：

- **自己動手做 (DIY)。** 確保磁碟空間足夠後，請使用以下磁片備份方法之一執行完整的資料庫並記錄備份。 您可以直接備份到附加到 HANA 大型實例單元的卷，也可以備份到在 Azure 虛擬機器 （VM） 中設置的 NFS 共用。 在後面一種情況下，客戶會在 Azure 中設定 Linux VM、將 Azure 儲存體連結至 VM，並透過該 VM 中所設定的 NFS 伺服器來共用儲存體。 如果針對直接附加到 HANA 大型實例單元的卷執行備份，請將備份複製到 Azure 存儲帳戶。 在設置匯出基於 Azure 存儲的 NFS 共用的 Azure VM 後執行此操作。 您還可以使用 Azure 備份保存庫或 Azure 冷存儲。 

   另一個選項是使用協力廠商資料保護工具在備份複製到 Azure 存儲帳戶後存儲備份。 對於出於合規性和審核目的需要存儲更長時間的資料，可能還需要 DIY 備份選項。 不論是哪種情況，備份都會複製到透過 VM 與 Azure 儲存體所表示的 NFS 共用中。

- **基礎結構備份和還原功能。** 您還可以使用 Azure（大型實例）上 SAP HANA 的基礎基礎結構提供的備份和恢復功能。 此選項可滿足備份和快速還原的需要。 本節的其餘部分討論 HANA (大型執行個體) 隨附的備份和還原功能。 本節還介紹備份和恢復與 HANA 大型實例提供的災害復原功能的關係。

> [!NOTE]
>   HANA 大型實例的基礎基礎結構使用的快照技術依賴于 SAP HANA 快照。 此時，SAP HANA 快照與 SAP HANA 多租戶資料庫容器的多個租戶不配合使用。 如果只部署了一個租戶，SAP HANA 快照可以正常工作，您可以使用此方法。

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>在 Azure（大型實例）上使用 SAP HANA 的存儲快照

SAP HANA on Azure (大型執行個體) 底下的儲存體基礎結構支援磁碟區的儲存體快照集。 不論是備份還是還原磁碟區都受到支援，但有下列考量事項：

- 系統會經常建立存放磁碟區快照，而不是進行完整的資料庫備份。
- 當通過 /hana/資料和 /hana/shared（包括 /usr/sap）卷觸發快照時，快照技術在運行存儲快照之前啟動 SAP HANA 快照。 在儲存體快照集復原之後，此 SAP HANA 快照集是最後記錄還原的設定點。 要成功使用 HANA 快照，需要一個活動的 HANA 實例。 在 HSR 方案中，當前無法執行 HANA 快照的輔助節點不支援存儲快照。
- 存儲快照成功運行後，SAP HANA 快照將被刪除。
- 系統會經常建立交易記錄備份並儲存在 /hana/logbackups 磁碟區或 Azure 中。 您可以觸發含有交易記錄備份的 /hana/logbackups 磁碟區，對它個別擷取快照集。 在這種情況下，您不需要運行 HANA 快照。
- 如果必須將資料庫還原到特定時間點，則對於生產中斷，請請求 Azure 上的 Microsoft Azure 支援或 AZURE 上的 SAP HANA 還原到特定存儲快照。 例如，將沙箱系統還原到其原始狀態的計劃性還原。
- 存儲快照中包含的 SAP HANA 快照是應用在拍攝存儲快照後運行和存儲的事務記錄備份的偏移點。
- 建立這些交易記錄備份的目的是要將資料庫還原回特定的時間點。

您可以執行針對三類卷的存儲快照：

- /hana/資料和 /hana/共用的組合快照，包括 /usr/sap。 此快照集需要建立 SAP HANA 快照集，作為儲存體快照集的準備工作。 從存儲的角度來看，SAP HANA 快照可確保資料庫處於一致狀態。 對於還原過程，這是要設置的點。
- 針對 /hana/logbackups 的個別快照集。
- 作業系統分割區。

要獲取最新的快照腳本和文檔，請參閱[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。 當您從[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)下載快照腳本包時，您將獲得三個檔。 其中一個檔記錄在 PDF 中，用於提供的功能。 下載工具集後，請按照"獲取快照工具"中的說明進行操作。

## <a name="storage-snapshot-considerations"></a>儲存體快照考量事項

>[!NOTE]
>存儲快照佔用分配給 HANA 大型實例單元的存儲空間。 請考慮計畫存儲快照的以下方面以及要保留的存儲快照數。 

SAP HANA on Azure (大型執行個體) 儲存體快照集的獨特技巧包括：

- 拍攝時的特定存儲快照佔用的存儲空間很少。
- 隨著資料內容的變化和 SAP HANA 資料檔案中的內容在存儲卷上的變化，快照需要存儲原始塊內容和資料更改。
- 因此，儲存體快照的大小會增加。 快照存在的時間越長，儲存體快照就會變得越大。
- 在儲存體快照集的生命週期中對 SAP HANA 資料庫磁碟區進行的變更越多，儲存體快照集所耗用的空間就越大。

SAP HANA on Azure (大型執行個體) 隨附固定的磁碟區大小供 SAP HANA 資料和記錄磁碟區使用。 執行這些磁碟區的快照集會耗用磁碟區空間。 您必須：

- 確定何時計畫存儲快照。
- 監視存儲卷的空間消耗。 
- 管理存儲的快照數。 

您可以在匯入大量資料或對 HANA 資料庫執行其他重大變更時，停用儲存體快照集。 


以下各節提供了執行這些快照的資訊，並包括一般建議：

- 儘管硬體可以維持每個卷 255 個快照，但您希望保持在遠低於此數位。 建議為 250 或更少。
- 執行儲存體快照集之前，請監視並追蹤可用空間。
- 根據可用空間來降低儲存體快照的數目。 您可以降低保留的快照集數目，也可以擴充磁碟區。 您可以訂購額外的儲存空間，以 1 TB 為單位。
- 在「使用 SAP 平台移轉工具 (R3load) 將資料移到 SAP HANA」或是「從備份還原 SAP HANA 資料庫」等活動進行期間，請在 /hana/data 磁碟區上停用儲存體快照集。 
- 在 SAP HANA 表進行大型重組期間，盡可能避免存儲快照。
- 儲存體快照集也是利用 SAP HANA on Azure (大型執行個體) 之災害復原功能的必要條件。

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>使用自助服務儲存體快照集的必要條件

要確保快照腳本成功運行，請確保 Perl 安裝在 HANA 大型實例伺服器上的 Linux 作業系統上。 Perl 預先安裝在您的 HANA 大型實例單元上。 若要檢查 Perl 版本，請使用下列命令：

`perl -v`

![執行此命令即可複製公開金鑰](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>設定儲存體快照集

要使用 HANA 大型實例設置存儲快照，請按照以下步驟操作。
1. 確定 HANA (大型執行個體) 伺服器的 Linux 作業系統上已安裝 Perl。
1. 修改 /etc/ssh/ssh\_config 以新增 _MACs hmac-sha1_ 這一行。
1. 在主節點上為運行的每個 SAP HANA 實例創建 SAP HANA 備份使用者帳戶（如果適用）。
1. 在所有 SAP HANA (大型執行個體) 伺服器上安裝 SAP HANA HDB 用戶端。
1. 在每個區域的第一部 SAP HANA (大型執行個體) 伺服器上，建立一個公開金鑰，以便存取其底下控制快照集建立的儲存體基礎結構。
1. 將指令碼和組態檔從 [ GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 複製到 SAP HANA 安裝中的 **hdbsql** 位置。
1. 根據需要修改*HANABackupDetails.txt*檔，以符合相應的客戶規格。

從 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 取得最新快照集指令碼和文件。 有關前面列出的步驟，請參閱[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。

### <a name="consideration-for-mcod-scenarios"></a>MCOD 案例的考量
如果在一個 HANA 大型實例單元上運行具有多個 SAP HANA 實例的[MCOD 方案](https://launchpad.support.sap.com/#/notes/1681092)，則為每個 SAP HANA 實例預配了單獨的存儲卷。 有關 MDC 和其他注意事項的詳細資訊，請參閱在 Azure 上的 SAP [HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"要記住的重要事項"。
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>步驟 1：安裝 SAP HANA HDB 用戶端

在 Azure（大型實例）上安裝在 SAP HANA 上的 Linux 作業系統包括運行 SAP HANA 存儲快照以用於備份和災害復原所必需的資料夾和腳本。 檢查[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md)中的更多最新版本。 

在安裝 SAP HANA 時，您有責任在 HANA 大型實例單元上安裝 SAP HANA HDB 用戶端。

### <a name="step-2-change-the-etcsshssh_config"></a>步驟 2：變更 /etc/ssh/ssh\_config

此步驟在[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"啟用與存儲的通信"中進行了介紹。


### <a name="step-3-create-a-public-key"></a>步驟 3︰建立公開金鑰

要啟用對 HANA 大型實例租戶的存儲快照介面的訪問，請通過公開金鑰建立登錄過程。 

在租戶中的 Azure（大型實例）伺服器上的第一個 SAP HANA 上，創建公開金鑰以訪問存儲基礎結構。 使用公開金鑰時，無需密碼即可登錄到存儲快照介面。 您也不需要使用公開金鑰維護密碼憑據。 

要生成公開金鑰，請參閱在 Azure 上的 SAP [HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"啟用與存儲的通信"。


### <a name="step-4-create-an-sap-hana-user-account"></a>步驟 4：建立 SAP HANA 使用者帳戶

要開始創建 SAP HANA 快照，請在 SAP HANA 中創建存儲快照腳本可以使用的使用者帳戶。 為此目的，在 SAP HANA Studio 中建立 SAP HANA 使用者帳戶。 使用者必須在 SYSTEMDB 下創建，*而不是*在 MDC 的 SID 資料庫下創建。 在單個容器環境中，使用者是在租戶資料庫中創建的。 此帳戶必須具有**備份管理員**和**目錄讀取**許可權。 

要設置和使用使用者帳戶，請參閱[GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"啟用與 SAP HANA 的通信"。


### <a name="step-5-authorize-the-sap-hana-user-account"></a>步驟 5：授權 SAP HANA 使用者帳戶

在此步驟中，您可以授權您創建的 SAP HANA 使用者帳戶，以便腳本在運行時無需提交密碼。 SAP HANA`hdbuserstore`命令支援創建 SAP HANA 使用者金鑰。 金鑰存儲在一個或多個 SAP HANA 節點上。 使用者金鑰可讓使用者存取 SAP HANA，而不需要在指令碼程序內管理密碼。 本文稍後會討論指令碼處理。

>[!IMPORTANT]
>使用快照命令運行的使用者上下文運行這些配置命令。 否則，快照命令將無法正常工作。


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>步驟 6：取得快照集指令碼、設定快照集並測試組態與連線能力

從 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1) 下載最新版本的指令碼。 腳本的安裝方式隨著腳本版本 4.1 而改變。 有關詳細資訊，請參閱[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中"啟用與 SAP HANA 的通信"。

有關命令的確切順序，請參閱[在 Azure 上為 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"輕鬆安裝快照工具（預設）"。 我們建議使用預設安裝。 

要從版本 3.x 升級到 4.1，請參閱[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"升級現有安裝"。 要卸載 4.1 工具集，請參閱[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"卸載快照工具"。

不要忘記在[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中運行"快照工具的完整設置"中描述的步驟。

安裝時的不同腳本和檔的目的在"這些快照工具是什麼？ 在[Azure 上的 SAP HANA 的 Microsoft 快照工具中](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。

在配置快照工具之前，請確保也正確配置了 HANA 備份位置和設置。 有關詳細資訊，請參閱[Azure 上的 SAP HANA 的 Microsoft 快照工具中的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"SAP HANA 配置"。

快照工具集的配置在[Azure 上的 SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)的 Microsoft 快照工具中的"設定檔 - HANABackupCustomerdetails.txt"中進行了描述。

#### <a name="test-connectivity-with-sap-hana"></a>使用 SAP HANA 測試連接

將所有組態資料放入 HANABackupCustomerDetails.txt** 檔案後，請檢查 HANA 執行個體資料的組態是否正確。 使用指令碼 `testHANAConnection`，其與 SAP HANA 相應增加或相應放大組態無關。

有關詳細資訊，請參閱[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中"檢查與 SAP HANA - testHANA 連接的連接"。

#### <a name="test-storage-connectivity"></a>測試存儲連接

下一個測試步驟是根據您放入*HANABackupCustomerDetails.txt*設定檔中的資料檢查與存儲的連接。 然後運行測試快照。 在運行該`azure_hana_backup`命令之前，必須運行此測試。 有關此測試的命令序列，請參閱[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中"檢查與存儲 - 測試存儲快照連接的連接"。

成功登入儲存體虛擬機器介面之後，指令碼會繼續進行階段 2 並建立測試快照集。 此處顯示了 SAP HANA 的三節點橫向擴展配置的輸出。

如果測試快照使用腳本成功運行，則可以計畫實際存儲快照。 如果不成功，請先調查問題，然後再前進。 測試快照集應先留著，直到完成第一個實際快照集為止。


### <a name="step-7-perform-snapshots"></a>步驟 7：執行快照集

完成準備步驟後，可以開始配置和安排實際存儲快照。 要排程的指令碼可與 SAP HANA 相應增加和相應放大組態搭配運作。 如需定期或正常執行備份指令碼，請透過使用 cron 公用程式排程指令碼。 

有關確切的命令語法和功能，請參閱[在 Azure 上為 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"執行快照備份 - azure_hana_backup"。 

當腳本`azure_hana_backup`運行時，它會在以下三個階段創建存儲快照：

1. 它運行 SAP HANA 快照。
1. 它運行存儲快照。
1. 它刪除在存儲快照運行之前創建的 SAP HANA 快照。

要運行腳本，請從複製該腳本的 HDB 可執行資料夾調用它。 

保留期使用運行腳本時作為參數提交的快照數進行管理。 存儲快照涵蓋的時間量是執行期間的函數，以及腳本運行時作為參數提交的快照數的函數。 

如果保留的快照數超過腳本調用中指定為參數的快照數，則在新快照運行之前將刪除同一標籤的最舊存儲快照。 您所提供作為最後呼叫參數的數字，就是您可用來控制要保留之快照集數目的數字。 使用此數位，您還可以間接控制用於快照的磁碟空間。 


## <a name="snapshot-strategies"></a>快照集策略
針對不同類型的快照集，其頻率取決於您是否使用 HANA 大型執行個體災害復原功能。 此功能依賴儲存快照集，其可能需要有關儲存體快照集執行頻率和期間的特殊建議。 

在下面的考量和建議中，假設您「不」** 使用 HANA 大型執行個體所提供的災害復原功能。 相反地，您會使用儲存體快照集來建立備份，且您能夠提供過去 30 天的時間點復原。 鑒於快照和空間的數量限制，請考慮以下要求：

- 時間點復原的復原時間。
- 使用的空間。
- 從災害潛在復原的復原點和復原時間目標。
- 對磁碟執行的最終 HANA 完整資料庫備份。 每次對磁碟執行完整資料庫備份，或執行 **backint** 介面時，執行儲存體快照集都會失敗。 如果計畫在存儲快照上運行完整資料庫備份，請確保在此期間禁用存儲快照的執行。
- 每個卷的快照數，限制為 250。

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

如果不使用 HANA 大型實例的災害復原功能，快照週期將不太頻繁。 在這種情況下，在 12 小時或 24 小時期間對 /hana/資料和 /hana/shared 執行組合快照，其中包括 /usr/sap。 將快照保留一個月。 記錄備份卷的快照也是如此。 針對記錄備份卷執行 SAP HANA 事務記錄備份會在 5 分鐘到 15 分鐘的時間內進行。

使用 cron 來執行排程儲存體的快照集最為合適。 對所有備份和災害復原需求使用相同的腳本。 修改指令碼輸入，以符合各種要求的備份時間。 這些快照都以不同的方式在 cron 中計畫，具體取決於它們的執行時間。 它可以每小時、每 12 小時、每天或每週一次。 

下面的示例在 /etc/crontab 中顯示 cron 計畫：
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
在前面的示例中，每小時組合的快照涵蓋包含 /hana/資料和 /hana/shared/SID 的卷，其中包括 /usr/sap 的位置。 使用此類快照集以加快過去兩天內的時間點復原速度。 這些卷上還有每日快照。 因此，按每小時快照和每日快照進行四周的覆蓋，您有兩天的覆蓋範圍。 事務記錄備份卷也每天備份。 這些備份將保留四周。 

正如您在 crontab 的第三行中看到的，HANA 事務日誌的備份計畫每 5 分鐘運行一次。 運行存儲快照的不同 cron 作業的開始時間是交錯的。 這樣，快照不會在特定時間點同時運行。 

在下面的示例中，您將執行一個組合快照，該快照涵蓋了包含 /hana/資料和 /hana/shared/SID 的卷，其中包括每小時的位置/ usr/sap。 您會將這些快照集保留兩天。 事務記錄備份卷的快照在 5 分鐘的基礎上運行，並保留 4 小時。 與以前一樣，HANA 事務日誌檔的備份計畫每 5 分鐘運行一次。 

在開始交易記錄備份後，系統會延後 2 分鐘再執行交易記錄備份磁碟區的快照集。 在正常情況下，SAP HANA 事務記錄備份將在這 2 分鐘內完成。 和之前一樣，儲存體快照集每天會將包含開機 LUN 的磁碟區備份一次，並保留四週之久。

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

下圖說明瞭上一個示例的序列。 引導 LUN 被排除。

![備份與快照集之間的關聯性](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA 會定期對 /hana/log 磁碟區執行寫入作業，以將認可的變更記錄至資料庫。 SAP HANA 會定期將儲存點寫入 /hana/data 磁碟區。 如在 crontab 中指定，SAP HANA 事務記錄備份每 5 分鐘運行一次。 

您還看到，由於在 /hana/資料和 /hana/shared/SID 卷上觸發組合存儲快照，SAP HANA 快照每小時運行一次。 HANA 快照成功後，組合存儲快照將運行。 按照 crontab 中的指示，/hana/logbackup 卷上的存儲快照在 HANA 事務記錄備份後大約 2 分鐘運行一次。

> 

>[!IMPORTANT]
> 只有在快照集與 SAP HANA 交易記錄備份一起執行時，使用儲存體快照集來進行 SAP HANA 備份才有價值。 這些交易記錄備份必須要涵蓋儲存體快照集之間的時段。 

如果您已對使用者承諾執行 30 天的時間點復原，則需具備下列條件︰

- 在極端情況下，通過 /hana/資料和 /hana/shared/SID 訪問 30 天的舊存儲快照。 
- 擁有連續交易記錄備份，其涵蓋任何合併儲存體快照之間的時間。 因此，最舊的交易記錄備份磁碟區快照集必須達 30 天之久。 如果將事務記錄備份複製到位於 Azure 存儲上的另一個 NFS 共用，則情況並非如此。 在這種情況下，您可以從該 NFS 共用提取舊的交易記錄備份。

要受益于存儲快照和事務記錄備份的最終存儲複製，請更改 SAP HANA 將事務記錄備份寫入的位置。 您可以在 HANA Studio 中進行這項變更。 

儘管 SAP HANA 會自動備份完整的日誌段，但指定記錄備份間隔是確定性的。 當您使用災害復原選項時尤其如此，因為您通常希望運行具有確定性週期的記錄備份。 在以下情況下，15 分鐘設置為記錄備份間隔。

![在 SAP HANA Studio 中排定 SAP HANA 備份記錄](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

您還可以選擇比每 15 分鐘更頻繁的備份。 頻率較高的設定經常與 HANA 大型執行個體的災害復原功能搭配使用。 有些客戶會每隔 5 分鐘執行一次交易記錄備份。

如果資料庫從未進行過備份，則最後一個步驟是執行檔案型備份，以建立必須存在於備份資料目錄內的單一備份項目。 否則，SAP HANA 無法啟動指定的記錄備份。

![建立檔案型備份以建立單一備份項目](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


首次成功運行存儲快照後，請刪除步驟 6 中運行的測試快照。 有關詳細資訊，請參閱[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中"刪除測試快照 - 刪除測試存儲快照"。 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>監視磁片卷上的快照數量和大小

在特定的存放磁碟區上，您可以監視快照集的數目和這些快照集的儲存體耗用量。 `ls` 命令不會顯示快照集目錄或檔案。 Linux OS`du`命令顯示有關這些存儲快照的詳細資訊，因為它們存儲在相同的卷上。 將該命令與以下選項一起使用：

- `du –sh .snapshot`：此選項提供快照集目錄內所有快照集的總計。
- `du –sh --max-depth=1`：此選項會列出所有儲存在 **.snapshot** 資料夾中的快照集及每個快照集的大小。
- `du –hc`：此選項會提供所有快照集使用的總大小。

使用這些命令可確保拍攝和存儲的快照不會佔用卷上的所有存儲。

>[!NOTE]
>引導 LUN 的快照與前面的命令不可見。

### <a name="get-details-of-snapshots"></a>獲取快照的詳細資訊
要獲取有關快照的更多詳細資訊，請使用 腳本`azure_hana_snapshot_details`。 如果災害復原位置中有一個活動伺服器，則可以在任一位置運行此腳本。 此指令碼提供下列輸出，這些輸出會依包含快照集的每個磁碟區細分： 
   * 磁碟區中所有快照集的大小
   * 該磁碟區中的每個快照集都包含下列詳細資料： 
      - 快照集名稱 
      - 建立時間 
      - 快照集的大小
      - 快照集的頻率
      - 與該快照集相關聯的 HANA 備份識別碼 (如果相關)

有關命令和輸出的語法，請參閱[Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中的"清單快照 - azure_hana_snapshot_details"。 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>減少伺服器上的快照數

如前所述，可以減少存儲的某些快照標籤的數量。 用來起始快照之命令的最後兩個參數是一個標籤及您想要保留的快照數目。

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

在前面的示例中，快照標籤是**每日哈納**。 要保留此標籤的快照數為**28**。 在您因應磁碟空間耗用量時，可能會想要縮減所儲存的快照集數目。 例如，將快照數減少到 15 的一種簡單方法是運行最後一個參數設置為**15**的腳本：

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

如果使用此設置運行腳本，則包含新存儲快照的快照數為 15。 15 個最新的快照集會保留下來，然後會刪除 15 個較舊的快照集。

 >[!NOTE]
 > 僅當快照超過一小時時，此腳本才會減少快照數。 腳本不會刪除不到一小時的快照。 這些限制與所提供的選擇性災害復原功能有關。

如果不再希望在語法示例中維護具有每日**哈納**備份首碼的一組快照，則運行**0**作為保留編號的腳本。 然後刪除與該標籤匹配的所有快照。 刪除所有快照可能會影響 HANA 大型實例災害復原功能的功能。

若要刪除特定快照集，第二種選項是使用 `azure_hana_snapshot_delete` 指令碼。 此指令碼的用途在於使用在 HANA Studio 中找到的 HANA 備份識別碼或透過快照集名稱本身，來刪除單一快照集或一組快照集。 目前，備份 ID 僅綁定到為**hana**快照類型創建的快照。 類型**日誌**和**引導**的快照備份不執行 SAP HANA 快照，因此找不到這些快照的備份 ID。 如果您輸入快照集名稱，則指令碼會尋找不同磁碟區上符合所輸入快照集名稱的所有快照集。 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

有關該腳本的詳細資訊，請參閱[在 Azure 上的 SAP HANA 的 Microsoft 快照工具](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中"刪除快照 - azure_hana_snapshot_delete"。

將腳本運行為使用者**根**。

>[!IMPORTANT]
>如果僅在計畫刪除的快照上存在資料，則在刪除快照後，該資料將永久丟失。


## <a name="file-level-restore-from-a-storage-snapshot"></a>從儲存體快照集進行檔案層級還原

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
針對快照集類型 **hana** 和 **logs**，您可以直接在 **.snapshot** 目錄中的磁碟區上存取快照集。 每個快照都有一個子目錄。 將每個檔以快照點時的狀態從該子目錄複寫到實際的目錄結構中。 

在當前版本的腳本中，*沒有*為快照還原提供還原腳本作為自助服務。 快照還原可以在容錯移轉期間作為災害復原網站的自助服務災害復原腳本的一部分執行。 要從現有可用快照還原所需的快照，必須通過打開服務請求與 Microsoft 操作團隊聯繫。

>[!NOTE]
>單個檔案還原適用于獨立于 HANA 大型實例單元類型的引導 LUN 快照。 **.snapshot**目錄未在引導 LUN 中公開。 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>復原到最近的 HANA 快照集

在生產中斷方案中，可以從存儲快照中恢復的過程可以作為 Microsoft Azure 支援的客戶事件啟動。 如果在生產系統中刪除了資料，則這是一個高度緊迫的問題，檢索資料的唯一方法是還原生產資料庫。

若是其他情況，則時間點復原可能為低緊急性，可提前幾天規劃。 您可以使用 Azure 上的 SAP HANA 計畫此恢復，而不是升起高優先順序標誌。 例如，您可能計畫通過應用新的增強包來升級 SAP 軟體。 然後，您必須還原為代表增強套件升級前狀態的快照集。

傳送要求之前，您必須做準備。 然後，Azure 團隊的 SAP HANA 可以處理請求並提供還原的卷。 之後，您會根據快照集來還原 HANA 資料庫。

有關使用新工具集還原快照的可能性，請參閱[Azure 上的 SAP HANA 手冊恢復指南中的](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)"如何還原快照"。

要準備請求，請按照以下步驟操作。

1. 決定要還原哪一個快照。 除非另有指示，否則只會還原 hana/data 磁碟區。 

1. 關閉 HANA 執行個體。

   ![關閉 HANA 執行個體](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. 將每個 HANA 資料庫節點上的資料磁碟區卸載。 如果資料磁碟區仍掛接至作業系統，快照集還原會失敗。

   ![將每個 HANA 資料庫節點上的資料磁碟區卸載](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. 打開 Azure 支援請求，並包含有關恢復特定快照的說明：

   - 在還原期間：Azure 服務上的 SAP HANA 可能會要求您參加電話會議，以協調、驗證和確認是否還原了正確的存儲快照。 

   - 恢復後：Azure 服務上的 SAP HANA 在還原存儲快照時通知您。

1. 在還原程序完成後，重新掛接所有資料磁碟區。

   ![重新掛接所有資料磁碟區](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



另一種可能性，例如，從存儲快照恢復的 SAP HANA 資料檔案，在 Azure 上的 SAP HANA 手冊中的步驟 7[中記錄了從存儲快照](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中恢復的資料檔案。

要從快照備份進行還原，請參閱[從存儲快照在 Azure 上使用 SAP HANA 的手動恢復指南](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)。 

>[!Note]
>如果您的快照已由 Microsoft 操作還原，則無需執行步驟 7。


### <a name="recover-to-another-point-in-time"></a>復原到另一個時間點
要還原到特定時間點，請參閱[從存儲快照在 Azure 上的 SAP HANA 的手動恢復指南](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)中"將資料庫恢復到以下時間點"。 


## <a name="next-steps"></a>後續步驟
- 請參閱[災害復原原則和準備](hana-concept-preparation.md)。
