---
title: 在 Azure 縮放集樣本中引用自訂映像
description: 了解如何將自訂映像新增到現有的「Azure 虛擬機器擴展集」範本
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimckitt
ms.openlocfilehash: 3965090239949b5e1116ceebe427728e49ffafe4
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273686"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>新增自訂映像至 Azure 擴展集範本

本文演示如何修改[基本比例集範本](virtual-machine-scale-sets-mvss-start.md)以從自定義映射進行部署。

## <a name="change-the-template-definition"></a>變更範本定義
在前面的一[篇文章中](virtual-machine-scale-sets-mvss-start.md),我們創建了一個基本的比例集範本。 現在,我們將使用該早期範本對其進行修改,以創建從自定義映射部署縮放集的範本。  

### <a name="creating-a-managed-disk-image"></a>建立受控磁碟映像

如果您已有自訂的受控磁碟映像 (類型為 `Microsoft.Compute/images` 的資源)，則可略過此節。

首先，新增 `sourceImageVhdUri` 參數，這是 Azure 儲存體中包含所要部署自訂映像的一般化 blob 其 URI。


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

接著，新增類型為 `Microsoft.Compute/images` 的資源，這是以位在 URI `sourceImageVhdUri` 的一般化 blob 為基礎的受控磁碟映像。 此映像必須位在與使用它的擴展集相同的區域中。 在映像的屬性中，指定作業系統類型、blob 的位置 (從 `sourceImageVhdUri` 參數) 及儲存體帳戶類型：

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

在擴展集資源中，新增參照自訂映像的 `dependsOn` 子句，以確定會先建立映像，擴展集才會嘗試從該映像部署：

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>變更擴展集屬性以使用受控磁碟映像

在擴展集 `storageProfile` 的 `imageReference` 中，不是指定發行者、供應項目、sku 及平台映像版本，而是指定 `Microsoft.Compute/images` 資源的 `id`：

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

在此範例中，使用 `resourceId` 函式取得以相同範本所建立映像的資源識別碼。 如果您已事先建立受控磁碟映像，您應改為提供該映像的識別碼。 此識別碼的格式必須是：`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`。


## <a name="next-steps"></a>後續步驟

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
