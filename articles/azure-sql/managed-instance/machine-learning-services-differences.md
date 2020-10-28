---
title: Machine Learning 服務 (預覽) 的主要差異
description: 本文說明 Azure SQL 受控執行個體中的 Machine Learning 服務與 SQL Server Machine Learning 服務之間的主要差異。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 10/26/2020
ms.openlocfilehash: adf454ac697f8cabf4256ebfc5baa5d0d1c76264
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782462"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Azure SQL 受控執行個體和 SQL Server 中機器學習服務的主要差異

[AZURE SQL 受控執行個體 (preview) 中 Machine Learning 服務](machine-learning-services-overview.md)的功能與[SQL Server Machine Learning 服務](/sql/advanced-analytics/what-is-sql-server-machine-learning)幾乎完全相同。 以下是一些重要的差異。

> [!IMPORTANT]
> Azure SQL 受控執行個體中的 Machine Learning 服務目前處於公開預覽狀態。 若要註冊，請參閱 [註冊預覽](machine-learning-services-overview.md#signup)。

## <a name="preview-limitations"></a>預覽限制

在公開預覽其間，服務將會存在下列限制：

- 回送連線無法運作 (請參閱 [從 Python 或 R 腳本) SQL Server 的回送](/sql/machine-learning/connect/loopback-connection) 連線。
- 不支援外部資源集區。
- 僅支援 Python 和 R。 無法新增 JAVA 之類的外部語言。
- 不支援使用 [訊息傳遞介面](/message-passing-interface/microsoft-mpi) (MPI) 的案例。

如果服務等級目標 (SLO) 更新，請更新 SLO 並提出支援票證，以重新啟用 R/Python 的專用資源限制。

## <a name="language-support"></a>語言支援

SQL 受控執行個體中的 Machine Learning 服務和 SQL Server 同時支援 Python 和 R 擴充性 [架構](/sql/advanced-analytics/concepts/extensibility-framework)。 主要差異包括：

- Python 和 R 的初始版本在 SQL 受控執行個體和 SQL Server 的 Machine Learning 服務之間不同：

  | 系統               | Python | R     |
  |----------------------|--------|-------|
  | SQL 受控執行個體 | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- 不需要透過 `sp_configure` 設定 `external scripts enabled`。 一旦您 [註冊](machine-learning-services-overview.md#signup) 預覽，就會啟用 Azure SQL 受控執行個體的機器學習。

## <a name="packages"></a>套件

Python 和 R 套件管理在 SQL 受控執行個體和 SQL Server 之間的運作方式不同。 這些差異包括：

- 對於依賴外部執行階段 (如 Java) 或需要 OS API 安裝或使用存取權的套件並不支援。
- 封裝可以執行輸出網路呼叫 (從先前的預覽) 變更。 您可以在 [網路安全性群組](/azure/virtual-network/network-security-groups-overview) 層級設定正確的輸出安全性規則，以啟用輸出網路呼叫。

如需管理 Python 和 R 套件的詳細資訊，請參閱：

- [取得 Python 套件資訊](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [取得 R 套件資訊](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>資源管理

您無法透過 [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) 和外部資源集區來限制 R 資源。

在公開預覽期間，R 資源會設定為最多 20% 的 SQL 受控執行個體資源，並取決於您選擇的服務層級。 如需詳細資訊，請參閱 [Azure SQL Database 購買模型](../database/purchasing-models.md)。

### <a name="insufficient-memory-error"></a>記憶體不足錯誤

如果沒有足夠的記憶體可用來執行 R，您會收到錯誤訊息。 常見的錯誤訊息為：

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

記憶體使用量取決於 R 指令碼中的使用量，以及執行的平行查詢數目。 如果您收到上述錯誤，可以將資料庫調整為較高的服務層級，以解決此問題。

## <a name="next-steps"></a>後續步驟

- 請參閱「 [AZURE SQL 受控執行個體中的 Machine Learning 服務](machine-learning-services-overview.md)」總覽。
- 若要瞭解如何在 Machine Learning 服務中使用 Python，請參閱 [執行 python 腳本](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)。
- 若要瞭解如何在 Machine Learning 服務中使用 R，請參閱 [執行 r 腳本](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)。
