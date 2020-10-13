---
title: 以程式設計方式建立 Azure Dashboards
description: 使用 Azure 入口網站中的儀表板作為範本，以程式設計方式建立 Azure 儀表板。 包含 JSON 參考。
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: mtillman
ms.service: azure-portal
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: bdaf1261e9945aa862157f7e43a44387e14d3657
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764038"
---
# <a name="programmatically-create-azure-dashboards"></a>以程式設計方式建立 Azure Dashboards

本文將逐步引導您完成以程式設計方式建立及發佈 Azure 儀表板的流程。 文件全程參照以下所示的儀表板。

![範例儀表板](./media/azure-portal-dashboards-create-programmatically/sample-dashboard.png)

## <a name="overview"></a>概觀

[Azure 入口網站](https://portal.azure.com)中的共用儀表板就像是虛擬機器和儲存體帳戶一樣的[資源](../azure-resource-manager/management/overview.md)。 您可以使用 [AZURE RESOURCE MANAGER REST api](/rest/api/)、 [Azure CLI](/cli/azure)和 [Azure PowerShell 命令](/powershell/azure/get-started-azureps)，以程式設計方式管理資源。

許多功能都建基於這些 Api，以簡化資源管理。 這些 API 和工具都能夠建立、列出、擷取、修改和刪除資源。 由於儀表板是資源，您可以選擇您最愛的 API 或工具來使用。

無論您使用哪種工具，以程式設計方式建立儀表板，您都可以建立儀表板物件的 JSON 標記法。 此物件包含儀表板上磚的相關資訊。 它包含大小、位置、其所系結的資源，以及任何使用者自訂。

若要建立此 JSON 檔，最實用的方法是使用 Azure 入口網站。 您可以透過互動方式新增並放置磚。 然後從結果中匯出 JSON 並建立範本，以供稍後在腳本、程式和部署工具中使用。

## <a name="create-a-dashboard"></a>建立儀表板

若要建立儀表板，請從[Azure 入口網站](https://portal.azure.com)] 功能表中選取 [**儀表板**]，然後選取 [**新增儀表板**]。

![新儀表板命令](./media/azure-portal-dashboards-create-programmatically/new-dashboard-command.png)

使用磚庫來尋找和新增磚。 透過拖曳和置放即可新增圖格。 某些圖格支援使用拖曳控點來調整大小。

![拖曳控點以變更大小](./media/azure-portal-dashboards-create-programmatically/drag-handle.png)

有些則具有固定的大小，可以在其內容功能表中選擇。

![調整要變更大小的內容功能表大小](./media/azure-portal-dashboards-create-programmatically/sizes-context-menu.png)

## <a name="share-the-dashboard"></a>共用儀表板

設定儀表板之後，下一個步驟是使用 **Share** 命令發佈儀表板。

![共用儀表板](./media/azure-portal-dashboards-create-programmatically/share-command.png)

選取 [ **共用** ] 會提示您選擇要發佈的訂用帳戶和資源群組。 您必須具有所選訂用帳戶和資源群組的寫入權限。 如需詳細資訊，請參閱 [使用 AZURE RBAC 和 Azure 入口網站來新增或移除角色指派](../role-based-access-control/role-assignments-portal.md)。

![進行共用和存取的變更](./media/azure-portal-dashboards-create-programmatically/sharing-and-access.png)

## <a name="fetch-the-json-representation-of-the-dashboard"></a>擷取儀表板的 JSON 表示法

發佈只需要幾秒鐘。 完成時，下一步是使用 **下載** 命令來提取 JSON。

![下載 JSON 標記法](./media/azure-portal-dashboards-create-programmatically/download-command.png)

## <a name="create-a-template-from-the-json"></a>從 JSON 建立範本

下一步是從這個 JSON 建立範本。 使用適當的資源管理 Api、命令列工具或在入口網站中，以程式設計方式使用該範本。

您不需要完全瞭解儀表板 JSON 結構即可建立範本。 在大部分情況下，您會想要保留每個磚的結構和設定。 然後將磚所指向的 Azure 資源集參數化。 查看您匯出的 JSON 儀表板，並尋找所有出現的 Azure 資源識別碼。 我們的範例儀表板有多個圖格全部指向單一 Azure 虛擬機器。 這是因為我們的儀表板只會查看這個單一資源。 如果您搜尋範例 JSON （包含在檔結尾），則針對 "/subscriptions"，您會發現此識別碼有幾個出現次數。

`/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1`

若要在未來為任何虛擬機器發佈此儀表板，請在 JSON 中將此字串的每個出現專案參數化。

在 Azure 中建立資源的 Api 有兩種方法：

* 命令式 Api 一次建立一項資源。 如需詳細資訊，請參閱[資源](/rest/api/resources/resources)。
* 以範本為基礎的部署系統，可透過單一 API 呼叫來建立多個相依的資源。 如需詳細資訊，請參閱  [使用 Resource Manager 範本部署資源和 Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)。

以範本為基礎的部署支援參數化和範本化。 我們會在本文中使用此方法。

## <a name="programmatically-create-a-dashboard-from-your-template-using-a-template-deployment"></a>以程式設計方式使用範本部署從範本建立儀表板

Azure 可讓您協調多個資源的部署。 您可以建立部署範本，以表示要部署的資源集合，以及它們之間的關聯性。  每個資源的 JSON 格式都相同，就如同您逐一建立這些資源一般。 差異在於，範本語言會加入一些概念，例如變數、參數、基本功能等等。 只有在範本部署的內容中才支援此擴充語法。 如果與稍早所討論的命令式 Api 搭配使用，它將無法運作。 如需詳細資訊，請參閱 [瞭解 Azure Resource Manager 範本的結構和語法](../azure-resource-manager/resource-group-authoring-templates.md)。

參數化應該使用範本的參數語法來完成。  您可以取代我們稍早找到的資源識別碼的所有實例，如下所示。

使用硬式編碼資源識別碼的 JSON 屬性範例：

```json
id: "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
```

按照範本參數轉換為參數化版本的 JSON 屬性範例

```json
id: "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
```

在 JSON 範本的頂端宣告必要的範本中繼資料和參數，如下所示：

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},

    ... rest of template omitted ...
```
設定您的範本之後，請使用下列任一種方法來部署它：

* [REST API](/rest/api/resources/deployments)
* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](/cli/azure/group/deployment#az-group-deployment-create)
* [Azure 入口網站範本部署頁面](https://portal.azure.com/#create/Microsoft.Template)

接下來，您會看到範例儀表板 JSON 的兩個版本。 第一個是我們從已經繫結至資源的入口網站匯出的版本。 第二個是可透過程式設計方式系結至任何虛擬機器，並使用 Azure Resource Manager 部署的範本版本。

### <a name="json-representation-of-our-example-dashboard-before-templating"></a>範本前範例儀表板的 JSON 標記法

此範例會顯示您在遵循這篇文章時可預期看到的內容。 已部署之儀表板的 JSON 標記法匯出的指示。 硬式編碼的資源識別碼顯示此儀表板指向特定的 Azure 虛擬機器。

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": { }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

### <a name="template-representation-of-our-example-dashboard"></a>範例儀表板的範本表示法

儀表板的範本版本已定義三個參數 `virtualMachineName` ，稱為、 `virtualMachineResourceGroup` 和 `dashboardName` 。  這些參數可讓您在每次部署時，將此儀表板指向不同的 Azure 虛擬機器。 您可以透過程式設計方式設定和部署此儀表板，以指向任何 Azure 虛擬機器。 若要測試此功能，請複製下列範本，並將它貼入 [Azure 入口網站範本部署頁面](https://portal.azure.com/#create/Microsoft.Template)。

此範例是自行部署儀表板，但是範本語言可讓您部署多個資源，並組合一個或多個儀表板。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualMachineName": {
            "type": "string"
        },
        "virtualMachineResourceGroup": {
            "type": "string"
        },
        "dashboardName": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "properties": {
                "lenses": {
                    "0": {
                        "order": 0,
                        "parts": {
                            "0": {
                                "position": {
                                    "x": 0,
                                    "y": 0,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                                "title": "",
                                                "subtitle": ""
                                            }
                                        }
                                    }
                                }
                            },
                            "1": {
                                "position": {
                                    "x": 3,
                                    "y": 0,
                                    "rowSpan": 4,
                                    "colSpan": 8
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                                "title": "Test VM Dashboard",
                                                "subtitle": "Contoso"
                                            }
                                        }
                                    }
                                }
                            },
                            "2": {
                                "position": {
                                    "x": 0,
                                    "y": 2,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [],
                                    "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                                    "settings": {
                                        "content": {
                                            "settings": {
                                                "title": "",
                                                "subtitle": "",
                                                "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                                "autoplay": false
                                            }
                                        }
                                    }
                                }
                            },
                            "3": {
                                "position": {
                                    "x": 0,
                                    "y": 4,
                                    "rowSpan": 3,
                                    "colSpan": 11
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Percentage CPU",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "4": {
                                "position": {
                                    "x": 0,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Operations/Sec",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "5": {
                                "position": {
                                    "x": 3,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Disk Read Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Disk Write Bytes",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "6": {
                                "position": {
                                    "x": 6,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 3
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "queryInputs",
                                            "value": {
                                                "timespan": {
                                                    "duration": "PT1H",
                                                    "start": null,
                                                    "end": null
                                                },
                                                "id": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]",
                                                "chartType": 0,
                                                "metrics": [
                                                    {
                                                        "name": "Network In",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    },
                                                    {
                                                        "name": "Network Out",
                                                        "resourceId": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                                    }
                                                ]
                                            }
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                                    "settings": {}
                                }
                            },
                            "7": {
                                "position": {
                                    "x": 9,
                                    "y": 7,
                                    "rowSpan": 2,
                                    "colSpan": 2
                                },
                                "metadata": {
                                    "inputs": [
                                        {
                                            "name": "id",
                                            "value": "[resourceId(parameters('virtualMachineResourceGroup'), 'Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
                                        }
                                    ],
                                    "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                                    "asset": {
                                        "idInputName": "id",
                                        "type": "VirtualMachine"
                                    },
                                    "defaultMenuItemId": "overview"
                                }
                            }
                        }
                    }
                }
            },
            "metadata": { },
            "apiVersion": "2015-08-01-preview",
            "type": "Microsoft.Portal/dashboards",
            "name": "[parameters('dashboardName')]",
            "location": "westus",
            "tags": {
                "hidden-title": "[parameters('dashboardName')]"
            }
        }
    ]
}
```

現在您已看過使用參數化範本部署儀表板的範例，您可以嘗試使用 [AZURE RESOURCE MANAGER REST api](/rest/api/)、 [Azure CLI](/cli/azure)或 [Azure PowerShell 命令](/powershell/azure/get-started-azureps)來部署範本。
