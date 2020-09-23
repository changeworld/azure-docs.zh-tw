---
title: Azure 中的證明記憶體保護區
description: 瞭解如何使用證明來驗證您的機密計算信任環境是否安全
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: 70a17aacde67744eae74ca263200f2c65fbd300a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994849"
---
# <a name="attesting-sgx-enclaves"></a>證明 SGX 記憶體保護區

Azure 上的機密運算提供以 Intel SGX 為基礎的虛擬機器，可隔離您部分的程式碼或資料。 使用這些 [記憶體保護區](confidential-computing-enclaves.md)時，您會想要驗證並驗證信任的環境是否安全。 此種驗證就是證明程序。 

## <a name="overview"></a>概觀 

證明可讓信賴憑證者對以下事項更有信心：(1) 其軟體是在記憶體保護區中執行，而且 (2) 記憶體保護區是最新且安全的。 例如，記憶體保護區會要求基礎硬體產生認證，其中包含記憶體保護區存在於平台上的證明。 然後可將報告提供給第二個記憶體保護區，以確認報告是在相同的平台上產生。

![證明記憶體保護區中的程式碼](media/attestation/attestation.png)



證明必須使用與系統軟體和晶片相容的安全證明服務來執行。 您可以使用的一些服務範例如下

- [Microsoft Azure 證明 (preview) ](https://docs.microsoft.com/azure/attestation/overview) 或
- [Intel 的證明和布建服務](https://software.intel.com/sgx/attestation-services)


這兩者都與 Azure 機密運算 Intel SGX 基礎結構相容。 

## <a name="next-steps"></a>下一步
試用 [適用于記憶體保護區感知應用程式的 Microsoft Azure 證明範例](https://docs.microsoft.com/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/)。