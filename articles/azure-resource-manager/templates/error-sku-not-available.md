---
title: SKU 無法使用錯誤
description: 描述如何在使用 Azure Resource Manager 部署資源時，針對 [無法使用 SKU] 錯誤進行疑難排解。
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: 5d055540b4c0d8f2900d2ede6735662801be5974
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250651"
---
# <a name="resolve-errors-for-sku-not-available"></a>解決 SKU 無法使用的錯誤

本文說明如何解決 **SkuNotAvailable** 錯誤。 如果您找不到該區域/區域或符合您業務需求的替代區域/區域中的適當 SKU，請向 Azure 支援服務提交[SKU 要求](https://aka.ms/skurestriction)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>徵兆

在部署資源 (通常是虛擬機器) 時，您可能會收到下列錯誤碼和錯誤訊息︰

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>'
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>原因

當您選取的資源 SKU (例如 VM 大小) 不適用於您選取的位置時，就會收到這個錯誤。

如果您要部署 Azure 點 VM 或「點」擴展集實例，則在此位置中沒有適用于 Azure 點的任何容量。 如需詳細資訊，請參閱[找出錯誤訊息](../../virtual-machines/error-codes-spot.md)。

## <a name="solution-1---powershell"></a>解決方案 1：PowerShell

若要判斷區域/區域中可用的 Sku，請使用[get-azcomputeresourcesku](/powershell/module/az.compute/get-azcomputeresourcesku)命令。 依據位置篩選結果。 您必須擁有最新版 PowerShell 才能執行此命令。

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

結果包括該位置的 SKU 清單，以及該 SKU 的任何限制。 請注意，SKU 可能會列出為 `NotAvailableForSubscription`。

```powershell
ResourceType          Name           Locations   Zone      Restriction                      Capability           Value
------------          ----           ---------   ----      -----------                      ----------           -----
virtualMachines       Standard_A0    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1    centralus             NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2    centralus             NotAvailableForSubscription      MaxResourceVolumeMB  138240
virtualMachines       Standard_D1_v2 centralus   {2, 1, 3}                                  MaxResourceVolumeMB
```

一些其他範例：

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

在結尾附加 "fc" 會傳回更多詳細資料。

## <a name="solution-2---azure-cli"></a>解決方案 2：Azure CLI

若要判斷區域中可以使用哪些 SKU，請使用 `az vm list-skus` 命令。 使用 `--location` 參數可將輸出篩選至您要使用的位置。 使用 `--size` 參數可依部分大小名稱進行搜尋。

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

此命令會傳回類似以下的結果：

```output
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>解決方案 3：Azure 入口網站

若要判斷區域中可以使用哪些 SKU，請使用[入口網站](https://portal.azure.com)。 登入入口網站，然後透過介面新增資源。 當您設定值時，您會看到該資源可用的 SKU。 您不需要完成部署。

例如，請開始建立虛擬機器的程序。 若要查看其他可用的大小，請選取 [變更大小]。

![建立 VM](./media/error-sku-not-available/create-vm.png)

您可以篩選及捲動瀏覽可用的大小。

![可用的 SKU](./media/error-sku-not-available/available-sizes.png)

## <a name="solution-4---rest"></a>解決方案 4：REST

若要判斷區域中可用的 SKU，請使用[資源 SKU - 列出](/rest/api/compute/resourceskus/list)作業。

它會以下列格式傳回可用的 SKU 和區域︰

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

