---
title: 範本的最佳做法
description: 說明用於製作 Azure Resource Manager 範本的建議方法。 提供建議來避免使用範本時的常見問題。
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: c62bde8fc8cfc79330d13b7b2ff4f778dadf1339
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497974"
---
# <a name="arm-template-best-practices"></a>ARM 範本的最佳作法

本文說明如何在建立 ARM 範本時使用建議的作法。 這些建議可協助您避免在使用 ARM 範本部署解決方案時遇到的常見問題。

## <a name="template-limits"></a>範本限制

將您的範本大小限制為 4 MB，並將每個參數檔案限制為 64 KB。 4 MB 的限制會套用至範本在使用反復資源定義擴展之後的最終狀態，以及變數和參數的值。

您也受限於：

* 256 個參數
* 256 個變數
* 800 個資源 (包括複本計數)
* 64 個輸出值
* 範本運算式中的 24,576 個字元

使用巢狀範本，即可超出一些範本限制。 如需詳細資訊，請參閱[在部署 Azure 資源時使用連結的範本](linked-templates.md)。 若要減少參數、變數或輸出數目，您可以將數個值合併成一個物件。 如需詳細資訊，請參閱[物件作為參數](/azure/architecture/building-blocks/extending-templates/objects-as-parameters)。

## <a name="resource-group"></a>資源群組

當您將資源部署至資源群組時，資源群組會儲存資源的相關中繼資料。 中繼資料會儲存在資源群組的位置。

如果資源群組的區域暫時無法使用，您就無法更新資源群組中的資源，因為中繼資料無法使用。 其他區域中的資源仍可如預期般運作，但您無法更新這些資源。 若要將風險降至最低，請將資源群組和資源放在相同區域。

## <a name="parameters"></a>參數

本節資訊對您在使用[參數](template-parameters.md)時會有幫助。

### <a name="general-recommendations-for-parameters"></a>參數的一般建議

* 將參數的使用最小化。 相反地，請對不需要在部署期間指定的屬性使用變數或常值。

* 參數名稱使用駝峰式大小寫。

* 針對根據環境 (例如 SKU、大小或容量) 而有所不同的設定，請使用參數。

* 針對希望能方便識別而指定的資源名稱使用參數。

* 在中繼資料中提供每個參數的描述：

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* 針對不敏感的參數定義預設值。 藉由指定預設值，您可以更輕鬆地部署範本，且範本的使用者可以看到適當值的範例。 參數的預設值皆必須對預設部署組態中的所有使用者有效。 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* 若要指定選擇性參數，請不要使用空字串作為預設值。 相反地，請使用常值或語言運算式來建構值。

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* 請謹慎使用 `allowedValues`。 只有當您必須確保允許的選項中不會包含某些值時才可使用。 如果您 `allowedValues` 太廣泛地使用，可能會因為不讓清單保持在最新狀態而封鎖有效的部署。

* 當範本中的參數名稱與 PowerShell 部署命令中的參數相符時，Resource Manager 會在範本參數加上後置詞 **FromTemplate** 以避免命名衝突。 例如，如果您在範本中包含名為 **ResourceGroupName** 的參數，它會與 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) Cmdlet 中的 **ResourceGroupName** 參數發生衝突。 部署期間，系統會提示您為 **ResourceGroupNameFromTemplate** 提供值。

### <a name="security-recommendations-for-parameters"></a>參數的安全性建議

* 使用者名稱和密碼 (或祕密) 請一律使用參數。

* 所有密碼和祕密都要使用 `securestring`。 如果您在 JSON 物件中傳遞敏感資料，請使用 `secureObject` 類型。 部署資源後，便無法讀取具有安全字串或安全物件類型的範本參數。 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* 針對需要 `secureString` 類型的使用者名稱、密碼或任何值，請勿提供預設值。

* 針對會增加應用程式受攻擊面的屬性，請勿提供預設值。

### <a name="location-recommendations-for-parameters"></a>參數的位置建議

* 請使用參數來指定資源的位置，並將預設值設定為 `resourceGroup().location`。 提供位置參數可讓範本的使用者指定其有權作為部署目的地的位置。

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* 請勿針對位置參數指定 `allowedValues`。 您指定的位置可能無法在所有雲端中使用。

* 針對可能位於相同位置的資源，請使用位置參數值。 此方法可以降低系統要求使用者提供位置資訊的次數。

* 針對無法在所有位置中使用的資源，請使用不同參數或指定常值的位置值。

## <a name="variables"></a>變數

當您使用[變數](template-variables.md)時，下列資訊可能會很有幫助︰

* 針對變數名稱使用 camel 大小寫。

* 您需要在範本中使用一次以上的值，才使用變數。 如果值只會使用一次，硬式編碼值會讓您的範本較容易看懂。

* 針對用複雜方式排列範本函式所建構的值，請使用變數。 當複雜的運算式只以變數形式顯示時，範本會較容易看懂。

* 您不能在範本的 **variables** 區段使用 [reference](template-functions-resource.md#reference) 函式。 **reference** 函式的值是從資源的執行階段狀態所衍生。 不過，將範本初始剖析時，會將變數加以解析。 請直接在範本的 **resources** 或 **outputs** 區段中，建構需要 **reference** 函式的值。

* 包含變數以用於必須是唯一的資源名稱。

* 請[在變數中使用複製迴圈](copy-variables.md)來建立重複的 JSON 物件模式。

* 請移除未使用的變數。

## <a name="api-version"></a>API 版本

將此 `apiVersion` 屬性設定為資源類型的硬式編碼 API 版本。 建立新的範本時，建議您針對資源類型使用最新的 API 版本。 若要判斷可用的值，請參閱 [範本參考](/azure/templates/)。

當您的範本如預期般運作時，建議您繼續使用相同的 API 版本。 藉由使用相同的 API 版本，您不必擔心可能會在較新版本中引進的重大變更。

請勿使用 API 版本的參數。 資源屬性和值可能會因 API 版本而異。 將 API 版本設定為參數時，程式碼編輯器中的 Intellisense 會無法判斷正確的結構描述。 如果您傳入的 API 版本不符合範本中的屬性，部署將會失敗。

請勿使用 API 版本的變數。 尤其是，請勿使用 [provider](template-functions-resource.md#providers) 函式，在部署期間動態取得 API 版本。 動態抓取的 API 版本可能不符合範本中的屬性。

## <a name="resource-dependencies"></a>資源相依性

決定 [要設定的相依](define-resource-dependency.md) 性時，請使用下列指導方針：

* 使用 **reference** 函式並傳入資源名稱，以便在必須共用屬性的資源之間設定隱含相依性。 當您已經定義隱含的相依性時，請勿新增明確的 `dependsOn` 元素。 這種方法可減少產生不必要相依性的風險。 如需設定隱含相依性的範例，請參閱 [隱含](define-resource-dependency.md#reference-and-list-functions)相依性。

* 設定子資源為相依於其父資源。

* 設定為[false 的資源](conditional-resource-deployment.md)，會自動從相依性順序中移除。 請在假定一律會部署該資源的情況下設定相依性。

* 讓相依性重疊顯示，而不需要明確設定它們。 例如，您的虛擬機器相依於虛擬網路介面，而虛擬網路介面相依於虛擬網路和公用 IP 位址。 因此，會在所有三個資源之後部署虛擬機器，但不會明確地將虛擬機器設定為相依於所有三個資源。 這種方法可釐清相依性順序，而且稍後可以比較容易變更範本。

* 如果部署之前可以決定值，請嘗試部署沒有相依性的資源。 例如，如果設定值需要另一個資源的名稱，您可能就不需要相依性。 本指引並非一律有效，因為某些資源會確認其他資源是否存在。 如果您收到錯誤，請加入相依性。

## <a name="resources"></a>資源

當您使用[資源](template-syntax.md#resources)時，下列資訊可能會很有幫助︰

* 針對範本中的每個資源指定 **comments**，協助其他參與者了解資源的用途：
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-06-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* 如果您使用範本中的公用端點 (例如 Azure Blob 儲存體公用端點)，請勿將命名空間硬式編碼。 使用 **reference** 函式，動態擷取命名空間。 您可以使用此方法可將範本部署到不同的公用命名空間環境中，而不需要將範本中的端點手動變更。 將 API 版本設定成與您在範本中用於儲存體帳戶相同的版本：
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   如果儲存體帳戶部署在您要建立的相同範本中，而儲存體帳戶的名稱未與範本中的另一個資源分享，則當您參考資源時，不需要指定提供者命名空間或 apiVersion。 下列範例顯示簡化的語法：
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
       }
   }
   ```
     
   您也可以參考不同資源群組中現有的儲存體帳戶：

   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
       }
   }
   ```

* 只有在應用程式要求時，才將公用 IP 位址指派給虛擬機器。 若要連線至虛擬機器 (VM) 進行偵錯，或要進行管理或系統管理，請使用輸入 NAT 規則、虛擬網路閘道或 Jumpbox。
   
     如需有關如何連線至虛擬機器的詳細資訊，請參閱︰
   
   * [在 Azure 中執行多層式架構的 VM](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
   * [在 Azure Resource Manager 中設定 VM 的 WinRM 存取](../../virtual-machines/windows/winrm.md)
   * [允許使用 Azure 入口網站從外部存取您的 VM](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [允許使用 PowerShell 從外部存取您的 VM](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [允許使用 Azure CLI 從外部存取您的 Linux VM](../../virtual-machines/linux/nsg-quickstart.md)

* 公用 IP 位址的 **domainNameLabel** 屬性必須是唯一的。 **domainNameLabel** 值的長度必須介於 3 到 63 個字元之間，還要遵循這個規則運算式指定的規則：`^[a-z][a-z0-9-]{1,61}[a-z0-9]$`。 **uniqueString** 函式會產生長度為 13 個字元的字串，因此 **dnsPrefixString** 參數會限制為 50 個字元：

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* 將密碼新增至自訂指令碼擴充功能時，使用 **protectedSettings** 屬性中的 **commandToExecute** 屬性。
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > 為了確保作為參數傳遞至 VM 和擴充功能的祕密會經過加密，請使用相關擴充功能的 **protectedSettings** 屬性。
   > 

## <a name="use-test-toolkit"></a>使用測試控管組

ARM 範本測試控管組是一種腳本，可檢查您的範本是否使用建議的做法。 當您的範本不符合建議的做法時，它會傳回警告清單以及建議的變更。 測試控管組可協助您瞭解如何在您的範本中執行最佳作法。

當您完成範本之後，請執行測試控管組，以查看是否有方法可以改善 it 的執行。 如需詳細資訊，請參閱 [ARM 範本測試控管](test-toolkit.md)組。

## <a name="next-steps"></a>後續步驟

* 如需範本檔案結構的詳細資訊，請參閱 [瞭解 ARM 範本的結構和語法](template-syntax.md)。
* 如需有關如何建立可在所有 Azure 雲端環境中運作之範本的建議，請參閱 [開發適用于雲端一致性的 ARM 範本](templates-cloud-consistency.md)。