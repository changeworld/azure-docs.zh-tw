---
title: 在 Azure SQL Database 中規劃 Intel SGX 記憶體保護區和證明
description: 使用 Azure SQL Database 的安全記憶體保護區規劃 Always Encrypted 的部署。
keywords: 加密資料、sql 加密、資料庫加密、機密資料、Always Encrypted、安全記憶體保護區、SGX、證明
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: c41856c394166f2e3b8fd3bde794f0f294ef6af9
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253334"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>在 Azure SQL Database 中規劃 Intel SGX 記憶體保護區和證明

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted 與 Azure SQL Database 的安全記憶體保護區目前處於 **公開預覽** 狀態。

[Always Encrypted 使用 Azure SQL Database 的安全記憶體保護區](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) ，會使用 [Intel Software Guard 擴充功能 (intel SGX) ](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) 記憶體保護區，並且需要 [Microsoft Azure 證明](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation)。

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>規劃 Azure SQL Database 中的 Intel SGX

Intel SGX 是以硬體為基礎的受信任執行環境技術。 Intel SGX 適用于使用 [vCore 模型](service-tiers-vcore.md) 和 [DC 系列](service-tiers-vcore.md?#dc-series) 硬體世代的資料庫。 因此，為了確保您可以在資料庫中搭配使用 Always Encrypted 與安全記憶體保護區，您需要在建立資料庫時選取 DC 系列硬體世代，也可以更新現有的資料庫以使用 DC 系列硬體世代。

> [!NOTE]
> 除了 DC 系列以外的硬體世代中無法使用 Intel SGX。 例如，Intel SGX 無法供第5代硬體使用，也不適用於使用 [DTU 模型](service-tiers-dtu.md)的資料庫。

> [!IMPORTANT]
> 在您為資料庫設定 DC 系列硬體世代之前，請先檢查 DC 系列的區域可用性，並確定您瞭解其效能限制。 如需詳細資訊，請參閱 [DC 系列](service-tiers-vcore.md#dc-series)。

## <a name="plan-for-attestation-in-azure-sql-database"></a>規劃 Azure SQL Database 中的證明

[Microsoft Azure 證明](../../attestation/overview.md) (preview) 是證明受信任的執行環境 (tee) 的解決方案，包括使用 DC 系列硬體世代的 Azure SQL 資料庫中的 Intel SGX 記憶體保護區。

若要在 Azure SQL Database 中使用 Azure 證明來證明 Intel SGX 記憶體保護區，您需要：

1. 建立 [證明提供者](../../attestation/basic-concepts.md#attestation-provider) ，並使用證明原則進行設定。 

2. 將您的 Azure SQL 邏輯伺服器存取權授與所建立的證明提供者。

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>設定 SGX 記憶體保護區和證明時的角色和責任

將您的環境設定為支援 Intel SGX 記憶體保護區，Azure SQL Database 中的 Always Encrypted 證明需要設定不同類型的元件： Microsoft Azure 證明、Azure SQL Database，以及觸發記憶體保護區證明的應用程式。 每種類型的元件都是由使用者（假設下列其中一個相異角色）來執行：

- 證明系統管理員-在 Microsoft Azure 證明中建立證明提供者、撰寫證明原則、授與 Azure SQL 邏輯伺服器對證明提供者的存取權，以及將指向原則的證明 URL 共用至應用程式系統管理員。
- Azure SQL Database 系統管理員-選取 DC 系列硬體世代來啟用資料庫中的 SGX 記憶體保護區，並為證明系統管理員提供存取證明提供者所需的 Azure SQL 邏輯伺服器身分識別。
- 應用程式系統管理員-使用從證明系統管理員取得的證明 URL 來設定應用程式。

在生產環境中 (處理實際的機密資料) ，在設定證明時，您的組織必須遵守角色分隔，其中每個相異角色都是由不同的人員所採用。 尤其是，如果您在組織中部署 Always Encrypted 的目標是要藉由確保 Azure SQL Database 系統管理員無法存取敏感性資料，來減少受攻擊面區，Azure SQL Database 系統管理員不應控制證明原則。

## <a name="next-steps"></a>後續步驟

- [為您的 Azure SQL 資料庫啟用 Intel SGX](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>另請參閱

- [教學課程：在 Azure SQL Database 中使用安全記憶體保護區來開始使用 Always Encrypted](always-encrypted-enclaves-getting-started.md)