---
title: Azure 機密計算開發工具
description: 使用工具和程式庫來開發用於機密運算的應用程式
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 6bb3b8dbc7887419f7901a52b56c25f60c869abb
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994854"
---
# <a name="application-development-on-intel-sgx"></a>在 Intel SGX 上開發應用程式 


機密計算基礎結構需要特定的工具和軟體。 此頁面特別討論在 Intel SGX 上執行之 Azure 機密運算虛擬機器的應用程式開發相關概念。 閱讀此頁面之前，請先 [閱讀 INTEL SGX 虛擬機器和記憶體保護區的引進](confidential-computing-enclaves.md)。 

若要利用記憶體保護區和隔離環境的強大功能，您必須使用可支援機密運算的工具。 有各種工具可支援記憶體保護區應用程式開發。 例如，您可使用下列開放原始碼的架構： 

- [Open 記憶體保護區軟體發展工具組 (OE SDK) ](#oe-sdk)
- [機密聯盟架構 (CCF)](#ccf)

## <a name="overview"></a>概觀

在記憶體保護區中建置的應用程式會以兩種方式進行分割：

1. 「不受信任」的元件 (主機)
1. 「受信任」的元件 (記憶體保護區)


![應用程式開發](media/application-development/oe-sdk.png)


**主機**是記憶體保護區應用程式的執行基礎所在，而且是不受信任的環境。 主機無法存取部署在主機上的記憶體保護區程式碼。 

**記憶體保護區**是應用程式程式碼及其快取的資料/記憶體執行所在的位置。 記憶體保護區中應該會進行安全運算，以確保秘密和敏感性資料受到保護。 


在應用程式設計期間，請務必識別並判斷應用程式必須在記憶體保護區中執行的部分。 您選擇要放入受信任元件中的程式碼會與您應用程式的其餘部分隔離。 記憶體保護區完成初始化且程式碼載入至記憶體後，就無法從不受信任的元件讀取或變更該程式碼。 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave 軟體開發套件 (OE SDK) <a id="oe-sdk"></a>

如果您想要撰寫在記憶體保護區中執行的程式碼，請使用您的提供者所支援的程式庫或架構。 [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) 是一個開放原始碼 SDK，可讓您對已啟用機密運算的不同硬體進行抽象處理。 

OE SDK 會建置為任何 CSP 上任何硬體的單一抽象層。 OE SDK 可在 Azure 機密運算虛擬機器上使用，以在記憶體保護區上建立和執行應用程式。

## <a name="confidential-consortium-framework-ccf"></a>機密聯盟架構 (CCF) <a id="ccf"></a>

[CCF](https://github.com/Microsoft/CCF)是節點的分散式網路，每個節點都會執行自己的記憶體保護區。 受信任的節點網路可讓您執行分散式總帳。 總帳提供安全、可靠的元件供通訊協定使用。 

![CCF 節點](media/application-development/ccf.png)

此開放原始碼架構可讓您在區塊鏈的整個、更細緻的機密性和聯盟治理方面獲得高效益。 使用 Tee 的每個節點都可以確保安全的共識和交易處理。


## <a name="next-steps"></a>下一步 
- [部署機密計算 DCsv2 系列虛擬機器](quick-create-portal.md)
- [下載並安裝 OE SDK 並開始開發應用程式](https://github.com/openenclave/openenclave)