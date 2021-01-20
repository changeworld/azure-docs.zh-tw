---
title: Azure AD Connect 雲端同步轉換
description: 本文描述如何使用轉換來改變預設屬性對應。
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 1c81ee0ebace65e50cdab5b5b223d5e5eae4ff9a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613104"
---
# <a name="transformations"></a>轉換

透過轉換，您可以使用雲端同步處理，來變更屬性與 Azure Active Directory (Azure AD) 的同步處理方式的預設行為。

若要進行這項工作，您需要編輯架構，然後透過 web 要求重新提交。

如需有關雲端同步屬性的詳細資訊，請參閱 [瞭解 Azure AD 架構](concept-attributes.md)。


## <a name="retrieve-the-schema"></a>取出架構
若要抓取架構，請依照 [查看架構](concept-attributes.md#view-the-schema)中的步驟執行。 

## <a name="custom-attribute-mapping"></a>自訂屬性對應
若要加入自訂屬性對應，請遵循下列步驟。

1. 將架構複製到文字或程式碼編輯器（例如 [Visual Studio Code](https://code.visualstudio.com/)）。
1. 在架構中找出您想要更新的物件。

   ![架構中的物件](media/how-to-transformation/transform-1.png)</br>
1. `ExtensionAttribute3`在使用者物件下找出程式碼。

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
1. 編輯程式碼，使 company 屬性對應到 `ExtensionAttribute3` 。

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
 1. 將架構複製回 Graph Explorer、將 **要求類型** 變更為 **PUT**，然後選取 [ **執行查詢**]。

    ![執行查詢](media/how-to-transformation/transform-2.png)

 1. 現在，在 Azure 入口網站中，移至雲端同步處理設定，然後選取 [ **重新開機** 布建]。

    ![重新開機布建](media/how-to-transformation/transform-3.png)

 1. 經過一小段時間之後，請在 Graph Explorer 中執行下列查詢，以確認屬性已填入： `https://graph.microsoft.com/beta/users/{Azure AD user UPN}` 。
 1. 您現在應該會看到值。

    ![值隨即出現](media/how-to-transformation/transform-4.png)

## <a name="custom-attribute-mapping-with-function"></a>使用函數的自訂屬性對應
如需更先進的對應，您可以使用可讓您運算元據的函式，並為屬性建立值，以符合您組織的需求。

若要執行此工作，請遵循先前的步驟，然後編輯用來建立最終值的函式。

如需運算式語法和範例的詳細資訊，請參閱 [在 Azure Active Directory 中撰寫屬性對應的運算式](reference-expressions.md)。


## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [什麼是雲端同步 Azure AD Connect？](what-is-cloud-sync.md)
