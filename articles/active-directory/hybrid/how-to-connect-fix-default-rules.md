---
title: 如何修正修改過的預設規則-Azure AD Connect |Microsoft Docs
description: 瞭解如何修正 Azure AD Connect 隨附的已修改預設規則。
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd16dd5a20a677568c928f805c1aaa5f2c222f24
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279936"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>修正 Azure AD Connect 中修改過的預設規則

Azure Active Directory (Azure AD) Connect 會使用預設的同步處理規則。  可惜的是，這些規則不會通用於所有組織。 根據您的需求而定，您可能需要加以修改。 本文討論兩個最常見的自訂範例，並說明完成這些自訂的正確方式。

>[!NOTE] 
> 不支援修改現有的預設規則以達成所需的自訂。 如果您這樣做，它會防止在未來的版本中將這些規則更新為最新版本。 您將不會收到所需的 bug 修正或新功能。 本檔說明如何達到相同的結果，而不需要修改現有的預設規則。 

## <a name="how-to-identify-modified-default-rules"></a>如何識別修改過的預設規則
從 Azure AD Connect 版的1.3.7.0 開始，很容易就能識別修改過的預設規則。 移至 **桌面上的 [應用程式**]，然後選取 **[同步處理規則編輯器**]。

![Azure AD Connect，反白顯示同步處理規則編輯器](media/how-to-connect-fix-default-rules/default1.png)

在編輯器中，任何修改過的預設規則都會顯示在名稱前面的警告圖示。

![警告圖示](media/how-to-connect-fix-default-rules/default2.png)

 它旁邊也會出現一個停用的規則， (這是標準的預設規則) 。

![同步處理規則編輯器，顯示標準預設規則和修改過的預設規則](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>常見自訂項目
以下是預設規則的常見自訂：

- 變更屬性流程
- 變更範圍篩選準則
- 變更聯結條件

變更任何規則之前：

- 停用同步排程器。 排程器 預設會每隔 30 分鐘執行一次。 當您進行變更並針對新規則進行疑難排解時，請確定它並未啟動。 若要暫時停用排程器，請啟動 PowerShell，然後執行 `Set-ADSyncScheduler -SyncCycleEnabled $false` 。
 ![用來停用同步排程器的 PowerShell 命令](media/how-to-connect-fix-default-rules/default3.png)

- 範圍篩選器的變更可能會導致刪除目標目錄中的物件。 在對物件的範圍進行任何變更之前，請務必小心。 建議您先對預備伺服器進行變更，然後才在使用中伺服器上進行變更。
- 在新增任何新規則之後，在單一物件上執行預覽，如「 [驗證同步規則](#validate-sync-rule) 」一節中所述。
- 新增新規則或修改任何自訂同步規則之後，請執行完整同步處理。 此同步會將新規則套用至所有物件。

## <a name="change-attribute-flow"></a>變更屬性流程
有三種不同的變更屬性流程案例：
- 加入新的屬性。
- 覆寫現有屬性的值。
- 選擇不同步現有的屬性。

您可以在不變更標準預設規則的情況下進行這些動作。

### <a name="add-a-new-attribute"></a>新增屬性
如果您發現某個屬性不是從來原始目錄流向目標目錄，請使用 [Azure AD Connect 同步：目錄延伸](how-to-connect-sync-feature-directory-extensions.md) 來修正此問題。

如果延伸模組無法運作，請嘗試新增兩個新的同步處理規則，如以下各節所述。


#### <a name="add-an-inbound-sync-rule"></a>新增輸入同步處理規則
輸入同步處理規則表示屬性的來源是連接器空間，而目標是元處理。 例如，若要讓內部部署的新屬性流程 Active Directory 至 Azure Active Directory，請建立新的輸入同步處理規則。 啟動 [ **同步處理規則編輯器**]，選取 [ **輸入** ] 做為方向，然後選取 [ **新增規則**]。 

 ![同步處理規則編輯器](media/how-to-connect-fix-default-rules/default3a.png)

遵循您自己的命名慣例來命名規則。 在這裡，我們會使用 **來自 AD 使用者的自訂**。 這表示規則是自訂規則，而且是從 Active Directory 連接器空間到元系的輸入規則。   

 ![建立輸入同步處理規則](media/how-to-connect-fix-default-rules/default3b.png)

提供規則的描述，讓規則的未來維護工作變得很簡單。 例如，描述可以根據規則的目標，以及其需要的原因。

針對 [ **連接的系統**]、[ **連接的系統物件類型**] 和 [ **元網物件類型** ] 欄位進行選取。

指定從0到99的優先順序值 (數位越低，優先順序) 越高。 針對 [ **標記**]、[ **啟用密碼同步**] 和 [ **已停用** ] 欄位，請使用預設選項。

讓 **範圍篩選** 保持空白。 這表示規則會套用至 Active Directory 連接系統和元節之間聯結的所有物件。

將 **聯結規則** 保持空白。 這表示此規則會使用標準預設規則中所定義的聯結條件。 這是不停用或刪除標準預設規則的另一個原因。 如果沒有聯結條件，屬性將不會流動。 

為您的屬性新增適當的轉換。 您可以指派常數，將常數值流至目標屬性。 您可以使用來源或目標屬性之間的直接對應。 或者，您可以使用屬性的運算式。 以下是您可以使用的各種 [運算式函數](./reference-connect-sync-functions-reference.md) 。

#### <a name="add-an-outbound-sync-rule"></a>新增輸出同步處理規則
若要將屬性連結至目標目錄，您必須建立輸出規則。 這表示來源為元網，而目標是連接的系統。 若要建立輸出規則，請啟動 [ **同步處理規則編輯器**]，將 **方向** 變更為 [ **輸出**]，然後選取 [ **新增規則**]。 

![同步處理規則編輯器](media/how-to-connect-fix-default-rules/default3c.png)

如同輸入規則，您可以使用自己的命名慣例來命名規則。 選取 **連線的系統** 作為 Azure AD 租使用者，然後選取您要設定屬性值的已連接系統物件。 設定0到99之間的優先順序。 

![建立輸出同步處理規則](media/how-to-connect-fix-default-rules/default3d.png)

讓 **範圍篩選器** 和 **聯結規則** 保持空白。 以常數、direct 或 expression 的形式填入轉換。 

您現在知道如何為使用者物件流程建立新的屬性，從 Active Directory 到 Azure Active Directory。 您可以使用這些步驟，將任何物件的任何屬性對應到來源和目標。 如需詳細資訊，請參閱 [建立自訂同步處理規則](how-to-connect-create-custom-sync-rule.md) 和準備布建 [使用者](/office365/enterprise/prepare-for-directory-synchronization)。

### <a name="override-the-value-of-an-existing-attribute"></a>覆寫現有屬性的值
您可能會想要覆寫已對應之屬性的值。 例如，如果您一律想要將 null 值設定為 Azure AD 中的屬性，只需建立輸入規則。 將常數值（ `AuthoritativeNull` ）傳送至目標屬性。 

>[!NOTE] 
> `AuthoritativeNull`在此情況下，請使用而不是 `Null` 。 這是因為非 null 值會取代 null 值，即使它的優先順序較低 (規則) 中的數位值也比較低。 `AuthoritativeNull`另一方面，其他規則並不會以非 null 值取代。 

### <a name="dont-sync-existing-attribute"></a>不要同步現有的屬性
如果您想要排除屬性不同步，請使用 Azure AD Connect 中提供的屬性篩選功能。 從桌面圖示啟動 **Azure AD Connect** ，然後選取 [ **自訂同步處理選項**]。

![Azure AD Connect 其他工作選項](media/how-to-connect-fix-default-rules/default4.png)

 確定已選取 **Azure AD 應用程式和屬性篩選** ，然後選取 **[下一步]**。

![Azure AD Connect 選用功能](media/how-to-connect-fix-default-rules/default5.png)

清除您要排除不同步的屬性。

![Azure AD Connect 屬性](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>變更範圍篩選準則
Azure AD 同步負責處理大部分的物件。 您可以減少物件的範圍，並減少要匯出的物件數目，而不需要變更標準預設同步處理規則。 

您可以使用下列其中一種方法來減少您要同步處理的物件範圍：

- cloudFiltered 屬性
- 組織單位篩選

如果您減少要同步處理之使用者的範圍，系統也會針對已篩選的使用者停止密碼雜湊同步處理。 如果物件已在同步中，則在您縮小範圍之後，會從目標目錄中刪除篩選出的物件。 基於這個理由，請務必謹慎地界定範圍。

>[!IMPORTANT] 
> 不建議您增加 Azure AD Connect 設定的物件範圍。 這麼做會讓 Microsoft 支援小組難以瞭解自訂專案。 如果您必須增加物件的範圍，請編輯現有的規則、複製它，然後停用原始規則。 

### <a name="cloudfiltered-attribute"></a>cloudFiltered 屬性
您無法在 Active Directory 中設定此屬性。 藉由加入新的輸入規則來設定這個屬性的值。 然後，您可以使用 **轉換** 和 **運算式** 在元組中設定這個屬性。 下列範例顯示您不想要同步處理其部門名稱以 **HRD** 開頭的所有使用者 (不區分大小寫的) ：

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

我們先將部門從來源 (Active Directory) 轉換為小寫。 然後，使用函式 `Left` 時，我們只會使用前三個字元，並將其與進行比較 `hrd` 。 如果相符，則會將值設為 `True` ，否則為 `NULL` 。 在將值設定為 null 時，其他具有較低優先順序的規則 (較高的數值) 可以用不同的條件來寫入。 在一個物件上執行預覽，以驗證同步處理規則，如 [驗證同步處理規則](#validate-sync-rule) 一節中所述。

![建立輸入同步處理規則選項](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>組織單位篩選
您可以)  (Ou 建立一或多個組織單位，並將您不想要同步處理的物件移至這些 Ou。 然後，在 Azure AD Connect 中設定 OU 篩選。 從桌面圖示啟動 **Azure AD Connect** ，然後選取下列選項。 您也可以在安裝 Azure AD Connect 時，設定 OU 篩選。 

![Azure AD Connect 其他工作](media/how-to-connect-fix-default-rules/default8.png)

遵循嚮導，並清除您不想要同步的 Ou。

![Azure AD Connect 網域和 OU 篩選選項](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>變更聯結條件
使用 Azure AD Connect 所設定的預設聯結條件。 變更預設的聯結條件，讓 Microsoft 支援人員難以瞭解自訂專案並支援產品。

## <a name="validate-sync-rule"></a>驗證同步處理規則
您可以使用預覽功能來驗證新增的同步處理規則，而不需要執行完整同步處理迴圈。 在 Azure AD Connect 中，選取 **[同步處理服務**]。

![Azure AD Connect，反白顯示同步處理服務](media/how-to-connect-fix-default-rules/default10.png)

選取 [ **元節搜尋**]。 選取 [領域] 物件做為 **person**、選取 [ **新增子句**]，然後提及您的搜尋條件。 接下來，選取 [ **搜尋**]，然後按兩下搜尋結果中的物件。 執行此步驟之前，請先在樹系上執行匯入和同步處理，以確定該物件在 Azure AD Connect 中的資料是最新的。

![同步處理服務管理員](media/how-to-connect-fix-default-rules/default11.png)

在 [ **元屬性] 物件屬性**上選取 [ **連接器**]，並選取對應連接器中的物件 (樹系) ，然後選取 [ **屬性 ...**]。

![Metaverse 物件屬性](media/how-to-connect-fix-default-rules/default12.png)

選取 **預覽 ...**

![連接器空間物件屬性](media/how-to-connect-fix-default-rules/default13a.png)

在 [預覽] 視窗中，選取左窗格中的 [ **產生預覽** 並匯 **入屬性流程** ]。

![預覽](media/how-to-connect-fix-default-rules/default14.png)
 
請注意，新加入的規則會在物件上執行，並將 `cloudFiltered` 屬性設定為 true。

![預覽](media/how-to-connect-fix-default-rules/default15a.png)
 
若要比較修改過的規則與預設規則，請將這兩個規則分別匯出為文字檔。 這些規則會匯出為 PowerShell 腳本檔案。 您可以使用任何檔案比較工具來比較它們 (例如，windiff) 來查看變更。 
 
請注意，在修改過的規則中， `msExchMailboxGuid` 屬性會變更為 **運算式** 類型，而不是 **直接**的。 此外，此值會變更為 **Null** 和 **ExecuteOnce** 選項。 您可以忽略已識別和優先順序的差異。 

![windiff 工具輸出](media/how-to-connect-fix-default-rules/default17.png)
 
若要修正規則以將其變更回預設設定，請刪除修改過的規則，並啟用預設規則。 確定您不會遺失您嘗試達成的自訂。 當您準備好時，請執行 **完整同步**處理。

## <a name="next-steps"></a>接下來的步驟
- [硬體和先決條件](how-to-connect-install-prerequisites.md) 
- [快速設定](how-to-connect-install-express.md)
- [自訂設定](how-to-connect-install-custom.md)