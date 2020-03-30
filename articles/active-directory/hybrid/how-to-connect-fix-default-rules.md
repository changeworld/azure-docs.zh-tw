---
title: 如何修復修改後的預設規則 - Azure AD 連接 |微軟文檔
description: 瞭解如何修復 Azure AD 連接附帶的修改的預設規則。
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4626e0149028a140d143fb8d0969a03b732201fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036987"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>修復 Azure AD 連接中修改的預設規則

Azure 活動目錄 （Azure AD） 連接使用預設規則進行同步。  遺憾的是，這些規則並不普遍適用于所有組織。 根據您的要求，您可能需要修改它們。 本文討論了兩個最常見的自訂示例，並解釋了實現這些自訂的正確方法。

>[!NOTE] 
> 不支援修改現有預設規則以實現所需的自訂。 如果這樣做，它可以防止將這些規則更新為將來版本中的最新版本。 您不會獲得所需的 Bug 修復或新功能。 本文檔說明如何在不修改現有預設規則的情況下實現相同的結果。 

## <a name="how-to-identify-modified-default-rules"></a>如何識別修改後的預設規則
從 Azure AD Connect 的版本 1.3.7.0 開始，很容易識別修改後的預設規則。 轉到**桌面上的應用**，然後選擇**同步規則編輯器**。

![Azure AD 連接，同步規則編輯器突出顯示](media/how-to-connect-fix-default-rules/default1.png)

在編輯器中，任何修改的預設規則都顯示在名稱前面，並帶有一個警告圖示。

![警告圖示](media/how-to-connect-fix-default-rules/default2.png)

 旁邊也會顯示同名的禁用規則（這是標準預設規則）。

![同步規則編輯器，顯示標準預設規則和修改的預設規則](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>常見自訂項目
以下是預設規則的常見自訂項：

- 更改屬性流
- 更改範圍篩選器
- 更改聯結條件

在更改任何規則之前：

- 禁用同步計畫程式。 排程器 預設會每隔 30 分鐘執行一次。 在進行更改和排除新規則故障時，請確保它未啟動。 要暫時禁用計畫程式，請啟動 PowerShell，然後`Set-ADSyncScheduler -SyncCycleEnabled $false`運行 。
 ![PowerShell 命令禁用同步計畫程式](media/how-to-connect-fix-default-rules/default3.png)

- 範圍篩選器的更改可能導致刪除目標目錄中的物件。 在對物件範圍進行任何更改之前，請小心。 我們建議您在活動伺服器上進行更改之前對暫存伺服器進行更改。
- 在添加任何新規則後，在單個物件上運行預覽，如["驗證同步規則"](#validate-sync-rule)部分所述。
- 添加新規則或修改任何自訂同步規則後運行完全同步。 此同步對所有物件應用新規則。

## <a name="change-attribute-flow"></a>更改屬性流
有三種不同的方案用於更改屬性流：
- 添加新屬性。
- 重寫現有屬性的值。
- 選擇不同步現有屬性。

無需更改標準預設規則即可執行此操作。

### <a name="add-a-new-attribute"></a>添加新屬性
如果發現屬性未從原始目錄流向目標目錄，請使用[Azure AD 連接同步：目錄擴展](how-to-connect-sync-feature-directory-extensions.md)來解決此問題。

如果擴展不適合您，請嘗試添加兩個新的同步規則，在以下各節仲介紹。


#### <a name="add-an-inbound-sync-rule"></a>添加入站同步規則
入站同步規則表示屬性的源是連接器空間，目標是元宇宙。 例如，要有從本地活動目錄到 Azure 活動目錄的新屬性流，請創建新的入站同步規則。 啟動**同步規則編輯器**，選擇 **"入站**"作為方向，然後選擇 **"添加新規則**"。 

 ![同步處理規則編輯器](media/how-to-connect-fix-default-rules/default3a.png)

按照您自己的命名約定命名規則。 在這裡， 我們使用**從 AD - 使用者 的自訂輸入**. 這意味著該規則是一個自訂規則，並且是從 Active Directory 連接器空間到元verse的入站規則。   

 ![建立輸入同步處理規則](media/how-to-connect-fix-default-rules/default3b.png)

提供您自己的規則描述，以便將來輕鬆維護規則。 例如，描述可以基於規則的目標以及需要它的原因。

為**連接的系統**、**連接的系統物件類型**和**元反物件類型**欄位進行選擇。

指定從 0 到 99 的優先順序值（數位越低，優先順序越高）。 對於**標記**、**啟用密碼同步**欄位和**禁用**欄位，請使用預設選擇。

保持**範圍篩選器**為空。 這意味著該規則適用于在活動目錄連接系統和元宇宙中聯接的所有物件。

將 **"聯接"規則**保留為空。 這意味著此規則使用標準預設規則中定義的聯結條件。 這是不禁用或刪除標準預設規則的另一個原因。 如果沒有聯結條件，則屬性不會流動。 

為您的屬性添加適當的轉換。 您可以分配常量，以使常量值流到目標屬性。 可以使用源屬性或目標屬性之間的直接映射。 或者，可以使用屬性的運算式。 下面是可以使用的各種[運算式函數](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference)。

#### <a name="add-an-outbound-sync-rule"></a>添加出站同步規則
要將屬性連結到目標目錄，需要創建出站規則。 這意味著源是元，目標是連接的系統。 要創建出站規則，請啟動**同步規則編輯器**，將**方向**更改為**出站**，然後選擇 **"添加新規則**"。 

![同步處理規則編輯器](media/how-to-connect-fix-default-rules/default3c.png)

與入站規則一樣，您可以使用自己的命名約定來命名規則。 選擇 **"已連接的系統**"作為 Azure AD 租戶，然後選擇要向其設置屬性值的連接系統物件。 將優先順序設置為 0 到 99。 

![建立輸出同步處理規則](media/how-to-connect-fix-default-rules/default3d.png)

保持**範圍篩選器**和**聯接規則**為空。 將轉換填報為常量、直接或運算式。 

現在，您知道如何為使用者物件從活動目錄流向 Azure 活動目錄創建新屬性。 可以使用這些步驟將任何屬性從任何物件映射到源和目標。 有關詳細資訊，請參閱[創建自訂同步規則](how-to-connect-create-custom-sync-rule.md)[並準備預配使用者](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)。

### <a name="override-the-value-of-an-existing-attribute"></a>覆蓋現有屬性的值
您可能希望重寫已映射的屬性的值。 例如，如果始終要將 null 值設置為 Azure AD 中的屬性，則只需僅創建入站規則即可。 使常量值`AuthoritativeNull`， 流向目標屬性。 

>[!NOTE] 
> 在這種情況下`AuthoritativeNull`，請使用`Null`而不是。 這是因為非空值替換 null 值，即使它具有較低的優先順序（規則中的數位值較高）。 `AuthoritativeNull`另一方面，其他規則不會替換為非空值。 

### <a name="dont-sync-existing-attribute"></a>不同步現有屬性
如果要從同步中排除屬性，請使用 Azure AD 連接中提供的屬性篩選功能。 從桌面圖示啟動**Azure AD 連接**，然後選擇 **"自訂同步選項**"。

![Azure AD 連接其他任務選項](media/how-to-connect-fix-default-rules/default4.png)

 確保選擇了**Azure AD 應用和屬性篩選**，然後選擇 **"下一步**"。

![Azure AD 連接可選功能](media/how-to-connect-fix-default-rules/default5.png)

清除要從同步中排除的屬性。

![Azure AD 連接屬性](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>更改範圍篩選器
Azure AD 同步可處理大多數物件。 您可以減小物件的範圍，並減少要匯出的物件數，而無需更改標準預設同步規則。 

使用以下方法之一來縮小要同步的物件的範圍：

- 雲篩選屬性
- 組織單位篩選

如果縮小要同步的使用者的範圍，則篩選出使用者的密碼雜湊同步也會停止。 如果物件已同步，則在縮小範圍後，篩選出的物件將從目標目錄中刪除。 因此，請確保您的範圍非常仔細。

>[!IMPORTANT] 
> 不建議增加 Azure AD Connect 配置的物件範圍。 這樣做會使 Microsoft 支援小組難以理解這些自訂項。 如果必須增加物件的範圍，請編輯現有規則，克隆它，並禁用原始規則。 

### <a name="cloudfiltered-attribute"></a>雲篩選屬性
無法在活動目錄中設置此屬性。 通過添加新入站規則來設置此屬性的值。 然後，可以使用**變換**和**運算式**在元節中設置此屬性。 下面的示例顯示，您不希望同步其部門名稱以**HRD**開頭的所有使用者（不區分大小寫）：

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

我們首先將部門從源（活動目錄）轉換為小寫。 然後，使用`Left`函數，我們只取了前三個字元，並將其與`hrd`進行比較。 如果匹配，則該值將設置為`True`，否則`NULL`。 在將值設置為 null 時，其他優先順序較低的規則（數位值越高）可以用不同的條件寫入該值。 在一個物件上運行預覽以驗證同步規則，如["驗證同步規則](#validate-sync-rule)"部分所述。

![創建入站同步規則選項](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>組織單位篩選
您可以創建一個或多個組織單位 （O），並將不想同步的物件移動到這些單位。 然後，在 Azure AD 連接中配置 OU 篩選。 從桌面圖示啟動**Azure AD 連接**，然後選擇以下選項。 您還可以在安裝 Azure AD 連接時配置 OU 篩選。 

![Azure AD 連接其他任務](media/how-to-connect-fix-default-rules/default8.png)

請按照嚮導操作，清除不想同步的 O。

![Azure AD 連接域和 OU 篩選選項](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>更改聯結條件
使用 Azure AD 連接配置的預設聯結條件。 更改預設聯結條件使 Microsoft 支援人員難以理解自訂項並支援產品。

## <a name="validate-sync-rule"></a>驗證同步規則
您可以使用預覽功能驗證新添加的同步規則，而無需運行完整的同步週期。 在 Azure AD 連接中，選擇**同步服務**。

![Azure AD 連接，同步服務突出顯示](media/how-to-connect-fix-default-rules/default10.png)

選擇**元搜索**。 選擇範圍物件作為**人**，選擇 **"添加子句**"，並提及搜尋條件。 接下來，選擇 **"搜索**"，然後按兩下搜尋結果中的物件。 通過在運行此步驟之前在林上運行導入和同步，確保 Azure AD Connect 中的資料是該物件的最新資料。

![同步處理服務管理員](media/how-to-connect-fix-default-rules/default11.png)

在**Metaverse 物件屬性**上，選擇 **"連接器**"，在相應的連接器（林）中選擇物件，然後選擇**屬性..."**

![Metaverse 物件屬性](media/how-to-connect-fix-default-rules/default12.png)

選擇**預覽..."**

![連接器空間物件屬性](media/how-to-connect-fix-default-rules/default13a.png)

在"預覽"視窗中，在左側窗格中選擇 **"生成預覽**和**導入屬性流**"。

![預覽](media/how-to-connect-fix-default-rules/default14.png)
 
在這裡，請注意新添加的規則在物件上運行，並將`cloudFiltered`屬性設置為 true。

![預覽](media/how-to-connect-fix-default-rules/default15a.png)
 
要將修改後的規則與預設規則進行比較，請將這兩個規則分別匯出為文字檔。 這些規則匯出為 PowerShell 指令檔。 您可以使用任何檔比較工具（例如 windiff）來比較它們以查看更改。 
 
請注意，在修改後的規則中，`msExchMailboxGuid`屬性將更改為**運算式**類型，而不是**Direct**。 此外，該值將更改為**Null**和 **"執行一次"** 選項。 您可以忽略"已識別"和"優先順序"差異。 

![溫蒂法工具輸出](media/how-to-connect-fix-default-rules/default17.png)
 
要修復規則以將其更改回預設設置，請刪除修改後的規則並啟用預設規則。 確保不會丟失要實現的自訂項。 準備就緒後，運行**完全同步**。

## <a name="next-steps"></a>後續步驟
- [硬體和先決條件](how-to-connect-install-prerequisites.md) 
- [快速設定](how-to-connect-install-express.md)
- [自訂設定](how-to-connect-install-custom.md)



