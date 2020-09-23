---
title: 將 Windows 安全性事件資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用安全性事件連接器，將 Windows 系統中的所有安全性事件串流至 Azure Sentinel 工作區。 
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: a16afcafa03ef2ab8642316db560e30a473a526b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883719"
---
# <a name="connect-windows-security-events"></a>連線 Windows 安全性事件 

安全性事件連接器可讓您將 Windows 系統中的所有安全性事件串流至您的 Azure Sentinel 工作區， (伺服器和工作站、實體和虛擬) 。 這可讓您查看儀表板中的 Windows 安全性事件，以在建立自訂警示時使用這些事件，並依賴它們來改善您的調查，讓您更深入瞭解組織的網路並擴充安全性作業功能。 您可以從下列集合中選取要串流的事件： <a name="event-sets"></a>

- **所有事件** -所有 Windows 安全性與 AppLocker 事件。
- **常見** -一組用於進行審核的標準事件。 完整的使用者審核記錄會包含在此集合中。 例如，它包含使用者的登入和使用者登出事件 (事件識別碼4624、4634) 。 另外還有一些像是安全性群組變更、金鑰網域控制站 Kerberos 作業，以及其他類型的事件，以及已接受的最佳作法等的審核動作。

    **常見**的事件集可能包含某些不常見的事件種類。  這是因為 **一般** 組的主要點是將事件的數量降到更容易管理的層級，同時仍保有完整的審核記錄功能。

- **極** 小-一組可能指出潛在威脅的小型事件。 此集合未包含完整的審核記錄。 它只涵蓋可能表示成功缺口的事件，以及其他具有極低比率的重要事件。 例如，它包含成功和失敗的使用者登入 (事件識別碼4624、4625) ，但它並不包含登出資訊 (4634) 這項功能對入侵偵測沒有意義，而且磁片區相對較高。 此集合的大部分資料量是由登入事件和進程建立事件所組成 (事件識別碼 4688) 。

- **無** -沒有安全性或 AppLocker 事件。  (此設定用來停用連接器。 ) 

    下列清單提供每個集合之安全性和應用程式保險箱事件識別碼的完整細目：

    | 事件集 | 收集的事件識別碼 |
    | --- | --- |
    | **最低限度** | 1102、4624、4625、4657、4663、4688、4700、4702、4719、4720、4722、4723、4724、4727、4728、4732、4735、4737、4739、4740、4754、4755、4756、4767、4799、4825、4946、4948、4956、5024、5033、8001、8002、8003、8004、8005、8006、8007、8222、 |
    | **通用** | 1，299，300，324，340，403，404，410，411，412，413，431，500，501，1100，1102，1107，1108，4608，4610，4611，4614，4622、4624、4625、4634、4647、4648、4649、4657、4661、4662、4663、4665、4666、4667、4688、4670、4672、4673、4674、4675、4689、4697、4700、4702、4704、4705、4716、4717、4718、4719、4720、4722、4723、4724、4725、4726、4727、4728、4729、，4733，4732，4735，4737，4738，4739，4740，4742，4744，4745，4746，4750，4751，4752，4754，4755，4756，4757，4760，4761，4762，4764，4767、4768、4771、4774、4778、4779、4781、4793、4797、4798、4799、4800、4801、4802、4803、4825、4826、4870、4886、4887、4888、4893、4898、4902、4904、4905、4907、4931、4932、4933、4946、4948、4956、4985、5024、5033、5059、5136、5137、、5140、5145、5632、6144、6145、6272、6273、6278、6416、6423、6424、8001、8002、8003、8004、8005、8006、8007、8222、26401、30004 |

> [!NOTE]
> 單一工作區內容中的安全性事件集合可以從 Azure 資訊安全中心或 Azure Sentinel 設定，但不能同時設定為兩者。 如果您在已從 Azure 資訊安全中心取得 Azure Defender 警示的工作區上架 Azure Sentinel，且設定為收集安全性事件，則您有兩個選項：
> - 將安全性事件集合保持在 Azure 資訊安全中心。 您將能夠在 Azure Sentinel 以及 Azure Defender 中查詢及分析這些事件。 不過，您將無法在 Azure Sentinel 中監視連接器的線上狀態或變更其設定。 如果這對您很重要，請考慮第二個選項。
>
> - 停用 Azure 資訊安全中心中的[安全性事件集合](../security-center/security-center-enable-data-collection.md)，然後只在 Azure Sentinel 中新增安全性事件連接器。 在第一個選項中，您可以查詢和分析 Azure Sentinel 和 Azure Defender/ASC 中的事件，但現在您將能夠監視連接器的線上狀態，或只在 Azure Sentinel 中變更其設定。

## <a name="set-up-the-windows-security-events-connector"></a>設定 Windows 安全性 Events 連接器

若要在 Azure Sentinel 中收集您的 Windows 安全性事件：

1. 從 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。 從連接器清單中，按一下 [ **安全性事件**]，然後按一下右下方的 [ **開啟連接器頁面** ] 按鈕。 然後依照本節其餘部分所述，依照 [ **指示** ] 索引標籤下的畫面上的指示進行。

1. 確認您具有適當的許可權，如 **必要條件**中所述。

1. 下載並安裝 [Log Analytics 代理程式](../azure-monitor/platform/log-analytics-agent.md) (也稱為 Microsoft Monitoring Agent 或在您要將安全性事件串流至 Azure Sentinel 的電腦上的 MMA) 。

    針對 Azure 虛擬機器：
    
    1. 按一下 [在 **Azure Windows 虛擬機器上安裝代理程式**]，然後按一下出現在下方的連結。
    1. 針對您想要連接的每部虛擬機器，在右側顯示的清單中按一下其名稱，然後按一下 [連線 **]**。

    針對非 Azure Windows 機器 (實體、虛擬內部內部部署或另一個雲端中的虛擬) ：

    1. 按一下 [在 **非 Azure Windows 電腦上安裝代理程式**]，然後按一下出現在下方的連結。
    1. 在 [ **Windows 電腦**] 下，按一下右邊出現的適當下載連結。
    1. 使用下載的可執行檔，在您選擇的 Windows 系統上安裝代理程式，並使用上述下載連結底下所顯示的 **工作區識別碼和金鑰** 來進行設定。

    > [!NOTE]
    >
    > 若要允許沒有必要網際網路連線的 Windows 系統仍將事件串流至 Azure Sentinel，請使用右下角的連結，在個別的電腦上下載並安裝 **OMS 閘道** ，以作為 proxy。  您仍然需要在您想要收集其事件的每個 Windows 系統上安裝 Log Analytics 代理程式。
    >
    > 如需此案例的詳細資訊，請參閱[ **Log Analytics 閘道**檔](../azure-monitor/platform/gateway.md)。

    如需其他安裝選項和進一步的詳細資料，請參閱[ **Log Analytics 代理程式**檔](../azure-monitor/platform/agent-windows.md)。

1. 選取您要串流 ([ [所有]、[一般] 或 [基本](#event-sets) ]) 的事件集。

1. 按一下 [更新]。

1. 若要在 Log Analytics 中使用適用于 Windows 安全性事件的相關架構，請 `SecurityEvent` 在 [查詢] 視窗中輸入。

## <a name="validate-connectivity"></a>驗證連線能力

大約需要20分鐘的時間，記錄才會出現在 Log Analytics 中。 

### <a name="configure-the-security-events-connector-for-anomalous-rdp-login-detection"></a>設定安全性事件連接器以進行異常 RDP 登入偵測

> [!IMPORTANT]
> 異常 RDP 登入偵測目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Sentinel 可以將機器學習 (ML) 套用至安全性事件資料，以識別異常遠端桌面通訊協定 (RDP) 登入活動。 案例包括：

- 不**尋常的 ip** -過去30天內，IP 位址很少或從未被觀察到

- 不**尋常的地理位置**-過去30天內很少或從未觀察到 IP 位址、城市、國家/地區和 ASN

- **新使用者** -新使用者從 IP 位址和地理位置登入，這兩者或其中一個都不會根據之前30天的資料來顯示。

**設定指示**

1. 您必須透過 **Security events** data Connector (事件識別碼 4624) 收集 RDP 登入資料。 請確定您已選取 [無] 以外的 [事件集](#event-sets) ，以串流至 Azure Sentinel。

1. 在 Azure Sentinel 入口網站中，按一下 [ **分析**]，然後按一下 [ **規則範本** ] 索引標籤。選擇 ** (Preview) 異常 RDP 登入偵測** 規則，並將 **狀態** 滑杆移至 [ **啟用**]。

    > [!NOTE]
    > 由於機器學習演算法需要30天的資料來建立使用者行為的基準設定檔，因此您必須允許在偵測到任何事件之前，先收集30天的安全性事件資料。

## <a name="next-steps"></a>下一步
在本檔中，您已瞭解如何將 Windows 安全性事件連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 使用 [內建](tutorial-detect-threats-built-in.md) 或 [自訂](tutorial-detect-threats-custom.md) 規則開始偵測 Azure Sentinel 的威脅。

