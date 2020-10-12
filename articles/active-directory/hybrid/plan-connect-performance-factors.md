---
title: 影響 Azure AD Connect 效能的因素
description: 本文件說明各種因素如何影響 Azure AD Connect 的佈建引擎。 這些因素有助於組織規劃其 Azure AD Connect 部署，確保部署符合其同步處理需求。
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15bcb0f7ca30c343072da396abeac8d08dee03a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087004"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>影響 Azure AD Connect 效能的因素

Azure AD Connect 會將 Active Directory 同步處理至 Azure AD。 此伺服器是在將使用者身分識別移往雲端時的重要元件。 影響 Azure AD Connect 效能的主要因素如下：

| **設計因素**| **[定義]** |
|:-|-|
| 拓撲| Azure AD Connect 必須在網路上管理的端點和元件發佈。 |
| 調整| 要由 Azure AD Connect 管理的使用者、群組和 OU 等物件的數目。 |
| 硬體| Azure AD Connect 的硬體 (實體或虛擬) 以及每個硬體元件 (包括 CPU、記憶體、網路和硬碟組態) 的相依效能容量。 |
| 組態| Azure AD Connect 處理目錄和資訊的方式。 |
| 載入| 物件的變更頻率。 一小時、一天或一週期間的負載可能各不相同。 視元件而定，您可能必須針對尖峰負載或平均負載進行設計。 |

本文件的目的是要說明影響 Azure AD Connect 佈建引擎效能的因素。 大型或複雜的組織 (佈建超過 100,000 個物件的組織) 如果遇到本文件所述的任何效能問題，可使用文件中的建議來獲得最佳的 Azure AD Connect 實作。 Azure AD Connect 的其他元件 (例如 [Azure AD Connect Health](how-to-connect-health-agent-install.md)) 和代理程式不在本文件的討論範圍。

> [!IMPORTANT]
> Microsoft 不支援在正式記載的動作以外修改和操作 Azure AD Connect。 任何這些動作可能會導致 Azure AD Connect 同步的狀態不一致或不受支援。因此，Microsoft 無法針對這類部署提供技術支援。

## <a name="azure-ad-connect-component-factors"></a>Azure AD Connect 元件因素

下圖顯示的雖是連線到單一樹系的佈建引擎高階架構，但該引擎也支援多個樹系。 此架構會顯示各種元件彼此間的互動方式。

![圖表顯示連接的目錄和 Azure AD Connect 布建引擎如何互動，包括 SQL Database 中的連接器空間和元程式元件。 ](media/plan-connect-performance-factors/AzureADConnentInternal.png)

佈建引擎會連線至每個 Active Directory 樹系和 Azure AD。 從每個目錄中讀取資訊的程序稱為匯入。 匯出則是指更新來自佈建引擎的目錄。 同步處理程序會評估物件在佈建引擎內流動方式的規則。 若要深入了解，您可以參閱 [Azure AD Connect 同步處理：了解架構](./concept-azure-ad-connect-sync-architecture.md)。

Azure AD Connect 會使用下列臨時區域、規則和程序，來允許從 Active Directory 同步處理至 Azure AD：

* **連接器空間 (CS)** - 來自每個連線目錄 (CD) 的物件 (實際目錄) 會先暫存在此，然後再供佈建引擎處理。 Azure AD 有它自己的 CS，且您所連線的每個樹系也會有它自己的 CS。
* **Metaverse (MV)** - 需要同步處理的物件會根據同步處理規則在此建立。 物件必須先存在於 MV 中，才能將物件和屬性填入其他連線目錄。 MV 只有一個。
* **同步處理規則** - 規則可決定要建立 (投影) 哪些物件或將其連線 (聯結) 到 MV 中的物件。 同步處理規則也會決定要在目錄中來回複製或轉換哪些屬性值。
* **執行設定檔** - 根據臨時區域和連線目錄之間的同步處理規則，將複製物件和其屬性值的程序步驟組合在一起。

會有不同的執行設定檔可供將佈建引擎的效能最佳化。 大部分組織會使用預設排程和執行設定檔來進行一般作業，但某些組織可能必須[變更排程](./how-to-connect-sync-feature-scheduler.md)或觸發其他執行設定檔，以因應不常見的情況。 可用的執行設定檔如下：

### <a name="initial-sync-profile"></a>初始同步處理設定檔

初始同步處理設定檔是首次讀取連線目錄 (例如 Active Directory 樹系) 的程序。 然後，它會分析同步處理引擎資料庫中的所有項目。 初始週期會在 Azure AD 中建立新的物件，如果 Active Directory 樹系很大，則需要更多時間才能完成。 初始同步處理包含下列步驟：

1. 在所有連接器上執行完整匯入
2. 在所有連接器上執行完整同步處理
3. 在所有連接器上執行匯出

### <a name="delta-sync-profile"></a>差異同步處理設定檔

為了獲得最佳同步處理程序，此執行設定檔只會處理連線目錄物件自上次同步處理程序進行後所做的變更 (建立、刪除和更新)。 根據預設，差異同步處理設定檔會每 30 分鐘執行一次。 組織應設法讓執行時間保持在 30 分鐘內，以確保 Azure AD 處於最新狀態。 若要監視 Azure AD Connect 的健康情況，請使用[健康情況監視代理程式](how-to-connect-health-sync.md)來查看程序的任何問題。 差異同步處理設定檔包含下列步驟：

1. 在所有連接器上執行差異匯入
2. 在所有連接器上執行差異同步處理
3. 在所有連接器上執行匯出

典型企業組織的差異同步處理案例為：

- 刪除的物件 ~1%
- 建立的物件 ~1%
- 修改的物件 ~5%

變動率可能會隨組織更新 Active Directory 使用者的頻率而有所不同。 例如，聘僱和縮減勞動力的季節效應會發生較高的變動率。

### <a name="full-sync-profile"></a>完整同步處理設定檔

如果您已進行下列任何一項組態變更，就必須進行完整同步處理週期：



- 增加了要從連線目錄匯入的物件或屬性範圍。 例如，當您將網域或 OU 新增至匯入範圍時。
- 對同步處理規則進行了變更。 例如，當您建立新的規則，以將 Active Directory 中的 extension_attribute3 填入 Azure AD 中的使用者職稱時。 此更新便需要佈建引擎重新檢查所有現有的使用者，更新其職稱以套用往後的變更。

完整同步處理週期包含下列作業：

1. 在所有連接器上執行完整匯入
2. 在所有連接器上執行完整/差異同步處理
3. 在所有連接器上執行匯出

> [!NOTE]
> 對 Active Directory 或 Azure AD 中的許多物件進行大量更新時，務必要做好仔細規劃。 因為許多物件有所變更，所以大量更新會導致差異同步處理程序在匯入時花費更多的時間。 即使大量更新未影響同步處理程序，仍可能會拉長匯入時間。 例如，將授權指派給 Azure AD 中的許多使用者會造成從 Azure AD 進行匯入的週期拉長，但不會導致 Active Directory 中的任何屬性有所變更。

### <a name="synchronization"></a>同步處理

同步處理程序執行階段具有下列效能特性：

* 同步處理都是單一執行緒，這表示佈建引擎不會平行處理連線目錄、物件或屬性的執行設定檔。
* 匯入時間會隨著所要同步處理的物件數目以線性方式增加。 例如，如果 10,000 個物件需要 10 分鐘才能匯入，則在相同伺服器上，20,000 個物件需要大約 20 分鐘的時間。
* 匯出也是線性的。
* 根據參考其他物件的物件數目，同步處理會以指數方式增加。 群組成員資格和巢狀群組會有主要的效能影響，因為其成員會參考使用者物件或其他群組。 必須找到這些參考，且必須參考到 MV 中的實際物件，才能完成同步處理週期。

### <a name="filtering"></a>篩選

您想要匯入的 Active Directory 拓撲大小，是會影響效能和佈建引擎內部元件所需整體時間的首要因素。

請使用[篩選](./how-to-connect-sync-configure-filtering.md)來減少要同步處理的物件。 它可避免不必要的物件進行處理並匯出至 Azure AD。 依據喜好順序，可用的篩選技術如下：



- **網域型篩選** - 使用此選項可選取要同步處理至 Azure AD 的特定網域。 當您在安裝 Azure AD Connect 同步處理之後對內部部署基礎結構進行變更，就必須從同步處理引擎組態新增和移除網域。
- **組織單位 (OU) 篩選** - 使用 OU 來鎖定要佈建至 Azure AD 的 Active Directory 網域特定物件。 OU 篩選是第二個建議篩選機制，因為它會使用簡單的 LDAP 範圍查詢，來從 Active Directory 匯入較小一部分的物件。
- **每個物件的屬性篩選** - 使用物件上的屬性值，來決定是否要將 Active Directory 中的特定物件佈建到 Azure AD。 當網域和 OU 篩選不符合特定篩選需求時，就非常適合使用屬性篩選來微調篩選。 屬性篩選不會減少匯入時間，但可以減少同步處理和匯出時間。
- **群組型篩選** - 使用群組成員資格來決定是否應該將物件佈建到 Azure AD。 群組型篩選只適用於測試情況，不建議用於生產環境，因為其需要額外的負擔以在同步處理期間檢查群組成員資格。

Active Directory CS 中有許多持續的[中斷器物件](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects)會導致同步處理時間拉長，因為佈建引擎必須重新評估每個中斷器物件是否可以在同步處理週期內連線。 若要解決此問題，請考慮下列其中一項建議：



- 使用網域或 OU 篩選將中斷器物件放到匯入範圍外。
- 將物件投影/聯結至 MV，並將 [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) 屬性設為 True，以避免將這些物件佈建到 Azure AD CS。

> [!NOTE]
> 篩選太多物件時，可能會讓使用者混淆或發生應用程式權限問題。 例如，在混合式 Exchange Online 實作中，具有內部部署信箱的使用者在其全域通訊清單中所看到的使用者，會比具有 Exchange Online 信箱的使用者所看到的人數還多。 而在其他情況下，某位使用者可能會想將雲端應用程式的存取權，授與給另一位不屬於一組所篩選物件範圍的使用者。

### <a name="attribute-flows"></a>屬性流程

屬性流程是將物件的屬性值從一個連線目錄複製或轉換到另一個連線目錄的程序。 屬性流程會定義為同步處理規則的一部分。 例如，當 Active Directory 中某位使用者的電話號碼變更時，Azure AD 中的電話號碼也會更新。 組織可以[修改屬性流程](./how-to-connect-sync-change-the-configuration.md)以符合各種需求。 建議您先複製現有屬性流程，再加以變更。

簡單的重新導向 (例如，讓屬性值流向不同的屬性) 不會對效能有太大影響。 重新導向的其中一個範例是將 Active Directory 中的行動電話號碼，流向 Azure AD 中的辦公室電話號碼。

轉換屬性值會影響同步處理程序的效能。 轉換屬性值的作業包括修改、重新格式化、串連或減去屬性值。

組織可以防止某些屬性流向 Azure AD，但它不會影響佈建引擎的效能。

> [!NOTE]
> 請勿刪除同步處理規則中不必要的屬性流程。 建議您不如將其停用，因為所刪除的規則會在 Azure AD Connect 升級期間予以重建。

## <a name="azure-ad-connect-dependency-factors"></a>Azure AD Connect 相依性因素

Azure AD Connect 的效能取決於其匯入和匯出時的目標連線目錄效能。 例如，其需要匯入的 Active Directory 大小或 Azure AD 服務的網路延遲。 提供引擎使用的 SQL database 也會影響同步處理週期的整體效能。

### <a name="active-directory-factors"></a>Active Directory 因素

如先前所述，所要匯入的物件數目會大幅影響效能。 [Azure AD Connect 的硬體與先決條件](how-to-connect-install-prerequisites.md)會概述根據部署大小所需的特定硬體層級。 Azure AD Connect 只支援特定拓撲，如 [Azure AD Connect 的拓撲](plan-connect-topologies.md)所述。 對於不受支援的拓撲，我們沒有任何效能最佳化方法及建議。

請根據您想要匯入的 Active Directory 大小，確定 Azure AD Connect 伺服器符合其硬體需求。 Azure AD Connect 伺服器與 Active Directory 網域控制站之間的網路連線若不佳或緩慢，將會拖慢匯入速度。

### <a name="azure-ad-factors"></a>Azure AD 因素

Azure AD 會使用節流功能來防止雲端服務遭受拒絕服務 (DoS) 的攻擊。 Azure AD 目前的節流限制為每 5 分鐘 7,000 次寫入 (每小時 84,000 次)。 例如，下列作業可以進行節流：



- Azure AD Connect 匯出至 Azure AD。
- PowerShell 指令碼或應用程式直接更新 Azure AD (即使在背景中)，例如動態群組成員資格。
- 使用者更新自己的身分識別記錄，例如註冊 MFA 或 SSPR (自助式密碼重設)。
- 在圖形化使用者介面中進行的作業。

請規劃部署和維護工作，以確定 Azure AD Connect 同步處理週期不會受到節流限制所影響。 例如，如果您一次招聘大量人員，從而建立了數以千計的使用者身分識別，則會導致動態群組成員資格、授權指派和自助式密碼重設註冊也跟著更新。 最好的辦法是將這些寫入作業分成幾小時或幾天的時間來進行。

### <a name="sql-database-factors"></a>SQL 資料庫因素

來源 Active Directory 拓撲的大小會影響 SQL 資料庫的效能。 請遵循 SQL 伺服器資料庫的[硬體需求](how-to-connect-install-prerequisites.md)，並考慮下列建議：



- 使用者人數超過 100,000 的組織可以在相同的伺服器上共置 SQL 資料庫和佈建引擎，從而減少網路延遲。
- 因為同步處理程序需要較高的磁碟輸入和輸出 (I/O)，請對佈建引擎的 SQL 資料庫使用固態硬碟 (SSD)，以獲得最佳結果，如果無法做到，請考慮使用 RAID 0 或 RAID 1 組態。
- 請勿進行完整同步處理事先;它會造成不必要的變換和較慢的回應時間。

## <a name="conclusion"></a>結論

若要獲得最佳的 Azure AD Connect 實作效能，請考慮以下建議：



- 根據 Azure AD Connect 伺服器的實作大小，使用[建議的硬體組態](how-to-connect-install-prerequisites.md)。
- 在大規模部署中升級 Azure AD Connect 時，請考慮使用[變換移轉方法](./how-to-upgrade-previous-version.md#swing-migration)，以確保停機時間可以最短，並獲得最佳可靠性。 
- 對 SQL 資料庫使用 SSD 來獲得最佳寫入效能。
- 使用網域、OU 或屬性篩選，將 Active Directory 範圍篩選成只包含需要佈建到 Azure AD 的物件。
- 如果您必須變更預設的屬性流程規則，請先複製規則，然後變更複本並停用原始規則。 請記得要重新執行完整同步處理。
- 規劃足夠的時間來進行初始的完整同步處理執行設定檔。
- 設法在 30 分鐘內完成差異同步處理週期。 如果差異同步處理設定檔未在 30 分鐘內完成，請修改預設的同步處理頻率，以便包含完整的差異同步處理週期。
- 在 Azure AD 中監視 [Azure AD Connect 同步處理健康情況](how-to-connect-health-agent-install.md)。

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](whatis-hybrid-identity.md)。