---
title: 查看受控識別的服務主體-Azure CLI-Azure AD
description: 逐步指導您如何使用 Azure CLI 檢視受控識別的服務主體。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce3a35562bdef34f44ef1093a3196ea7afb0bd9b
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92892038"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>使用 PowerShell 檢視受控識別的服務主體如何使用 Azure CLI 檢視受控識別的服務主體

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您會了解如何使用 Azure CLI 來檢視受控識別的服務主體。

如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱 [什麼是適用于 azure 資源的受控識別？](overview.md)。

- 啟用[虛擬機器](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)或[應用程式](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity)上的系統指派身分識別。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="view-the-service-principal"></a>檢視服務主體

下列命令示範如何檢視已啟用受控識別的 VM 或應用程式所具有的服務主體。 以您自己的值取代 `<Azure resource name>`。

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>後續步驟

如需如何使用 Azure CLI 管理 Azure AD 服務主體的詳細資訊，請參閱 [az ad sp](/cli/azure/ad/sp)。
