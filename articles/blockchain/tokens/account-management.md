---
title: Azure 區塊鏈權杖帳戶管理
description: 使用 Azure 區塊鏈 token 帳戶管理時，您可以建立群組並連結區塊鏈帳戶，以控制區塊鏈動作的存取權。
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74326107"
---
# <a name="azure-blockchain-tokens-account-management"></a>Azure 區塊鏈權杖帳戶管理

[!INCLUDE [Preview note](./includes/preview.md)]

針對區塊鏈解決方案，使用者可能需要不同層級的存取權，以使用 Azure 區塊鏈 token 服務所建立的權杖。 在大部分的區塊鏈案例中，您必須規劃及部署存在於總帳上的不同區塊鏈帳戶。 您也需要管理所有參與者的存取權。使用 Azure 區塊鏈 token 帳戶管理時，您可以建立群組並連結區塊鏈帳戶，以控制區塊鏈動作的存取權。

## <a name="blockchain-networks"></a>區塊鏈網路

Azure 區塊鏈權杖可讓您在一組區塊鏈網路上部署和管理權杖。 您可以將單一區塊鏈總帳或數個區塊鏈總帳連線至服務。

## <a name="accounts"></a>帳戶

針對連線至 Azure 區塊鏈 token 的區塊鏈網路，此服務會建立並管理帳戶私用金鑰組，並執行交易簽署和提交。 Azure 區塊鏈 token 也會提供識別對應，以符合總帳上具有公開金鑰身分識別的帳戶。

## <a name="groups"></a>群組

群組可讓您跨已連線的網路管理大量的區塊鏈帳戶。 您可以追蹤和審核目錄中的哪些應用程式和使用者能夠透過 Azure 區塊鏈 token Api 使用帳戶。 例如，您可以將代表不同企業營運的一群組帳戶或不同的角色，以及區塊鏈權杖的存取權組成群組。

您也可以將群組與 Azure Active Directory 的使用者或服務主體建立關聯，而且此主體具有群組及其相關聯帳戶的許可權。  

## <a name="next-steps"></a>後續步驟

深入了解可用的 [Azure Blockchain Tokens 範本](templates.md)。
