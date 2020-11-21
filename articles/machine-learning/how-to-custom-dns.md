---
title: 使用自訂 DNS 伺服器
titleSuffix: Azure Machine Learning
description: 如何設定自訂 DNS 伺服器來使用 Azure Machine Learning 工作區和私人端點。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: c67dcbbe2ca6dea533260f59831556c4338374ba
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013003"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>如何搭配自訂 DNS 伺服器來使用工作區

使用具有私人端點的 Azure Machine Learning 工作區時，有 [數種方式可處理 DNS 名稱解析](../private-link/private-endpoint-dns.md)。 根據預設，Azure 會自動為您的工作區和私人端點處理名稱解析。 如果您改為 _使用自己的自訂 DNS 伺服器_ _，則必須手動建立工作區的 dns 專案。

> [!IMPORTANT]
> 本文僅說明如何尋找 (FQDN 的完整功能變數名稱) 以及這些專案的 IP 位址，而不會提供設定這些專案之 DNS 記錄的相關資訊。 如需有關如何新增記錄的詳細資訊，請參閱您的 DNS 軟體檔。

## <a name="prerequisites"></a>必要條件

- 使用 [您自己的 DNS 伺服器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)的 Azure 虛擬網路。

- 具有私人端點的 Azure Machine Learning 工作區。 如需詳細資訊，請參閱 [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

- 熟悉 [在定型 & 推斷期間使用網路隔離](./how-to-network-security-overview.md)。

- 熟悉[Azure 私人端點 DNS 區域](../private-link/private-endpoint-dns.md)設定

- （選擇性） [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="find-the-ip-addresses"></a>尋找 IP 位址

下列清單包含您的工作區和私人端點所使用 (FQDN) 的完整功能變數名稱：

* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* 如果您建立計算實例，則您也必須 `<instance-name>.<region>.instances.azureml.ms` 使用工作區私人端點的私人 IP 來新增的專案。

    > [!NOTE]
    > 您只能從虛擬網路記憶體取計算實例。

若要尋找 VNet 中 Fqdn 的內部 IP 位址，請使用下列其中一種方法：

> [!NOTE]
> 完整的功能變數名稱和 IP 位址會根據您的設定而有所不同。 例如，功能變數名稱中的 GUID 值將會是您的工作區所特有。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure 入口網站](#tab/azure-portal)

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，選取您的 Azure Machine Learning __工作區__。
1. 從 [ __設定__ ] 區段中，選取 [ __私人端點__ 連線]。
1. 選取顯示的 [ __私人端點__ ] 資料行中的連結。
1. 工作區私人端點的完整功能變數名稱 (FQDN) 和 IP 位址的清單位於頁面底部。

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="入口網站中的 Fqdn 清單":::

---

所有方法所傳回的資訊都相同;資源的 FQDN 和私人 IP 位址清單。

| FQDN | IP 位址 |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> 私人端點未列出某些 Fqdn，但工作區需要這些 Fqdn。 下表列出這些 Fqdn，而且也必須新增至您的 DNS 伺服器：
>
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * 如果您有計算實例，請使用 `<instance-name>.<region>.instances.azureml.ms` ，其中 `<instance-name>` 是您的計算實例名稱。 請使用工作區私人端點的私人 IP 位址。 請注意，您只能從虛擬網路記憶體取計算實例。
>
> 針對所有這些 IP 位址，請使用與 `*.api.azureml.ms` 上一個步驟所傳回專案相同的位址。

## <a name="next-steps"></a>下一步

如需搭配虛擬網路使用 Azure Machine Learning 的詳細資訊，請參閱 [虛擬網路總覽](how-to-network-security-overview.md)。