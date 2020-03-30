---
title: Azure 區塊鏈權杖帳戶管理
description: 使用 Azure 區塊鏈權杖帳戶管理，您可以創建組並連結區塊鏈帳戶，以控制對區塊鏈操作的訪問。
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74326107"
---
# <a name="azure-blockchain-tokens-account-management"></a>Azure 區塊鏈權杖帳戶管理

[!INCLUDE [Preview note](./includes/preview.md)]

對於區塊鏈解決方案，使用者可能需要對使用 Azure 區塊鏈權杖服務創建的權杖進行不同級別的訪問。 在大多數區塊鏈方案中，您需要規劃和部署分類帳上存在的不同區塊鏈帳戶。 您還需要管理參與者之間的訪問。使用 Azure 區塊鏈權杖帳戶管理，您可以創建組並連結區塊鏈帳戶，以控制對區塊鏈操作的訪問。

## <a name="blockchain-networks"></a>區塊鏈網路

Azure 區塊鏈權杖支援跨一組區塊鏈網路部署和管理權杖。 您可以將單個區塊鏈分類帳或多個區塊鏈分類帳連接到服務。

## <a name="accounts"></a>帳戶

對於連接到 Azure 區塊鏈權杖的區塊鏈網路，該服務創建和管理帳戶私有-公開金鑰對，並執行事務簽名和提交。 Azure 區塊鏈權杖還提供標識映射，以將帳戶與分類帳上的公開金鑰標識匹配。

## <a name="groups"></a>群組

通過組，您可以跨互聯網絡管理大量區塊鏈帳戶。 您可以跟蹤和審核目錄中哪些應用程式和使用者能夠通過 Azure 區塊鏈權杖 API 使用帳戶。 例如，您可以對一組表示不同業務線或不同角色和訪問區塊鏈權杖的帳戶進行分組。

還可以將組關聯到 Azure 活動目錄使用者或服務主體，並且此主體對組及其關聯帳戶具有許可權。  

## <a name="next-steps"></a>後續步驟

深入了解可用的 [Azure Blockchain Tokens 範本](templates.md)。
