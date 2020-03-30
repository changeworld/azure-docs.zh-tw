---
title: Azure AD 連接雲預配轉換
description: 本文介紹如何使用轉換來更改預設屬性映射。
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: ec12927b40096b7ff04fae6b7cbc69a7bc11e8f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549290"
---
# <a name="transformations"></a>轉換

通過轉換，可以使用雲預配更改屬性與 Azure 活動目錄 （Azure AD） 同步方式的預設行為。

要執行此任務，您需要編輯架構，然後通過 Web 請求重新提交它。

有關雲預配屬性的詳細資訊，請參閱[瞭解 Azure AD 架構](concept-attributes.md)。


## <a name="retrieve-the-schema"></a>檢索架構
若要檢索架構，請按照[查看架構 中](concept-attributes.md#view-the-schema)的步驟操作。 

## <a name="custom-attribute-mapping"></a>自訂屬性對應
要添加自訂屬性對應，請按照以下步驟操作。

1. 將架構複製到文本或代碼編輯器（如[Visual Studio 代碼](https://code.visualstudio.com/)）。
1. 在架構中查找要更新的物件。

   ![架構中的物件](media/how-to-transformation/transform1.png)</br>
1. 找到使用者物件`ExtensionAttribute3`下的代碼。

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. 編輯代碼，以便公司屬性對應到`ExtensionAttribute3`。

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. 將架構複製回圖形資源管理器，將**請求類型**更改為**PUT，** 然後選擇 **"執行查詢**"。

    ![執行查詢](media/how-to-transformation/transform2.png)

 1. 現在，在 Azure 門戶中，轉到雲預配配置並選擇 **"重新開機預配**"。

    ![重新開機預配](media/how-to-transformation/transform3.png)

 1. 稍等一段時間後，通過在圖形資源管理器中運行以下查詢來驗證正在填充屬性： `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`。
 1. 現在，您應該看到該值。

    ![顯示該值](media/how-to-transformation/transform4.png)

## <a name="custom-attribute-mapping-with-function"></a>使用函數的自訂屬性對應
對於更高級的映射，可以使用允許您運算元據並為屬性創建值以滿足組織需求的函數。

要執行此任務，請按照前面的步驟操作，然後編輯用於構造最終值的函數。

有關運算式的語法和示例的資訊，請參閱[在 Azure 活動目錄中編寫屬性對應的運算式](reference-expressions.md)。


## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是 Azure AD Connect 雲端佈建？](what-is-cloud-provisioning.md)
