---
title: Azure DevTest Labs 基礎結構的治理-資源
description: 本文說明組織內 Azure DevTest Labs 資源的對齊與管理。
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1e470da5cd317d49f0d0734caa11eed6630d3f32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85480910"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>治理 Azure DevTest Labs 基礎結構 - 資源
此文章將說明如何在組織內調整與管理適用於 DevTest Labs 的資源。 

## <a name="align-within-an-azure-subscription"></a>在 Azure 訂用帳戶內進行調整 

### <a name="question"></a>問題
如何在 Azure 訂用帳戶內調整 DevTest Labs 資源？

### <a name="answer"></a>回答
在組織開始使用 Azure 進行一般應用程式開發之前，IT 規劃人員應該先檢閱如何引進功能以作為其整體服務組合的一部分。 檢閱的領域應該能夠消除下列顧慮：

- 如何測量與應用程式開發生命週期相關聯的成本？
- 組織如何使用公司安全性原則來調整所提議的服務供應項目？ 
- 是否需要進行分割以便將開發與生產環境分隔開來？ 
- 需要基於長期簡化管理、穩定性和成長因素來引進哪些控制項？

**第一個建議的做法**是檢閱組織的 Azure 分類法，其概述如何在生產與開發訂用帳戶之間進行劃分。 在下圖中，建議的分類法允許進行開發/測試和生產環境的邏輯分隔。 使用此方法，組織就能導入計費代碼，個別追蹤與每個環境相關聯的成本。 如需詳細資訊，請參閱[ 規定的訂用帳戶治理](/azure/architecture/cloud-adoption/appendix/azure-scaffold)。 此外，您可以基於追蹤與計費目的，使用 [Azure 標記](../azure-resource-manager/management/tag-resources.md)來組織資源。

**第二個建議的做法**是在 Azure 企業版入口網站內啟用 DevTest 訂用帳戶。 它讓組織能夠執行通常不會在 Azure 企業版訂用帳戶中提供的用戶端作業系統。 然後，使用您只需支付計算費用的企業軟體，而不必擔心授權。 它可確保對於指定服務 (包括像是 Microsoft SQL Server 之 IaaS 中的資源庫映像) 的計費只會以使用量為基礎。 有關 Azure DevTest 訂用帳戶的詳細資料，可在[此處](https://azure.microsoft.com/offers/ms-azr-0148p/)找到適用於 Enterprise 合約 (EA) 客戶的資料，並在[這裡](https://azure.microsoft.com/offers/ms-azr-0023p/)找到適用於隨用隨付客戶的資料。

![透過訂用帳戶進行資源調整](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

此模型可為組織提供彈性，以大規模部署 Azure DevTest Labs。 組織可以使用 100 到 1000 部以平行方式執行的虛擬機器，來支援各個業務單位的數百個實驗室。 它會推動集中式企業實驗室解決方案的概念，其可共用組態管理和安全性控制項的相同準則。

此模型也可確保組織不會耗盡與其 Azure 訂用帳戶相關聯的資源限制。 如需訂用帳戶與服務限制的詳細資料，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md)。 DevTest Labs 佈建程序可能會取用大量資源群組。 您可以透過 Azure DevTest 訂用帳戶中的支援要求來要求提高限制。 生產環境訂用帳戶內的資源不會因為使用中的開發訂用帳戶增加而受到影響。 如需調整 DevTest Labs 的詳細資訊，請參閱[在 DevTest Labs 中調整配額和限制](devtest-lab-scale-lab.md)。

需要考量的常見訂用帳戶層級限制是如何配置網路 IP 範圍指派，以同時支援生產環境和開發訂用帳戶。 這些指派應考量一段時間內的成長 (假設內部部署連線能力，或需要企業管理其網路堆疊而不是預設為 Azure 實作的另一個網路拓撲)。 建議的做法是具備少數虛擬網路，其中已指派大型 IP 位址前置詞，並已使用許多大型子網路來劃分，而不是具備多個含有小型子網路的虛擬網路。 例如，使用 10 個訂用帳戶，您可以定義 10 個虛擬網路 (針對每個訂用帳戶定義一個)。 所有不需隔離的實驗室均可在該訂用帳戶的 VNet 上共用相同的子網路。

## <a name="maintain-naming-conventions"></a>維護命名慣例

### <a name="question"></a>問題
如何在 DevTest Labs 環境中維護命名慣例？

### <a name="answer"></a>回答
您可能想要將目前的企業命名慣例擴充到 Azure 作業，並使其在整個 DevTest Labs 環境中保持一致。

部署 DevTest Labs 時，我們建議您具備特定的起始原則。 您可以透過中央指令碼和 JSON 範本來部署這些原則，以強制保持一致性。 命名原則可透過在訂用帳戶層級套用的 Azure 原則來實作。 如需適用於 Azure 原則的 JSON 範例，請參閱 [Azure 原則範例](../governance/policy/samples/index.md)。

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>每個實驗室的使用者數目及每個組織的實驗室數目

### <a name="question"></a>問題 
如何判斷每個實驗室的使用者比例，以及整個組織中所需的實驗室總數？

### <a name="answer"></a>回答
我們建議將與相同開發專案相關聯的業務單位和開發群組關聯至同一個實驗室。 這樣就能將相同類型的原則、映像及關機原則套用到這兩個群組。 

您可能也需要考慮地理界限。 例如，位於美國東部 (US) 的開發人員可能會使用佈建於美國東部 2 的實驗室。 此外，可能會將位於德克薩斯州達拉斯和科羅拉多州丹佛的開發人員重新導向來使用位於美國中南部的資源。 如果要與外部協力廠商共同合作，則可能會將他們指派到不是由內部開發人員所使用的實驗室。 

您還可以將實驗室用於 Azure DevOps 專案內的特定專案。 然後，透過指定的 Azure Active Directory 群組套用安全性，以允許您存取這兩組資源。 指派到實驗室的虛擬網路可以是另一個要合併使用者的界限。

## <a name="deletion-of-resources"></a>刪除資源

### <a name="question"></a>問題
如何防止刪除實驗室內的資源？

### <a name="answer"></a>回答
我們建議您在實驗室層級設定適當的權限，這樣一來，就只有授權的使用者可以刪除資源或變更實驗室原則。 開發人員應該放置於 **DevTest Labs 使用者**群組內。 首席開發人員或基礎結構負責人都應該是 **DevTest Labs 擁有者**。 我們建議您只有兩位實驗室擁有者。 此原則會擴充到程式碼存放庫以避免損毀。 實驗室使用者有權使用資源，但無法更新實驗室原則。 請參閱下列文章，其中列出每個內建群組在實驗室中擁有的角色和權限：[在 Azure DevTest Labs 中新增擁有者和使用者](devtest-lab-add-devtest-user.md)。

## <a name="move-lab-to-another-resource-group"></a>將實驗室移至另一個資源群組 

### <a name="question"></a>問題
它支援將實驗室移到另一個資源群組嗎？

### <a name="answer"></a>回答
是。 從您的實驗室首頁瀏覽至 [資源群組] 頁面。 接著，選取工具列上的 [移動]****，然後選取您想要移至不同資源群組的實驗室。 當您建立實驗室時，即會自動為您建立資源群組。 不過，您可能想要將該實驗室移至不同的資源群組，以遵循企業命名慣例。 

## <a name="next-steps"></a>後續步驟
請參閱[管理成本與擁有權](devtest-lab-guidance-governance-cost-ownership.md)。
