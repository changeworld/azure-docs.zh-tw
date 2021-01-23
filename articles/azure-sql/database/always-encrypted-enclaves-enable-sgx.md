---
title: 為您的 Azure SQL Database 啟用 Intel SGX
description: 瞭解如何藉由選取啟用 SGX 的硬體產生，在 Azure SQL Database 中使用安全記憶體保護區來啟用 Intel SGX 的 Always Encrypted。
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
ms.openlocfilehash: ded1406c47bb3f00c366da7a5b28319f3712f8a7
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733751"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>為您的 Azure SQL Database 啟用 Intel SGX 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted 與 Azure SQL Database 的安全記憶體保護區目前處於 **公開預覽** 狀態。

在 Azure SQL Database 中[使用安全記憶體保護區 Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves)會使用[Intel Software Guard 延伸模組 (intel SGX) ](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/)記憶體保護區。 若要讓 Intel SGX 可供使用，資料庫必須使用 [vCore 模型](service-tiers-vcore.md) 和 [DC 系列](service-tiers-vcore.md#dc-series) 硬體世代。

設定 DC 系列硬體產生來啟用 Intel SGX 記憶體保護區是 Azure SQL Database 系統管理員的責任。 設定 [SGX 記憶體保護區和證明時，請參閱角色和責任](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)。

> [!NOTE]
> 除了 DC 系列以外的硬體世代中無法使用 Intel SGX。 例如，Intel SGX 無法供第5代硬體使用，也不適用於使用 [DTU 模型](service-tiers-dtu.md)的資料庫。

> [!IMPORTANT]
> 在您為資料庫設定 DC 系列硬體世代之前，請先檢查 DC 系列的區域可用性，並確定您瞭解其效能限制。 如需詳細資訊，請參閱 [DC 系列](service-tiers-vcore.md#dc-series)。

如需如何設定新的或現有的資料庫以使用特定硬體世代的詳細指示，請參閱 [選取硬體世代](service-tiers-vcore.md#selecting-a-hardware-generation)。
   
## <a name="next-steps"></a>後續步驟

- [設定 azure SQL database 伺服器的 Azure 證明](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>另請參閱

- [教學課程：在 Azure SQL Database 中使用安全記憶體保護區來開始使用 Always Encrypted](always-encrypted-enclaves-getting-started.md)