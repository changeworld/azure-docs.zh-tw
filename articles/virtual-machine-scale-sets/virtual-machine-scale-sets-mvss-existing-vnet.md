---
title: 在 Azure 擴展集範本中參考現有的虛擬網路
description: 了解如何將虛擬網路新增到現有的「Azure 虛擬機器擴展集」範本
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 83328a31dad8009c28e146c81b24d6d9244f88a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273659"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>在 Azure 擴展集範本中新增對現有虛擬網路的參考

本文說明如何修改[基本擴展集範本](virtual-machine-scale-sets-mvss-start.md)，以部署到現有的虛擬網路，而不是建立新的。

## <a name="change-the-template-definition"></a>變更範本定義

在[前一篇文章](virtual-machine-scale-sets-mvss-start.md)中，我們已建立基本的擴展集範本。 我們現在會使用先前的範本並加以修改，以建立將擴展集部署至現有虛擬網路的範本。 

首先，新增 `subnetId` 參數。 這個字串會傳遞至擴展集組態，讓擴展集可識別要將虛擬機器部署到的預建子網路。 這個字串的格式必須是：`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

例如，若要將擴展集部署到名稱為 `myvnet`、子網路為 `mysubnet`、資源群組為 `myrg` 和訂用帳戶為 `00000000-0000-0000-0000-000000000000` 的現有虛擬網路，subnetId 會是：`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`。

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

接下來，從 `resources` 陣列刪除虛擬網路資源，因為您使用現有虛擬網路，因此不需要部署新的資源。

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

虛擬網路在部署範本之前就已存在，因此不需要指定從擴展集到虛擬網路的 dependsOn 子句。 刪除下列幾行：

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

最後，傳入使用者所`subnetId`設定的參數（而不是使用`resourceId`來取得相同部署中的 vnet 識別碼，也就是基本可行擴展集範本的用途）。

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>後續步驟

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
