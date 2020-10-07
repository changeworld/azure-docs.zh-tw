---
title: Azure 機密運算概觀
description: Azure 機密運算 (ACC) 概觀
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 16f45c39a329998f4b4da4ea89315683a0fab790
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90967586"
---
# <a name="confidential-computing-on-azure"></a>Azure 上的機密運算

Azure 機密運算可讓您在雲端處理敏感性資料時加以隔離。 許多產業都使用機密運算來保護其資料，使用機密運算來：

- 保護財務資料
- 保護專利資訊
- 對機密資訊執行機器學習程序
- 對來自多個來源的加密資料集執行演算法


## <a name="overview"></a>概觀
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

我們知道保護您的雲端資料至關重要。 我們聽到您的心聲了。 以下是我們的客戶將敏感性工作負載移到雲端時可能會有的一些問題： 

- 如何確定 Microsoft 無法存取未加密的資料？
- 如何防止公司內部具有特殊權限的系統管理員造成安全性威脅？
- 還有哪些方法可防止第三方存取敏感性客戶資料？

Microsoft Azure 可協助您將受攻擊面最小化，以獲得更強大的資料保護。 Azure 已提供許多工具，以透過用戶端加密和伺服器端加密等模型來保護[**待用資料**](../security/fundamentals/encryption-atrest.md)。 此外，Azure 還提供一些機制，以透過 TLS 和 HTTPS 等安全通訊協定來加密[**傳輸中的資料**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit)。 此頁面引進第三階段的資料加密 - 加密**使用中的資料**。

## <a name="introduction-to-confidential-computing"></a>機密運算簡介  

機密運算是由[機密運算聯盟](https://confidentialcomputing.io/) (CCC) 所定義的業界術語，這是專門用來定義和加速採用機密運算的基金會。 CCC 會將機密運算定義為：藉由在硬體型的受信任執行環境 (TEE) 中執行運算以保護使用中的資料。

TEE 是一種環境，其強制只能執行已獲授權的程式碼。 TEE 以外的任何程式碼，都無法讀取或竄改該環境中的任何資料。 

### <a name="lessen-the-need-for-trust"></a>減少信任的需求
在雲端上執行工作負載需要信任。 您會將此信任授與不同的提供者，以啟用應用程式的不同元件。


**應用程式軟體廠商**：在內部部署、使用開放原始碼或藉由建置內部應用程式軟體，以信任軟體。

**硬體廠商**：使用內部部署硬體或內部硬體來信任硬體。 

**基礎結構提供者**：信任雲端提供者或管理您自己的內部部署資料中心。


Azure 機密運算藉由減少計算雲端基礎結構各方面的信任需求，讓您更輕鬆地信任雲端提供者。 Azure 機密運算可將對於主機 OS 核心、Hypervisor、VM 系統管理員和主機管理員的信任降至最低。

### <a name="reducing-the-attack-surface"></a>減少受攻擊面
信賴運算基礎 (TCB) 指的是可提供安全環境的系統所有硬體、韌體和軟體元件。 TCB 內的元件會被視為「重要」。 如果 TCB 內的某個元件遭到入侵，則整個系統的安全性可能會受到危害。 

較低的 TCB 表示較高的安全性。 暴露於各種弱點、惡意程式碼、攻擊和惡意人員的風險較低。 Azure 機密運算的目標是要藉由提供 TEE 來降低雲端工作負載的 TCB。 TEE 將您的 TCB 縮減為信任的執行階段二進位檔、程式碼和程式庫。 當您使用 Azure 基礎結構和服務進行機密運算時，您可以從 TCB 中移除所有的 Microsoft。


## <a name="using-azure-for-cloud-based-confidential-computing"></a>將 Azure 使用於雲端式機密運算<a id="cc-on-azure"></a>

Azure 機密運算可讓您在虛擬化環境中運用機密運算功能。 您現在可以使用工具、軟體和雲端基礎結構，在安全的硬體之上進行建置。  

**防止未經授權的存取**：在雲端中執行敏感性資料。 信任 Azure 能提供可行的最佳資料保護，幾乎不需要變更現今的作業。

**法規遵循**：遷移至雲端並保有資料的完全控制權，以符合保護個人資訊及保護組織 IP 的政府法規。

**保護不受信任的共同作業**：藉由合併組織間 (甚至是競爭者) 的資料以便解鎖廣泛的資料分析和更深入的深入解析，來解決整個產業的工作規模問題。

**隔離處理**：提供全新產品，卸除盲目處理私人資料的責任。 服務提供者甚至無法取出使用者資料。 

## <a name="get-started"></a>開始使用
### <a name="azure-compute"></a>Azure 計算
在 Azure 中的機密運算 IaaS 供應項目上建置應用程式。
- 虛擬機器 (VM)：[DCsv2 系列](confidential-computing-enclaves.md)
- Azure Kubernetes (AKS)：[協調機密容器](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Azure 安全性 
透過驗證方法和硬體繫結金鑰管理，確保您的工作負載安全。 
- 證明：[Microsoft Azure 證明 (預覽)](https://docs.microsoft.com/azure/attestation/overview)
- 金鑰管理：受控 HSM (預覽)

### <a name="develop"></a>開發
開始使用開發記憶體保護區感知應用程式，並使用機密推斷架構部署機密演算法。
- 撰寫要在 DCsv2 VM 上執行的應用程式：[開啟記憶體保護區 SDK](https://github.com/openenclave/openenclave)
- ONNX 執行階段中的機密 ML 模型：[機密推斷搶鮮版 (Beta)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>後續步驟

部署 DCsv2 系列虛擬機器並在其上安裝 OE SDK。

> [!div class="nextstepaction"]
> [在 Azure Marketplace 中部署機密運算 VM](quick-create-marketplace.md)
