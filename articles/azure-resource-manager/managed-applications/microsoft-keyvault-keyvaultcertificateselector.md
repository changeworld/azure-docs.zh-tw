---
title: KeyVaultCertificateSelector UI 元素
description: 描述 Azure 入口網站的 KeyVault. KeyVaultCertificateSelector UI 元素。
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101216"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>KeyVault. KeyVaultCertificateSelector UI 元素

用來選取金鑰保存庫憑證的控制項。

## <a name="ui-sample"></a>UI 範例

使用者會看到選取可用憑證的選項。

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="KeyVault. KeyVaultCertificateSelector":::

選取 [ **選取憑證** ] 之後，使用者可以從金鑰保存庫指定金鑰保存庫和憑證。

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="KeyVault. KeyVaultCertificateSelector":::

控制項會更新以顯示選取的金鑰保存庫和憑證名稱。

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="KeyVault. KeyVaultCertificateSelector":::

## <a name="schema"></a>結構描述

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>範例輸出

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>後續步驟

* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
