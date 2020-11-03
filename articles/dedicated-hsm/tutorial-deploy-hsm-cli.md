---
title: 教學課程：使用 Azure CLI 部署到現有的虛擬網路中 - Azure 專用 HSM | Microsoft Docs
description: 示範如何使用 CLI 將專用 HSM 部署到現有虛擬網路中的教學課程
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurecli
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d175ac75ce76836d012cdd04d4dbd7d81ffda584
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460694"
---
# <a name="tutorial-deploying-hsms-into-an-existing-virtual-network-using-the-azure-cli"></a>教學課程：使用 Azure CLI 將 HSM 部署至現有的虛擬網路

Azure 專用 HSM 提供實體裝置以供單獨客戶使用，其具有完整的系統管理控制權和完整的管理責任。 使用實體裝置創造了 Microsoft 控制裝置配置的需求，以確保有效率地管理容量。 因此，在 Azure 訂用帳戶內，通常看不見可供佈建資源的專用 HSM 服務。 任何需要存取專用 HSM 服務的 Azure 客戶都必須先連絡其 Microsoft 客戶代表，要求註冊專用 HSM 服務。 唯有順利完成此程序，才可能進行佈建。 

本教學課程示範典型的佈建程序，其中：

- 客戶已經有虛擬網路
- 他們有虛擬機器
- 他們必須將 HSM 資源新增到該現有的環境中。

高可用性、多區域的典型部署架構可能如下所示：

![多區域部署](media/tutorial-deploy-hsm-cli/high-availability-architecture.png)

本教學課程著重於一對已整合到現有虛擬網路 (請參閱上述的 VNET 1) 的 HSM 和必要的 ExpressRoute 閘道 (請參閱上述的子網路 1)。  所有其他資源都是標準 Azure 資源。 相同的整合程序可以用於上述 VNET 3 上子網路 4 中的 HSM。

## <a name="prerequisites"></a>Prerequisites

Azure 入口網站中目前尚未提供 Azure 專用 HSM。 所有與服務的互動都會透過命令列或使用 PowerShell 進行。 本教學課程會使用 Azure Cloud Shell 中的命令列 (CLI) 介面。 如果您不熟悉 Azure CLI，請遵循以下的入門指示：[Azure CLI 2.0 使用者入門](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest&preserve-view=true)。

假設：

- 您已完成 Azure 專用 HSM 註冊程序
- 您已獲准使用服務。 若非如此，請連絡 Microsoft 客戶代表以取得詳細資料。
- 您已建立這些資源的資源群組，而本教學課程中部署的新資訊將會加入該群組。
- 您已經按照上圖建立所需的虛擬網路、子網路和虛擬機器，而現在想要將 2 個 HSM 整合到該部署中。

以下所有指示都假設您已經導覽至 Azure 入口網站，並已開啟 Cloud Shell (選取靠近入口網站右上方的 "\>\_")。

## <a name="provisioning-a-dedicated-hsm"></a>佈建專用 HSM

透過 ExpressRoute 閘道佈建 HSM 並將其整合到現有的虛擬網路時，會使用 ssh 來驗證。 此驗證有助於確保 HSM 裝置的連線性和基本可用性，以便進行任何進一步的設定活動。

### <a name="validating-feature-registration"></a>驗證功能註冊

如上所述，任何佈建活動都要求向您的訂用帳戶註冊專用 HSM 服務。 若要加以驗證，請在 Azure 入口網站 Cloud Shell 中執行下列命令。

```azurecli
az feature show \
   --namespace Microsoft.HardwareSecurityModules \
   --name AzureDedicatedHSM
```

這些命令都應該會傳回 "Registered" 狀態 (如下所示)。 如果命令並未傳回 “Registered”，則必須連絡您的 Microsoft 帳戶代表以註冊此服務。

![訂用帳戶狀態](media/tutorial-deploy-hsm-cli/subscription-status.png)

### <a name="creating-hsm-resources"></a>建立 HSM 資源

在建立 HSM 資源之前，有一些您需要的必要資源。 您必須具有虛擬網路，內含適用於計算、HSM 和閘道的子網路範圍。 下列命令可作為建立這類虛擬網路的範例。

```azurecli
az network vnet create \
  --name myHSM-vnet \
  --resource-group myRG \
  --address-prefix 10.2.0.0/16 \
  --subnet-name compute \
  --subnet-prefix 10.2.0.0/24
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name hsmsubnet \
  --address-prefixes 10.2.1.0/24 \
  --delegations Microsoft.HardwareSecurityModules/dedicatedHSMs
```

```azurecli
az network vnet subnet create \
  --vnet-name myHSM-vnet \
  --resource-group myRG \
  --name GatewaySubnet \
  --address-prefixes 10.2.255.0/26
```

>[!NOTE]
>請注意，對於虛擬網路而言最重要的組態是 HSM 裝置的子網路必須將委派設定為 "Microsoft.HardwareSecurityModules/dedicatedHSMs"。  若未設定此選項，HSM 佈建將無法運作。

設定網路之後，請使用這些 Azure CLI 命令來佈建您的 HSM。

1. 使用 [az dedicated-hsm create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) 命令來佈建第一個 HSM。 HSM 的名稱是 hsm1。 取代您的訂用帳戶：

   ```azurecli
   az dedicated-hsm create --location westus --name hsm1 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

   此部署應該需要大約 25 到 30 分鐘才能完成，其中大量時間用於 HSM 裝置。

1. 若要查看目前的 HSM，請執行 [az dedicated-hsm show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) 命令：

   ```azurecli
   az dedicated-hsm show --resource group myRG --name hsm1
   ```

1. 使用此命令來佈建第二個 HSM：

   ```azurecli
   az dedicated-hsm create --location westus --name hsm2 --resource-group myRG --network-profile-network-interfaces \
        /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet
   ```

1. 執行 [az dedicated-hsm list](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) 命令來檢視目前 HSM 的詳細資料：

   ```azurecli
   az dedicated-hsm list --resource-group myRG
   ```

還有一些其他可能有用的命令。 使用 [az dedicated-hsm update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) 命令來更新 HSM：

```azurecli
az dedicated-hsm update --resource-group myRG –name hsm1
```

若要刪除 HSM，請使用 [az dedicated-hsm delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) 命令：

```azurecli
az dedicated-hsm delete --resource-group myRG –name hsm1
```

## <a name="verifying-the-deployment"></a>確認部署

若要驗證是否已佈建裝置以及查看裝置屬性，請執行下列命令集。 請確定已適當設定資源群組，且資源名稱完全與您在參數檔案中擁有的資源名稱相同。

```azurecli
subid=$(az account show --query id --output tsv)
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM1
az resource show \
   --ids /subscriptions/$subid/resourceGroups/myRG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSM2
```

您會看到類似下列輸出：

```json
{
    "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/HSMl",
    "identity": null,
    "kind": null,
    "location": "westus",
    "managedBy": null,
    "name": "HSM1",
    "plan": null,
    "properties": {
        "networkProfile": {
            "networkInterfaces": [
            {
            "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/networkInterfaces/HSMl_HSMnic", "privatelpAddress": "10.0.2.5",
            "resourceGroup": "HSM-RG"
            }
            L
            "subnet": {
                "id": n/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/HSM-RG/providers/Microsoft.Network/virtualNetworks/demo-vnet/subnets/hsmsubnet", "resourceGroup": "HSM-RG"
            }
        },
        "provisioningState": "Succeeded",
        "stampld": "stampl",
        "statusMessage": "The Dedicated HSM device is provisioned successfully and ready to use."
    },
    "resourceGroup": "HSM-RG",
    "sku": {
        "capacity": null,
        "family": null,
        "model": null,
        "name": "SafeNet Luna Network HSM A790",
        "size": null,
        "tier": null
    },
    "tags": {
        "Environment": "prod",
        "resourceType": "Hsm"
    },
    "type": "Microsoft.HardwareSecurityModules/dedicatedHSMs"
}
```

您現在也可以使用 [Azure 資源總管](https://resources.azure.com/)來查看資源。   在總管中，依序展開左側的 [訂用帳戶]、專用 HSM 的特定訂用帳戶、[資源群組]、您所使用的資源群組，最後選取 [資源] 項目。

## <a name="testing-the-deployment"></a>測試部署

測試部署時，請連線到可存取 HSM 的虛擬機器，然後直接連線到 HSM 裝置。 這些動作會確認 HSM 可連線。
SSH 工具用於連線至虛擬機器。 此命令將如下所示，但採用您在參數中指定的系統管理員名稱和 dns 名稱。

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

VM 的 IP 位址也用來取代上述命令中的 DNS 名稱。 如果命令成功，它會提示您輸入密碼，而您應該加以輸入。 登入虛擬機器後，即可使用在與 HSM 相關聯的網路介面資源的入口網站中找到的私人 IP 位址登入 HSM。

![元件清單](media/tutorial-deploy-hsm-cli/resources.png)

>[!NOTE]
>請注意，若已選取 [顯示隱藏的類型] 核取方塊，將會顯示 HSM 資源。

在上面的螢幕擷取畫面，按一下 "HSM1_HSMnic" 或 "HSM2_HSMnic" 會顯示適當的私人 IP 位址。 否則，以上使用的 `az resource show` 命令是識別正確 IP 位址的方法。 

當您擁有正確的 IP 位址時，請執行下列命令來替代該位址：

`ssh tenantadmin@10.0.2.4`

如果成功，系統會提示您輸入密碼。 預設密碼是 PASSWORD，而 HSM 會先要求您變更您的密碼，以設定強式密碼，然後使用貴組織所偏好的機制來儲存密碼並防止遺失。

>[!IMPORTANT]
>如果您遺失此密碼，HSM 必須重設，這表示您的金鑰會遺失。

當您使用 ssh 連線到 HSM 時，請執行下列命令，以確保 HSM 運作正常。

`hsm show`

其輸出應類似下圖所示：

![螢幕擷取畫面：在 PowerShell 視窗中顯示輸出。](media/tutorial-deploy-hsm-cli/hsm-show-output.png)

此時，您已配置所有資源，以提供高可用性、兩個 HSM 部署和已驗證的存取和操作狀態。 任何進一步的設定或測試都牽涉到更多 HSM 裝置本身的工作。 為此，您應該遵循 Gemalto Luna Network HSM 7 Administration Guide 第 7 章的指示，來初始化 HSM 及建立分割區。 您在 Gemalto 客戶支援入口網站中註冊且具有客戶識別碼後，可以直接從 Gemalto 下載所有文件和軟體。 下載用戶端軟體 7.2 版，以取得所有必要的元件。

## <a name="delete-or-clean-up-resources"></a>刪除或清除資源

如果您已處理完 HSM 裝置，即可將它當作資源刪除並傳回可用的集區。 執行此作業時的顯著考量就是裝置上的任何敏感性客戶資料。 將裝置「歸零」的最佳方式是讓 HSM 管理員的密碼連錯 3 次 (注意：這裡指的不是裝置管理員，而是實際的 HSM 管理員)。 由於金鑰內容有安全措施保護，裝置必須處於歸零狀態，才能以 Azure 資源的形式刪除。

> [!NOTE]
> 如果您有關於任何 Gemalto 裝置組態的問題，您應該連絡 [Gemalto 客戶支援](https://safenet.gemalto.com/technical-support/)。

如果您已處理完此資源群組中的所有資源，即可透過下列命令來將其移除：

```azurecli
az group delete \
   --resource-group myRG \
   --name HSMdeploy \
   --verbose
```

## <a name="next-steps"></a>後續步驟

完成本教學課程中的步驟之後，就會佈建專用 HSM 資源，而您具有包含必要 HSM 和進一步網路元件的虛擬網路，能夠與 HSM 進行通訊。  您現在可以慣用部署架構所需的更多資源，讓此部署更加完善。 如需有關協助規劃部署的詳細資訊，請參閱「概念」文件。
建議採用以下設計：主要區域中有兩個 HSM 可處理機架層級的可用性，而次要區域中有兩個 HSM 可處理區域可用性。 

* [高可用性](high-availability.md)
* [實體安全性](physical-security.md)
* [網路功能](networking.md)
* [支援能力](supportability.md)
* [監視](monitoring.md)
