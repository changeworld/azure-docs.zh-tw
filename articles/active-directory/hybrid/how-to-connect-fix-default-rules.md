---
title: 如何修正修改過的預設規則-Azure AD Connect |Microsoft Docs
description: 瞭解如何修正 Azure AD Connect 隨附的修改過的預設規則。
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
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "79036987"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>修正 Azure AD Connect 中修改過的預設規則

Azure Active Directory （Azure AD） Connect 會使用預設規則進行同步處理。  可惜的是，這些規則並不會通用於所有組織。 根據您的需求，您可能需要加以修改。 本文討論最常見自訂的兩個範例，並說明達成這些自訂的正確方式。

>[!NOTE] 
> 不支援修改現有的預設規則以達到所需的自訂專案。 如果您這樣做，它會防止在未來版本中將這些規則更新為最新版本。 您不會收到所需的 bug 修正或新功能。 本檔說明如何在不修改現有預設規則的情況下，達到相同的結果。 

## <a name="how-to-identify-modified-default-rules"></a>如何識別已修改的預設規則
從 Azure AD Connect 的版本1.3.7.0 開始，很容易就能識別已修改的預設規則。 前往桌面上的 [**應用程式**]，然後選取 **[同步處理規則編輯器**]。

![Azure AD Connect，反白顯示 [同步處理規則編輯器]](media/how-to-connect-fix-default-rules/default1.png)

在編輯器中，任何修改過的預設規則都會顯示在名稱前面加上警告圖示。

![警告圖示](media/how-to-connect-fix-default-rules/default2.png)

 名稱旁邊的已停用規則也會出現（這是標準的預設規則）。

![同步處理規則編輯器，顯示標準預設規則和修改的預設規則](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>一般自訂
以下是預設規則的一般自訂：

- 變更屬性流程
- 變更範圍篩選器
- 變更聯結條件

在您變更任何規則之前：

- 停用同步排程器。 排程器預設會每隔30分鐘執行一次。 當您進行變更並針對新規則進行疑難排解時，請確定它並未啟動。 若要暫時停用排程器，請啟動 PowerShell，然後執行 `Set-ADSyncScheduler -SyncCycleEnabled $false`。
 ![PowerShell 命令來停用同步排程器](media/how-to-connect-fix-default-rules/default3.png)

- 範圍篩選器中的變更可能會導致刪除目標目錄中的物件。 在物件的範圍內進行任何變更之前，請先小心。 我們建議您在使用中伺服器上進行變更之前，先對預備伺服器進行變更。
- 在新增任何新規則之後，在單一物件上執行預覽，如[驗證同步處理規則](#validate-sync-rule)一節中所述。
- 新增新規則或修改任何自訂同步處理規則之後，執行完整同步處理。 這個同步處理會將新的規則套用至所有物件。

## <a name="change-attribute-flow"></a>變更屬性流程
變更屬性流程有三種不同的情況：
- 加入新的屬性。
- 覆寫現有屬性的值。
- 選擇不要同步現有的屬性。

您可以在不改變標準預設規則的情況下執行這些動作。

### <a name="add-a-new-attribute"></a>加入新的屬性
如果您發現某個屬性未從來原始目錄流向目標目錄，請使用[Azure AD Connect sync：目錄延伸](how-to-connect-sync-feature-directory-extensions.md)模組來修正此問題。

如果延伸模組無法供您使用，請嘗試新增兩個新的同步處理規則，如下列各節所述。


#### <a name="add-an-inbound-sync-rule"></a>新增輸入同步處理規則
輸入同步處理規則表示屬性的來源是連接器空間，而目標則是元處理。 例如，若要從內部部署 Active Directory 到 Azure Active Directory 的新屬性流程，請建立新的輸入同步處理規則。 啟動 [**同步處理規則編輯器**]，選取 [**輸入**] 做為方向，然後選取 [**新增規則**]。 

 ![同步處理規則編輯器](media/how-to-connect-fix-default-rules/default3a.png)

遵循您自己的命名慣例來命名規則。 在這裡，我們使用**來自 AD-User 的自訂**。 這表示規則是自訂規則，而是從 Active Directory 連接器空間到元節的輸入規則。   

 ![建立輸入同步處理規則](media/how-to-connect-fix-default-rules/default3b.png)

提供規則的描述，讓規則的未來維護變得很容易。 例如，描述可以根據規則的目標，以及為何需要它。

針對 [**已連接的系統**]、[**連接的系統] 物件類型**和 [**元對應物件類型**] 欄位進行選擇。

指定從0到99（數位越低，優先順序愈高）的優先順序值。 針對 [**標記**]、[**啟用密碼同步**] 和 [**已停用**] 欄位，請使用預設選項。

將 [**範圍篩選器**] 保留為空白。 這表示規則會套用至 Active Directory 連接的系統和元節之間聯結的所有物件。

將**聯結規則**保留空白。 這表示此規則使用標準預設規則中所定義的聯結條件。 這是不停用或刪除標準預設規則的另一個原因。 如果沒有聯結條件，屬性將不會流動。 

為您的屬性新增適當的轉換。 您可以指派常數，讓常數值流向您的目標屬性。 您可以在來源或目標屬性之間使用直接對應。 或者，您可以將運算式用於屬性。 以下是您可以使用的各種[運算式函數](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference)。

#### <a name="add-an-outbound-sync-rule"></a>新增輸出同步處理規則
若要將屬性連結至目標目錄，您必須建立輸出規則。 這表示來源是元（），而目標是連接的系統。 若要建立輸出規則，請啟動 [**同步處理規則編輯器**]，將**方向**變更為 [**輸出**]，然後選取 [**新增規則**]。 

![同步處理規則編輯器](media/how-to-connect-fix-default-rules/default3c.png)

如同輸入規則，您可以使用自己的命名慣例來命名規則。 選取 [**已連線的系統**] 做為 [Azure AD 租使用者]，然後選取您要設定其屬性值的已連接系統物件。 將優先順序從0到99。 

![建立輸出同步處理規則](media/how-to-connect-fix-default-rules/default3d.png)

將**範圍篩選準則**和**聯結規則**保留空白。 將轉換填入常數、直接或運算式。 

您現在知道如何為使用者物件流程建立新的屬性，從 Active Directory 到 Azure Active Directory。 您可以使用這些步驟，將任何物件的任何屬性對應至來源和目標。 如需詳細資訊，請參閱[建立自訂同步處理規則](how-to-connect-create-custom-sync-rule.md)和準備布建[使用者](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)。

### <a name="override-the-value-of-an-existing-attribute"></a>覆寫現有屬性的值
您可能想要覆寫已經對應之屬性的值。 例如，如果您一律要將 null 值設定為 Azure AD 中的屬性，只需建立輸入規則。 讓常數值 `AuthoritativeNull`，流向目標屬性。 

>[!NOTE] 
> 在此情況下，請使用 `AuthoritativeNull`，而不是 `Null`。 這是因為非 null 值會取代 null 值，即使它具有較低的優先順序（規則中較高的數值）。 另一方面，`AuthoritativeNull`則不會由其他規則取代為非 null 值。 

### <a name="dont-sync-existing-attribute"></a>不要同步現有的屬性
如果您想要將屬性從同步中排除，請使用 Azure AD Connect 中提供的屬性篩選功能。 從桌面圖示啟動**Azure AD Connect** ，然後選取 [**自訂同步處理選項**]。

![Azure AD Connect 其他工作選項](media/how-to-connect-fix-default-rules/default4.png)

 請確定已選取**Azure AD 應用程式和屬性篩選**，然後選取 **[下一步]** 。

![Azure AD Connect 選用功能](media/how-to-connect-fix-default-rules/default5.png)

清除您要從同步排除的屬性。

![Azure AD Connect 屬性](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>變更範圍篩選器
Azure AD 同步會負責處理大部分的物件。 您可以減少物件的範圍，並減少要匯出的物件數目，而不需要變更標準預設同步處理規則。 

使用下列其中一種方法來減少您要同步之物件的範圍：

- cloudFiltered 屬性
- 組織單位篩選

如果您減少要同步處理的使用者範圍，則密碼雜湊同步也會針對已篩選的使用者停止。 如果物件已經同步處理，則在您縮減範圍之後，會從目標目錄中刪除已篩選掉的物件。 基於這個理由，請務必小心地進行範圍。

>[!IMPORTANT] 
> 不建議增加 Azure AD Connect 所設定之物件的範圍。 這麼做會讓 Microsoft 支援小組難以瞭解自訂。 如果您必須增加物件的範圍，請編輯現有的規則、複製它，然後停用原始規則。 

### <a name="cloudfiltered-attribute"></a>cloudFiltered 屬性
您無法在 Active Directory 中設定此屬性。 藉由加入新的輸入規則來設定這個屬性的值。 接著，您可以使用**轉換**和**運算式**，在元組中設定這個屬性。 下列範例顯示您不想要將部門名稱開頭為**HRD** （不區分大小寫）的所有使用者進行同步處理：

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

我們先將部門從來源（Active Directory）轉換成小寫。 然後，使用 `Left` 函式，我們只會取得前三個字元，並與 `hrd`進行比較。 如果相符，則值會設定為 `True`，否則 `NULL`。 將值設為 null 時，較低優先順序的其他規則（較高的數位值）可以使用不同的條件來寫入它。 在一個物件上執行預覽，以驗證同步處理規則，如[驗證同步處理規則](#validate-sync-rule)一節中所述。

![建立輸入同步處理規則選項](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>組織單位篩選
您可以建立一或多個組織單位（Ou），並將您不想要同步處理的物件移到這些 Ou。 然後，在 Azure AD Connect 中設定 OU 篩選。 從桌面圖示啟動**Azure AD Connect** ，然後選取下列選項。 您也可以在安裝 Azure AD Connect 時設定 OU 篩選。 

![Azure AD Connect 其他工作](media/how-to-connect-fix-default-rules/default8.png)

依照嚮導的指示，清除您不想要同步處理的 Ou。

![Azure AD Connect 網域和 OU 篩選選項](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>變更聯結條件
使用 Azure AD Connect 所設定的預設聯結條件。 變更預設的聯結條件，讓 Microsoft 支援人員難以瞭解自訂並支援產品。

## <a name="validate-sync-rule"></a>驗證同步處理規則
您可以使用預覽功能來驗證新增的同步處理規則，而不需執行完整的同步處理迴圈。 在 Azure AD Connect 中，選取**同步處理服務**。

![Azure AD Connect，反白顯示同步處理服務](media/how-to-connect-fix-default-rules/default10.png)

選取 [**元 # 搜尋**]。 選取 [範圍] 物件為 [**人員**]，選取 [**新增子句**]，並提及您的搜尋條件。 接下來，選取 [**搜尋**]，然後在搜尋結果中按兩下物件。 執行此步驟之前，請先執行樹系上的 [匯入] 和 [同步處理]，以確定您 Azure AD Connect 中的資料是最新的。

![同步處理服務管理員](media/how-to-connect-fix-default-rules/default11.png)

在 [**元節物件屬性**] 上，選取 [**連接器**]，選取對應連接器（樹系）中的物件，然後選取 [**屬性 ...** ]。

![Metaverse 物件屬性](media/how-to-connect-fix-default-rules/default12.png)

選取 [**預覽**]

![連接器空間物件屬性](media/how-to-connect-fix-default-rules/default13a.png)

在 [預覽] 視窗中，選取左窗格中的 [**產生預覽**] 和 [匯**入屬性流程**]。

![預覽](media/how-to-connect-fix-default-rules/default14.png)
 
請注意，新加入的規則會在物件上執行，並將 `cloudFiltered` 屬性設定為 true。

![預覽](media/how-to-connect-fix-default-rules/default15a.png)
 
若要比較修改過的規則與預設規則，請將這兩個規則分別匯出為文字檔。 這些規則會匯出為 PowerShell 腳本檔案。 您可以使用任何檔案比較工具（例如 windiff）來比較它們，以查看變更。 
 
請注意，在修改過的規則中，`msExchMailboxGuid` 屬性會變更為**運算式**類型，而不是**直接**。 此外，此值也會變更為**Null**和**ExecuteOnce**選項。 您可以忽略已識別和優先順序的差異。 

![windiff 工具輸出](media/how-to-connect-fix-default-rules/default17.png)
 
若要修正您的規則以將其變更回預設設定，請刪除修改過的規則，並啟用預設規則。 請確定您不會遺失您嘗試達成的自訂。 當您準備好時，請執行**完整同步**處理。

## <a name="next-steps"></a>後續步驟
- [硬體和先決條件](how-to-connect-install-prerequisites.md) 
- [快速設定](how-to-connect-install-express.md)
- [自訂設定](how-to-connect-install-custom.md)



