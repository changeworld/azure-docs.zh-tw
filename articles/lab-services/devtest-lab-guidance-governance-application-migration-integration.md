---
title: Azure DevTest Labs 中的應用程式遷移與整合
description: 本文提供在應用程式遷移與整合的內容中，管理 Azure DevTest Labs 基礎結構的指引。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 14641e9096fa9366334e9f7460ae55cda0e6c2e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75644881"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>治理 Azure DevTest Labs 基礎結構 - 應用程式移轉和整合
一旦建立您的開發/測試實驗室環境之後，您需要思考下列問題：

- 如何在專案小組中利用該環境？
- 如何確保您會遵循任何必要的組織原則，並保有將值新增到應用程式的靈活度？

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure Marketplace 映像與自訂映像

### <a name="question"></a>問題
何時應使用 Azure Marketplace 映像，而何時應使用我自己自訂的組織映像？

### <a name="answer"></a>Answer
除非您具有特定考量或組織需求，否則預設應該使用 Azure Marketplace。 一些常見範例包括：

- 複雜的軟體安裝，需要包含應用程式作為基礎映像的一部分。
- 安裝和設定應用程式可能需要數個小時，如此就無法有效率地使用在 Azure Marketplace 映像上新增的計算時間。
- 開發人員和測試人員需要快速存取虛擬機器，並且想要將安裝新虛擬機器的時間縮至最短。
- 所有機器都必須具備的合規性或法規條件 (例如安全性原則)。

不應輕率地考慮使用自訂映像。 它們會引進額外的複雜性，因為您現在必須管理適用於那些基礎基本映像的 VHD 檔案。 您也需要使用軟體更新來定期修補那些基本映像。 這些更新包括新的作業系統 (OS) 更新，以及軟體套件本身所需的任何更新或設定變更。

## <a name="formula-vs-custom-image"></a>公式與自訂映像

### <a name="question"></a>問題
何時應使用公式，而何時應使用自訂映像？

### <a name="answer"></a>Answer
一般而言，此案例中的決定因素是成本與重複使用。

如果您的案例是數個使用者/實驗室需要在基本映像最上方含有大量軟體的映像，則您可以藉由建立自訂映像來降低成本。 這表示映像會建立一次。 它會減少虛擬機器的安裝時間，以及因為虛擬機器在安裝期間執行而產生的成本。

不過，另一個要注意的因素是對軟體套件進行變更的頻率。 如果您執行每日組建且要求軟體位於您使用者的虛擬機器上，請考慮使用公式，而非自訂映像。

## <a name="use-custom-organizational-images"></a>使用自訂組織映像

### <a name="question"></a>問題
如何設定可輕鬆重複執行的程序，以便將自訂的組織映像帶入 DevTest Labs 環境？

### <a name="answer"></a>Answer
請參閱[這段關於映像處理站模式的影片](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) \(英文\)。 此案例是一個進階案例，而提供的指令碼只是範例指令碼。 如果不需要任何變更，則您需要管理和維護環境中所使用的指令碼。

在 Azure Pipelines 中，使用 DevTest Labs 建立自訂映像管線：

- [簡介：藉由在 Azure DevTest Labs 中設定映像處理站，在數分鐘內備妥 VM](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/) \(英文\)
- [映射 Factory –第2部分！設定 Azure Pipelines 和 Factory 實驗室來建立 Vm](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [映像處理站 - 第 3 部分：儲存自訂映像並散發至多個實驗室](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/) \(英文\)
- [影片：使用 Azure DevTest Labs 自訂映像處理站](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) \(英文\)

## <a name="patterns-to-set-up-network-configuration"></a>設定網路設定的模式

### <a name="question"></a>問題
如何確保開發和測試虛擬機器無法連線到公用網際網路？ 是否有任何可用來設定網路設定的建議模式？

### <a name="answer"></a>Answer
是。 有兩個需要考慮的層面：輸入和輸出流量。

**輸入流量**：如果虛擬機器沒有公用 IP 位址，則無法透過網際網路連線。 常見的方法是確定訂用帳戶層級原則已設定，而使得所有使用者都無法建立公用 IP 位址。

**輸出流量**：如果您想要防止虛擬機器直接連至公用網際網路，並強制流量通過公司防火牆，則您可以藉由使用強制執行的路由，透過 Express Route 或 VPN 來路由傳送內部部署的流量。

> [!NOTE]
> 如果您的 Proxy 伺服器會封鎖不具 Proxy 設定的流量，請務必將例外狀況新增到實驗室的成品儲存體帳戶。

您也可以針對虛擬機器或子網路使用網路安全性群組。 這個步驟會新增額外的保護層來允許 / 封鎖流量。

## <a name="new-vs-existing-virtual-network"></a>新的與現有的虛擬網路

### <a name="question"></a>問題
何時應該針對 DevTest Labs 環境建立新的虛擬網路，而何時該使用現有的虛擬網路？

### <a name="answer"></a>Answer
如果您的 VM 需要與現有的基礎結構互動，則應該考慮在 DevTest Labs 環境內使用現有的虛擬網路。 此外，如果您使用 ExpressRoute，您可能想要將 VNet / 子網路的數量降到最低，如此就不需分割指派來在訂用帳戶中使用的 IP 位址空間。 您也應該考慮在此處使用 VNet 對等互連模式 (中樞輪輻模型)。 儘管跨區域的對等互連是 Azure 網路功能中一個即將推出的功能，但此方式還是能夠在指定的區域內跨訂用帳戶進行 VNet / 子網路通訊。

否則，每個 DevTest Labs 環境都可能有它自己的虛擬網路。 不過，請注意每個訂用帳戶都有虛擬網路數目[限制](../azure-resource-manager/management/azure-subscription-service-limits.md)。 雖然可將此限制提升到 100，但預設數目是 50。

## <a name="shared-public-or-private-ip"></a>共用、公用或私人 IP

### <a name="question"></a>問題
何時應使用共用 IP、公用 IP 及私人 IP？

### <a name="answer"></a>Answer
如果您使用站對站 VPN 或 Express Route，請考慮使用私人 IP，如此一來，您的機器就可透過透過內部網路存取，但無法透過公用網際網路存取。

> [!NOTE]
> 實驗室擁有者可以變更這個子網路原則，以確保沒有人會不小心為其 VM 建立公用 IP 位址。 訂用帳戶擁有者應該建立訂用帳戶原則，以防止建立公用 IP。

使用共用的公用 IP 時，實驗室中的虛擬機器會共用公用 IP 位址。 當您需要避免違反指定訂用帳戶的公用 IP 位址限制時，這個方法相當有幫助。

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>每個使用者或實驗室的虛擬機器數目限制

### <a name="question"></a>問題
是否有我應該為每位使用者或每個實驗室設定多少部虛擬機器的規則？

### <a name="answer"></a>Answer
考慮每位使用者或每個實驗室的虛擬機器數目時，有三個主要考量：

- 小組可在實驗室中花費於資源上的**整體成本**。 很容易就能將許多機器向上微調。 若要控制成本，有一種機制是限制每位使用者和/或每個實驗室的 VM 數目
- 實驗室中的虛擬機器總數會受到可用的[訂用帳戶層級配額](../azure-resource-manager/management/azure-subscription-service-limits.md)所影響。 其中一個上限是每個訂用帳戶 800 個資源群組。 DevTest Labs 目前會針對每部 VM 建立一個新的資源群組 (除非使用共用的公用 IP)。 如果訂用帳戶中有10個實驗室，實驗室可以在每個實驗室中容納大約79部虛擬機器（800上限為10個實驗室的資源群組） = 每個實驗室79個虛擬機器。
- 舉例來說，如果實驗室會透過 Express Route 連線到內部部署，則會針對 VNet/子網路**定義可用的 IP 位址空間**。 若要確保實驗室中的 VM 不會建立失敗 (錯誤：無法取得 IP 位址)，實驗室擁有者可以針對每個已配置可用 IP 位址空間的實驗室指定 VM 的最大數目。

## <a name="use-resource-manager-templates"></a>使用 Resource Manager 範本

### <a name="question"></a>問題
如何在 DevTest Labs 環境中使用 Resource Manager 範本？

### <a name="answer"></a>Answer
您可以使用 [DevTest labs 中的環境功能](devtest-lab-test-env.md)文章中所述的步驟，來將 Resource Manager 範本部署到 DevTest Labs 環境。 基本上，您會將 Resource Manager 範本簽入到 Git 存放庫 (Azure Repos 或 GitHub)，並將[適用於範本的私人存放庫](devtest-lab-test-env.md)新增到實驗室。

如果您使用 DevTest Labs 來裝載開發機器，此案例可能就沒有幫助，但若您要建立代表生產環境的預備環境，則可能會很有用。

另外值得注意的是，適用於每個實驗室或每位使用者選項的虛擬機器數目只會限制在實驗室本身原生建立的機器數目，而不會限制透過任何環境 (Resource Manager 範本) 所建立的機器數目。

## <a name="next-steps"></a>後續步驟
請參閱[在 DevTest Labs 中使用環境](devtest-lab-test-env.md)。