---
title: 為 Azure 前門配置 IP 限制 WAF 規則
description: 瞭解如何配置 Web 應用程式防火牆規則以限制現有 Azure 前門終結點的 IP 位址。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336087"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>使用 Azure 前門的 Web 應用程式防火牆配置 IP 限制規則

本文演示如何使用 Azure 門戶、Azure CLI、Azure PowerShell 或 Azure 資源管理器範本在 Azure 前門的 Web 應用程式防火牆 （WAF） 中配置 IP 限制規則。

基於 IP 位址的存取控制規則是一種自訂 WAF 規則，允許您控制對 Web 應用程式的訪問。 為此，它指定無類域間路由 （CIDR） 格式的 IP 位址或 IP 位址範圍清單。

預設情況下，可從 Internet 訪問 Web 應用程式。 如果要限制從已知 IP 位址或 IP 位址範圍清單中訪問用戶端，可以創建一個 IP 匹配規則，該規則包含 IP 位址清單作為匹配值，並將運算子設置為"不"（否定為 true）**和阻止操作**。 應用 IP 限制規則後，來自此允許清單外部地址的請求將收到 403 禁止回應。

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>使用 Azure 門戶配置 WAF 策略

### <a name="prerequisites"></a>Prerequisites

按照["快速入門：為高度可用的全域 Web 應用程式創建前門"](../../frontdoor/quickstart-create-front-door.md)中所述的說明創建 Azure 前門設定檔。

### <a name="create-a-waf-policy"></a>建立 WAF 原則

1. 在 Azure 門戶上，選擇 **"創建資源**"，在搜索框中鍵入**Web 應用程式防火牆**，然後選擇**Web 應用程式防火牆 （WAF）。**
2. 選取 [建立]****。
3. 在 **"創建 WAF 策略**"頁上，使用以下值完成 **"基礎知識"** 選項卡：
   
   |設定  |值  |
   |---------|---------|
   |政策     |全球 WAF（前門）|
   |訂用帳戶     |選取您的訂用帳戶|
   |資源群組     |選擇前門所在的資源組。|
   |原則名稱     |鍵入策略的名稱|
   |原則狀態     |啟用|

   選擇 **"下一步"：策略設置**

1. 在 **"策略設置"** 選項卡上，選擇 **"防止**"。 對於**塊回應正文**，鍵入 *"您已被阻止"！* 因此，您可以看到自訂規則有效。
2. 選擇 **"下一步"：託管規則**。
3. 選擇**下一個：自訂規則**。
4. 選擇 **"添加自訂規則**"。
5. 在 **"添加自訂規則"** 頁上，使用以下測試值創建自訂規則：

   |設定  |值  |
   |---------|---------|
   |自訂規則名稱     |FdWafCustRule|
   |狀態     |啟用|
   |規則型別     |相符項目|
   |優先順序    |100|
   |相符類型     |IP 位址|
   |匹配變數|遠端添加器|
   |作業|不包含|
   |IP 位址或範圍|10.10.10.0/24|
   |結果為|拒絕流量|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="自訂規則":::

   選取 [加入]****。
6. 選擇 **"下一步"：關聯**。
7. 選擇 **"添加前端主機**"。
8. 對於**前端主機**，選擇前端主機並選擇"**添加**"。
9. 選擇 **"審閱" = 創建**。
10. 策略驗證通過後，選擇 **"創建**"。

### <a name="test-your-waf-policy"></a>測試 WAF 策略

1. WAF 策略部署完成後，流覽到前門前端主機名稱。
2. 您應該會看到自訂塊消息。

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF 規則測試":::

   > [!NOTE]
   > 自訂規則中有意使用私人 IP 位址，以確保規則將觸發。 在實際部署中，針對您的特定情況創建使用 IP 位址創建*允許*和*拒絕*規則。

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>使用 Azure CLI 配置 WAF 策略

### <a name="prerequisites"></a>Prerequisites
在開始配置 IP 限制策略之前，請設置 CLI 環境並創建 Azure 前門設定檔。

#### <a name="set-up-the-azure-cli-environment"></a>設置 Azure CLI 環境
1. 安裝[Azure CLI](/cli/azure/install-azure-cli)，或使用 Azure 雲外殼。 Azure Cloud Shell 是免費的 Bash Shell，您可以直接在 Azure 入口網站內執行。 它具有預先安裝和設定的 Azure CLI，可與您的帳戶搭配使用。 在隨後訪問的 CLI 命令中選擇"**試用"** 按鈕，然後在打開的雲殼會話中登錄到 Azure 帳戶。 會話開始後，輸入`az extension add --name front-door`以添加 Azure 前門擴展。
 2. 如果在 Bash 中本地使用 CLI，請使用`az login`登錄到 Azure。

#### <a name="create-an-azure-front-door-profile"></a>創建 Azure 前門設定檔
按照["快速入門：為高度可用的全域 Web 應用程式創建前門"](../../frontdoor/quickstart-create-front-door.md)中所述的說明創建 Azure 前門設定檔。

### <a name="create-a-waf-policy"></a>建立 WAF 原則

使用[az 網路前門 waf 策略創建命令創建](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create)WAF 策略。 在以下示例中，將策略名稱*IPAllowPolicyExampleCLI*替換為唯一的策略名稱。

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>添加自訂 IP 存取控制規則

使用[az 網路前門 waf 策略自訂規則創建](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create)命令為剛剛創建的 WAF 策略添加自訂 IP 存取控制規則。

在以下示例中：
-  將*IPAllow 政策示例 CLI*替換為之前創建的唯一策略。
-  將*ip 位址範圍-1，ip**位址範圍 2*替換為您自己的範圍。

首先，為從上一步創建的策略創建 IP 允許規則。 
> [!NOTE]
> **--需要延遲**，因為規則必須具有要在下一步中添加的匹配條件。

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
接下來，向規則添加匹配條件：

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>查找 WAF 策略的 ID 
使用[az 網路前門 waf-策略顯示](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show)命令查找 WAF 策略的 ID。 將以下示例中*的 IPAllow 政策示例 CLI*替換為您之前創建的唯一策略。

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>將 WAF 策略連結到 Azure 前門前端主機

使用[az 網路前門更新](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update)命令將 Azure 前門*Web 應用程式防火牆策略連結*ID 設置為策略 ID。 將*IPAllow 政策示例 CLI*替換為您之前創建的唯一策略。

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
在此示例中，WAF 策略應用於**前端點[0]。** 您可以將 WAF 策略連結到任何前端。
> [!Note]
> 只需設置一次**Web 應用程式防火牆策略連結**屬性，才能將 WAF 策略連結到 Azure 前門前端。 後續策略更新將自動應用於前端。

## <a name="configure-a-waf-policy-with-azure-powershell"></a>使用 Azure PowerShell 配置 WAF 策略

### <a name="prerequisites"></a>Prerequisites
在開始配置 IP 限制策略之前，請設置 PowerShell 環境並創建 Azure 前門設定檔。

#### <a name="set-up-your-powershell-environment"></a>設定 PowerShell 環境
Azure PowerShell 提供了一組使用[Azure 資源管理器](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)模型來管理 Azure 資源的 Cmdlet。

您可以在本機電腦上安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)，並將其用於任何 PowerShell 工作階段。 按照頁面上的說明使用 Azure 憑據登錄到 PowerShell，然後安裝 Az 模組。

1. 使用以下命令連接到 Azure，然後使用互動式對話方塊登錄。
    ```
    Connect-AzAccount
    ```
 2. 在安裝 Azure 前門模組之前，請確保安裝了 PowerShellGet 模組的當前版本。 運行以下命令，然後重新打開 PowerShell。

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. 使用以下命令安裝 Az.FrontDoor 模組。 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>創建 Azure 前門設定檔
按照["快速入門：為高度可用的全域 Web 應用程式創建前門"](../../frontdoor/quickstart-create-front-door.md)中所述的說明創建 Azure 前門設定檔。

### <a name="define-an-ip-match-condition"></a>定義 IP 匹配條件
使用["新建"面法WafMatch條件物件](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)"命令定義 IP 匹配條件。
在下面的示例中，將*ip 位址範圍-1* *、ip 位址範圍 2*替換為您自己的範圍。    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>創建自訂 IP 允許規則

使用["新建"面法WafCustomRuleObject命令](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject)定義操作並設定優先權。 在下面的示例中，將阻止來自與清單匹配的用戶端 IP 的請求。

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>配置 WAF 策略
使用`Get-AzResourceGroup`查找包含 Azure 前門設定檔的資源組的名稱。 接下來，使用[New-AzFrontDoorWaf策略](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)使用 IP 規則配置 WAF 策略。

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>將 WAF 策略連結到 Azure 前門前端主機

將 WAF 策略物件連結到現有前端主機並更新 Azure 前門屬性。 首先，使用[Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)檢索 Azure 前門物件。 接下來，使用[Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)命令將**Web 應用程式防火牆策略連結**屬性設置為在上一步驟中創建的 *$IPAllowPolicyExamplePS*的資源識別碼。

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> 在此示例中，WAF 策略應用於**前端點[0]。** 您可以將 WAF 策略連結到任何前端。 只需設置一次**Web 應用程式防火牆策略連結**屬性，才能將 WAF 策略連結到 Azure 前門前端。 後續策略更新將自動應用於前端。


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>使用資源管理器範本配置 WAF 策略
要查看創建 Azure 前門策略的範本和具有自訂 IP 限制規則的 WAF 策略，請訪問[GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)。


## <a name="next-steps"></a>後續步驟

- 瞭解如何創建[Azure 前門設定檔](../../frontdoor/quickstart-create-front-door.md)。
