---
title: 將 Windows 安全性事件資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Windows 安全性事件資料連線到 Azure Sentinel。
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
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 30055ba1befc68d015e3e3162d8db11a2916f3d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80124974"
---
# <a name="connect-windows-security-events"></a>連線 Windows 安全性事件 

安全性事件連接器可讓您將 Windows 系統（伺服器和工作站、實體和虛擬）的所有安全性事件，串流至您的 Azure Sentinel 工作區。 這可讓您在儀表板中查看 Windows 安全性事件，以用於建立自訂警示，並依賴它們來改善您的調查，讓您更深入瞭解組織的網路並擴充您的安全性作業功能。 您可以從下列集合中選取要串流的事件：<a name="event-sets"></a>

- **所有事件**-所有 Windows 安全性和 AppLocker 事件。
- **一般**-用於進行審核的一組標準事件。 此集合中包含完整的使用者 audit 記錄。 例如，它同時包含使用者登入和使用者登出事件（事件識別碼4624、4634）。 另外還有一些審核動作，例如安全性群組變更、金鑰網域控制站 Kerberos 作業，以及其他類型的事件，以及已接受的最佳作法。

    **一般**事件集可能包含一些不常見的事件種類。  這是因為**一般**設定的重點是將事件的數量減少到更容易管理的層級，同時仍維持完整的 audit 追蹤功能。

- **最少**-可能表示潛在威脅的一小部分事件。 此集合未包含完整的 audit 記錄。 它只涵蓋可能表示成功缺口的事件，以及有極低發生率的其他重要事件。 例如，它包含成功和失敗的使用者登入（事件識別碼4624、4625），但它不包含登出資訊（4634），而在進行檢查時，對入侵偵測沒有意義，而且具有相當高的磁片區。 此集合的大部分資料量都是由登入事件和進程建立事件（事件識別碼4688）所組成。

- **無**-沒有安全性或 AppLocker 事件。 （這是用來停用連接器的設定）。

    下列清單提供每個集合的安全性和應用程式保險箱事件識別碼的完整細目：

    | 事件集 | 收集的事件識別碼 |
    | --- | --- |
    | **最小** | 1102，4624，4625，4657，4663，4688，4700，4702，4719，4720，4722，4723，4724，4727，4728，4732，4735，4737，4739，4740，4754，4755，4756，4767，4799，4825，4946，4948，4956，5024，5033，8001，8002，8003，8004，8005 |
    | **通用** | 1，299，300，324，340，403，404，410，411，412，413，431，500，501，1100，1102，1107，1108，4608，4610，4611，4614，4622，4624，4625，4634，4647，4648，4649，4657，4661，4662，4663，4665，4666，4667，4688，4670，4672，4673，4674，4675，4689，4697，4700，4702，4704，4705，4716，4717，4718，4719，4720，4722，4723，4724，4725，4726，4733，4732，4735，4737，4738，4739，4740，4742，4744，4745，4746，4750，4751，4752，4754，4755，4756，4757，4760，4761，4762，4764，4767，4768，4771，4774，4778，4779，4781，4793，4797，4798，4799，4800，4801，4802，4803，4825，4826，4870，4886，4887，4888，4893，4898，4902，4904，4905，4907，4931，4932，4933，4946，4948，4956，4985，5024，5033，5140，5145，5632，6144，6145，6272，6273，6278，6416，6423，6424，8001，8002，8003，8004，8005，8006，8007，8222，26401，30004 |

> [!NOTE]
> 單一工作區內容中的安全性事件集合可以從 Azure 資訊安全中心或 Azure Sentinel 進行設定，但不能兩者同時進行。 如果您要在已執行 Azure 資訊安全中心的工作區中上架 Azure Sentinel，而且設定為收集安全性事件，您有兩個選項：
> - 保持 Azure 資訊安全中心中的 [安全性事件] 集合。 您將能夠在 Azure Sentinel 以及 Azure 資訊安全中心中查詢和分析這些事件。 但是，您將無法在 Azure Sentinel 中監視連接器的線上狀態或變更其設定。 如果這對您很重要，請考慮第二個選項。
>
> - 停用 Azure 資訊安全中心中的[安全性事件集合](../security-center/security-center-enable-data-collection.md)，然後只在 Azure Sentinel 中新增安全性事件連接器。 如同第一個選項，您可以同時查詢和分析 Azure Sentinel 和 Azure 資訊安全中心中的事件，但您現在可以監視連接器的線上狀態，或在中變更其設定，而且只會在-Azure Sentinel 中。

## <a name="set-up-the-windows-security-events-connector"></a>設定 Windows 安全性事件連接器

若要在 Azure Sentinel 中收集您的 Windows 安全性事件：

1. 從 Azure Sentinel 導覽功能表中，選取 [**資料連線器**]。 從連接器清單中，按一下 [**安全性事件**]，然後在右下方的 [**開啟連接器頁面**] 按鈕上。 然後遵循 [**指示**] 索引標籤底下的螢幕指示，如本節其餘部分所述。

1. 請確認您具有如**必要條件**中所述的適當許可權。

1. 在您要將安全性事件串流至 Azure Sentinel 的電腦上，下載並安裝[Log Analytics 代理程式](../azure-monitor/platform/log-analytics-agent.md)（也稱為 MICROSOFT MONITORING AGENT 或 MMA）。

    針對 Azure 虛擬機器：
    
    1. 按一下 [在**Azure Windows 虛擬機器上安裝代理程式**]，然後在如下所示的連結上。
    1. 針對您想要連線的每個虛擬機器，在右側出現的清單中按一下其名稱，然後按一下 **[連線]**。

    適用于非 Azure Windows 機器（實體、虛擬內部內部部署或另一個雲端中的虛擬）：

    1. 按一下 [在**非 Azure Windows 機器上安裝代理程式**]，然後在如下所示的連結上。
    1. 在 [ **Windows 電腦**] 下，按一下右側顯示的適當下載連結。
    1. 使用下載的可執行檔，在您選擇的 Windows 系統上安裝代理程式，並使用出現在上述下載連結下方的**工作區識別碼和金鑰**進行設定。

    > [!NOTE]
    >
    > 若要允許沒有必要網際網路連線的 Windows 系統仍然將事件串流至 Azure Sentinel，請使用右下角的連結，將**OMS 閘道**下載並安裝在另一部電腦上，以作為 proxy。  您仍然需要在要收集事件的每個 Windows 系統上，安裝 Log Analytics 代理程式。
    >
    > 如需此案例的詳細資訊，請參閱[ **Log Analytics 閘道**檔](../azure-monitor/platform/gateway.md)。

    如需其他安裝選項和進一步的詳細資料，請參閱[ **Log Analytics 代理程式**檔](../azure-monitor/platform/agent-windows.md)。

1. 選取您想要串流的事件集（[全部、一般或最小](#event-sets)）。

1. 按一下 [更新]  。

1. 若要在 Log Analytics 中使用適用于 Windows 安全性事件的相關`SecurityEvent`架構，請在查詢視窗中輸入。

## <a name="validate-connectivity"></a>驗證連線能力

大約需要20分鐘的時間，記錄才會開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Windows 安全性事件連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 使用[內建](tutorial-detect-threats-built-in.md)或[自訂](tutorial-detect-threats-custom.md)規則，開始偵測 Azure Sentinel 的威脅。

