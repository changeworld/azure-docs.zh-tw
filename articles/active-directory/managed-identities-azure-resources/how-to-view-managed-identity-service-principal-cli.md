---
title: 查看託管標識的服務主體 - Azure CLI - Azure AD
description: 逐步指導您如何使用 Azure CLI 檢視受控識別的服務主體。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fedef003c6d4143d3ad1814081d55b61d5ee020
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298694"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>使用 PowerShell 檢視受控識別的服務主體如何使用 Azure CLI 檢視受控識別的服務主體

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您會了解如何使用 Azure CLI 來檢視受控識別的服務主體。

## <a name="prerequisites"></a>Prerequisites

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。
- 如果您還沒有 Azure 帳戶，請[註冊免費帳戶](https://azure.microsoft.com/free/)。
- 啟用[虛擬機器](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)或[應用程式](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity)上的系統指派身分識別。
- 若要執行 CLI 指令碼範例，您有三個選項：
    - 從 Azure 入口網站使用 [Azure Cloud Shell](../../cloud-shell/overview.md) (請參閱下一節)。
    - 請透過每個程式碼區塊右上角的 [立即試用] 按鈕，使用內嵌的 Azure Cloud Shell。
    - 如果您偏好使用本機 CLI 主控台，請[安裝最新版的 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)，然後使用 `az login` 登入 Azure
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>檢視服務主體

下列命令示範如何檢視已啟用受控識別的 VM 或應用程式所具有的服務主體。 以您自己的值取代 `<VM or application name>`。 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>後續步驟

如需如何使用 Azure CLI 管理 Azure AD 服務主體的詳細資訊，請參閱 [az ad sp](/cli/azure/ad/sp)。


