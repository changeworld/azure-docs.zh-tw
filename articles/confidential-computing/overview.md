---
title: Azure 機密運算概觀
description: Azure 機密運算 (ACC) 概觀
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 4e92f974ce7d6c03143276808c4ca4d09d607a84
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835811"
---
# <a name="confidential-computing-on-azure"></a>Azure 上的機密運算

Azure 機密運算可讓您在雲端處理敏感性資料時加以隔離。 許多產業都使用機密運算來保護其資料。 這些工作負載包括：

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


## <a name="introduction-to-confidential-computing"></a>機密運算簡介 <a id="intro to acc"></a>

機密運算是由[機密運算聯盟](https://confidentialcomputing.io/) (CCC) 所定義的業界術語，這是專門用來定義和加速採用機密運算的基金會。 CCC 對於機密運算的定義是，藉由在硬體型的受信任執行環境 (TEE) 中執行運算以保護使用中的資料。

TEE 是一種環境，其強制只能執行已獲授權的程式碼。 TEE 以外的任何程式碼，都無法讀取或竄改該環境中的任何資料。

### <a name="enclaves"></a>記憶體保護區

記憶體保護區是硬體處理器和記憶體的安全部分。 即使使用偵錯工具，也沒有辦法檢視記憶體保護區內的資料或程式碼。 如果不受信任的程式碼嘗試修改記憶體保護區記憶體中的內容，則會停用環境並拒絕作業。

開發應用程式時，您可使用[軟體工具](#oe-sdk)來防護記憶體保護區內的部分程式碼和資料。 這些工具可確保受信任環境以外的任何人都無法檢視或修改您的程式碼和資料。 

基本上，請將記憶體保護區視為一個安全箱。 您可將已加密的程式碼和資料放入這個箱子中。 從箱子外部，您看不到任何東西。 您可為記憶體保護區提供可解密資料的金鑰，然後在從記憶體保護區送出之前，再次處理和加密資料。

### <a name="attestation"></a>證明

您會想要確認和驗證您信任的環境是安全的。 此種驗證就是證明程序。 

證明可讓信賴憑證者對以下事項更有信心：(1) 其軟體是在記憶體保護區中執行，而且 (2) 記憶體保護區是最新且安全的。 例如，記憶體保護區會要求基礎硬體產生認證，其中包含記憶體保護區存在於平台上的證明。 然後可將報告提供給第二個記憶體保護區，以確認報告是在相同的平台上產生。

證明必須使用與系統軟體和晶片相容的安全證明服務來執行。 [Intel 的證明和佈建服務](https://software.intel.com/sgx/attestation-services)與 Azure 機密運算虛擬機器相容。


## <a name="using-azure-for-cloud-based-confidential-computing"></a>將 Azure 使用於雲端式機密運算<a id="cc-on-azure"></a>

Azure 機密運算可讓您在虛擬化環境中運用機密運算功能。 您現在可以使用工具、軟體和雲端基礎結構，在安全的硬體之上進行建置。 

### <a name="virtual-machines"></a>虛擬機器

Azure 是在虛擬化環境中提供機密運算的第一個雲端提供者。 我們開發了虛擬機器，作為硬體與您應用程式之間的抽象層。 您可大規模執行工作負載，並可使用備援和可用性選項。  

#### <a name="intel-sgx-enabled-virtual-machines"></a>已啟用 Intel SGX 的虛擬機器

在 Azure 機密運算虛擬機器中，CPU 硬體的一部分會保留給您應用程式中一部分的程式碼和資料使用。 此限制的部分就是記憶體保護區。 

![VM 模型](media/overview/hardware-backed-enclave.png)

Azure 機密運算基礎結構目前由虛擬機器 (VM) 的專用 SKU 組成。 這些 VM 會在具有 Software Guard Extension (Intel SGX) 的 Intel 處理器上執行。 [Intel SGX](https://intel.com/sgx) 是一種元件，可讓我們以機密運算增加保護。 

現今，Azure 提供以 Intel SGX 技術為基礎的 [DCsv2 系列](https://docs.microsoft.com/azure/virtual-machines/dcv2-series)，以便建立以硬體為基礎的記憶體保護區。 您可建置以安全記憶體保護區為基礎的應用程式，以在 DCsv2 系列的 VM 中執行，進而保護您的應用程式資料和使用中的程式碼。 

您可以[深入了解](virtual-machine-solutions.md)如何使用以硬體為基礎的受信任記憶體保護區來部署 Azure 機密運算虛擬機器。

## <a name="application-development"></a>應用程式開發 <a id="application-development"></a>

若要利用記憶體保護區和隔離環境的強大功能，您必須使用可支援機密運算的工具。 有各種工具可支援記憶體保護區應用程式開發。 例如，您可使用下列開放原始碼的架構： 

- [Open Enclave 軟體開發套件 (SDK)](https://github.com/openenclave/openenclave)
- [機密聯盟架構 (CCF)](https://github.com/Microsoft/CCF)

### <a name="overview"></a>概觀

在記憶體保護區中建置的應用程式會以兩種方式進行分割：
1. 「不受信任」的元件 (主機)
1. 「受信任」的元件 (記憶體保護區)

**主機**是記憶體保護區應用程式的執行基礎所在，而且是不受信任的環境。 主機無法存取部署在主機上的記憶體保護區程式碼。 

**記憶體保護區**是應用程式程式碼及其快取的資料/記憶體執行所在的位置。 記憶體保護區中應該會進行安全運算，以確保秘密和敏感性資料受到保護。 

在應用程式設計期間，請務必識別並判斷應用程式必須在記憶體保護區中執行的部分。 您選擇要放入受信任元件中的程式碼會與您應用程式的其餘部分隔離。 記憶體保護區完成初始化且程式碼載入至記憶體後，就無法從不受信任的元件讀取或變更該程式碼。 

### <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave 軟體開發套件 (OE SDK) <a id="oe-sdk"></a>

如果您想要撰寫在記憶體保護區中執行的程式碼，請使用您的提供者所支援的程式庫或架構。 [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) 是一個開放原始碼 SDK，可讓您對已啟用機密運算的不同硬體進行抽象處理。 

OE SDK 會建置為任何 CSP 上任何硬體的單一抽象層。 OE SDK 可在 Azure 機密運算虛擬機器上使用，以在記憶體保護區上建立和執行應用程式。

## <a name="next-steps"></a>後續步驟

部署 DCsv2 系列虛擬機器並在其上安裝 OE SDK。

> [!div class="nextstepaction"]
> [在 Azure Marketplace 中部署機密運算 VM](quick-create-marketplace.md)
