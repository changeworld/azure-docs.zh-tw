---
title: 建立 Azure 雲端服務 (延伸支援) -範本
description: '使用 ARM 範本來建立 Azure 雲端服務 (延伸支援) '
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: aeb53e722eae588b7d5e2963afe67f299c2c3565
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744967"
---
# <a name="create-a-cloud-service-extended-support-using-arm-templates"></a>使用 ARM 範本建立雲端服務 (延伸支援) 

> [!IMPORTANT]
> 雲端服務 (延伸支援) 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本教學課程說明如何使用 [ARM 範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)建立雲端服務 (延伸支援) 部署。 

## <a name="before-you-begin"></a>開始之前
1. 請參閱雲端服務的 [部署必要條件](deploy-prerequisite.md) (延伸支援) 並建立相關聯的資源。 

2. 使用 [Azure 入口網站](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) 或 [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell)建立新的資源群組。 如果您使用現有的資源群組，則此步驟是選擇性的。 
 
3. 使用 [Azure 入口網站](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) 或 [PowerShell](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-powershell)建立新的儲存體帳戶。 如果您使用現有的儲存體帳戶，則此步驟是選擇性的。 

4. 使用 [Azure 入口網站](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#upload-a-block-blob)、 [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json) 或 [PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell#upload-blobs-to-the-container)，將您的服務定義 ( 的) 和服務設定 ( .cscfg) 檔案上傳至儲存體帳戶。 在本教學課程稍後，取得要新增至 ARM 範本的兩個檔案的 SAS Uri。 

5.  (選擇性) 建立金鑰保存庫並上傳憑證。 
    -  憑證可以附加至雲端服務，以啟用與服務之間的安全通訊。 若要使用憑證，您必須在服務設定中指定其指紋 ( .cscfg) 檔案並上傳至金鑰保存庫。 Key Vault 可以透過 [Azure 入口網站](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal) 或 [PowerShell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell)建立。 
    - 相關聯的 Key Vault 必須位於與雲端服務相同的區域和訂用帳戶中。   
    - 的關聯 Key Vault 必須啟用適當的許可權，讓雲端服務 (延伸支援) 資源可以從 Key Vault 取得憑證。 如需詳細資訊，請參閱 [憑證和 Key Vault](certificates-and-key-vault.md)
    - 您必須在下列步驟所示的 ARM 範本的 OsProfile 區段中參考金鑰保存庫。

## <a name="create-a-cloud-service-extended-support"></a> (延伸支援) 建立雲端服務 
1. 建立虛擬網路。 虛擬網路的名稱必須符合服務設定 ( .cscfg) 檔案中的參考。 如果使用現有的虛擬網路，請從 ARM 範本省略此區段。

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     如果建立新的虛擬網路，請將下列內容新增至 `dependsOn` 區段，以確保平臺會在建立雲端服務之前先建立虛擬網路。 

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. 建立公用 IP 位址，並 (選擇性地) 設定公用 IP 位址的 DNS 標籤屬性。 如果您使用靜態 IP，則必須將它參考為服務設定 ( .cscfg) 檔案中的保留的 IP。 如果使用現有的 IP 位址，請略過此步驟，並將 IP 位址資訊直接新增至 ARM 範本的負載平衡器設定。
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     如果建立新的 IP 位址，請將下列內容新增至 `dependsOn` 區段，以確保平臺會在建立雲端服務之前建立 IP 位址。 
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. 建立網路設定檔物件，並將公用 IP 位址與負載平衡器的前端建立關聯。 負載平衡器會由平臺自動建立。  

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

4. 在 ARM 範本的區段中新增您的金鑰保存庫參考  `OsProfile`   。 Key Vault 是用來儲存與雲端服務相關聯的憑證， (延伸支援) 。 將憑證新增至 Key Vault，然後在 [服務設定] ( .cscfg) 檔案中參考憑證指紋。 您也需要啟用適當許可權的 Key Vault，讓雲端服務 (延伸支援) 資源可以從 Key Vault 中取出儲存為秘密的憑證。 如需詳細資訊，請參閱 [使用憑證搭配雲端服務 (延伸支援) ](certificates-and-key-vault.md)。
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > SourceVault 是您 Key Vault 的 ARM 資源識別碼。 您可以在 Key Vault 的 [屬性] 區段中找出資源識別碼，以找到這項資訊。 
    > - 您可以藉由流覽至金鑰保存庫中標示為 **秘密識別碼** 的憑證來找到 certificateUrl。  
   >  - certificateUrl 的格式應為 HTTPs：//{keyvault-端點}/secret/{secretname}/{secret-id}

5. 建立角色設定檔。 請確定 ARM 範本中的角色數目、角色名稱、每個角色的實例數目、每個角色的實例數目和大小都相同， ( .cscfg) 、服務定義)  ( 及角色設定檔區段。 
    
    ```json
    "roleProfile": { 
          "roles": { 
          "value": [ 
            { 
              "name": "WebRole1", 
              "sku": { 
                "name": "Standard_D1_v2", 
                "capacity": "1" 
              } 
            }, 
            { 
              "name": "WorkerRole1", 
              "sku": { 
                "name": "Standard_D1_v2", 
                "capacity": "1" 
              } 
            } 
        } 
    ```

6.  (選擇性) 建立延伸模組設定檔，以將延伸模組新增至您的雲端服務。 在此範例中，我們要新增遠端桌面和 Windows Azure 診斷擴充功能。 
    
    ```json
    "extensionProfile": { 
              "extensions": [ 
                 { 
                  "name": "RDPExtension", 
                  "properties": { 
                    "autoUpgradeMinorVersion": true, 
                    "publisher": "Microsoft.Windows.Azure.Extensions", 
                    "type": "RDP", 
                    "typeHandlerVersion": "1.2.1", 
                    "settings": " <PublicConfig>\r\n  <UserName>>[Insert Password]</UserName>\r\n  <Expiration>1/15/2022 12:00:00 AM</Expiration>\r\n</PublicConfig> ", 
                    "protectedSettings": "<PrivateConfig>\r\n  <Password>[Insert Password]</Password>\r\n</PrivateConfig>" 
                  } 
                } 
              ] 
            },

    "extensionProfile": { 
              "extensions": [ 
                { 
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1", 
                  "properties": { 
                    "autoUpgradeMinorVersion": true, 
                    "publisher": "Microsoft.Azure.Diagnostics", 
                    "type": "PaaSDiagnostics", 
                    "typeHandlerVersion": "1.5", 
                    "settings": "Include PublicConfig XML as a raw string", 
                    "protectedSettings": "Include PrivateConfig XML as a raw string”", 
                    "rolesAppliedTo": [ 
                      "WebRole1" 
                    ] 
                  } 
                }
              ]
            }
    ```    

7. 請參閱完整的範本。 

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "rdpPublicConfig": {
          "type": "string",
          "metadata": {
            "description": "Public config of remote desktop extension"
          }
        },
        "rdpPrivateConfig": {
          "type": "securestring",
          "metadata": {
            "description": "Private config of remote desktop extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2020-10-01-preview",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
            "roleProfile": {
              "roles": [
                {
                  "name": "WebRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                },
                {
                  "name": "WorkerRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                }
              ]
            },
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
        "extensionProfile": {
              "extensions": [
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                    ]
                  }
                },
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "[parameters('rdpPublicConfig')]",
                    "protectedSettings": "[parameters('rdpPrivateConfig')]"
                  }
                }
              ]
            }
          }
        }
      ]
    ```
 
8. 部署範本，並建立雲端服務 (延伸支援) 部署。 

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName “ContosOrg -TemplateFile "file path to your template file”  
    ```
 
## <a name="next-steps"></a>後續步驟 
- 請 [參閱雲端服務的常見問題，](faq.md) (延伸支援) 。
- 使用 [Azure 入口網站](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [範本](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)，將雲端服務部署 (延伸支援) 。