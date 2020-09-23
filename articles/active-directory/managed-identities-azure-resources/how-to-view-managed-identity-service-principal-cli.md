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
ms.date: 11/29/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: b8eec72666eadf90a401dc8f0adb77df77dbf782
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969307"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>使用 PowerShell 檢視受控識別的服務主體如何使用 Azure CLI 檢視受控識別的服務主體

Azure 資源受控識別會在 Azure Active Directory 中為 Azure 服務提供自動受控識別。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您會了解如何使用 Azure CLI 來檢視受控識別的服務主體。

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。
- 如果您還沒有 Azure 帳戶，請[註冊免費帳戶](https://azure.microsoft.com/free/)。
- 啟用[虛擬機器](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)或[應用程式](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity)上的系統指派身分識別。
- 若要執行範例腳本，您有兩個選項：
    - 使用您可以使用程式碼區塊右上角的 [**試試看**] 按鈕開啟的[Azure Cloud Shell](../../cloud-shell/overview.md)。
    - 安裝最新版本的 [Azure CLI](/cli/azure/install-azure-cli)，然後使用 [az Login 登](/cli/azure/reference-index#az-login)入 Azure，以在本機執行腳本。 使用與您想要在其中建立資源的 Azure 訂用帳戶相關聯的帳戶。   

## <a name="view-the-service-principal"></a>檢視服務主體

下列命令示範如何檢視已啟用受控識別的 VM 或應用程式所具有的服務主體。 以您自己的值取代 `<VM or application name>`。 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>下一步

如需如何使用 Azure CLI 管理 Azure AD 服務主體的詳細資訊，請參閱 [az ad sp](/cli/azure/ad/sp)。
