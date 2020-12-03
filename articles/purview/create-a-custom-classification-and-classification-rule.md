---
title: '建立自訂分類和分類規則 (預覽) '
description: 本文說明如何建立自訂分類，以定義您的組織唯一的資料資產中的資料類型。 它也會說明如何建立自訂分類規則，讓您可以在整個資料資產中尋找指定的資料。
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/1/2020
ms.openlocfilehash: 16a714cff506117c5d6f7fd4921fbd5346bfda39
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552008"
---
# <a name="custom-classifications-in-azure-purview"></a>Azure 範疇中的自訂分類 

本文說明如何建立自訂分類，以定義您的組織唯一的資料資產中的資料類型。 它也會說明如何建立自訂分類規則，讓您可以在整個資料資產中尋找指定的資料。

## <a name="default-classifications"></a>預設分類

Azure 範疇資料目錄提供一組大型的預設分類，代表您的資料資產中可能擁有的一般個人資料類型。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="選取分類" border="true":::

如果任何預設分類都不符合您的需求，您也可以建立自訂分類。

## <a name="steps-to-create-a-custom-classification"></a>建立自訂分類的步驟

若要建立自訂分類，請執行下列動作：

1. 從您的目錄中，選取左側功能表中的 [ **管理中心** ]。

2. 選取 [**中繼資料管理**] 下的 **分類**。

3. 選取 [ **+ 新增**]

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="新增分類" border="true":::

[ **加入新的分類** ] 窗格隨即開啟，您可以在其中提供分類的名稱和描述。 使用名稱間距慣例（例如）是不錯的作法 `your company name.classification name` 。
Microsoft 系統分類會依保留的 `MS.` 命名空間分組。 例如 **MS。政府。我們。社會 \_ 安全 \_ 號碼**。

您分類的名稱必須以字母開頭，後面接著字母、數位和句號的序列 (. ) 或底線字元。
不允許空格。 當您輸入時，UX 會自動產生易記的名稱。 當您將此易記名稱套用至目錄中的資產時，使用者會看到此易記名稱。

為了讓名稱保持簡短，系統會根據下列邏輯建立易記名稱：

- 命名空間的最後兩個區段會被修剪。

- 會調整大小寫，讓每個單字的第一個字母都是大寫。 所有其他字母都會轉換成小寫。

- 所有底線 (\_) 都會以空格取代。

例如，如果您將分類命名為 **CONTOSO.HR。員工 \_ 識別碼**，易記名稱會以 **HR. employee id** 的形式儲存在系統中。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

選取 **[確定]**，就會將新的分類加入至您的分類清單。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="自訂分類" border="true":::

選取清單中的分類會開啟 [分類詳細資料] 頁面。 您可以在這裡找到有關分類的所有詳細資料。
這些詳細資料包括存在的實例數量、正式名稱、相關聯的分類規則 (是否有任何) 和擁有者名稱。

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="選取分類" border="true":::

## <a name="custom-classification-rules"></a>自訂分類規則

目錄服務會提供一組預設分類規則，讓掃描器用來自動偵測特定的資料類型。 您也可以新增自己的自訂分類規則，以偵測您可能想要在資料資產中尋找的其他資料類型。 當您 \' 嘗試在資料資產內尋找資料時，這項功能可能非常強大。

例如，假設 \' 名為 Contoso 的公司具有在整個公司中標準化的員工識別碼，並在後面加上 \" \" GUID 以建立員工 {GUID}。 例如，員工識別碼的一個實例看起來像 EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55。

Contoso 可以藉由建立自訂分類規則，將掃描系統設定為尋找這些識別碼的實例。 它們可以提供符合資料模式的正則運算式，在此情況下為 `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` 。 （選擇性）如果資料通常位於他們知道名稱的資料行中（例如員工 \_ 識別碼或員工識別碼），他們可以加入資料行模式正則運算式，讓掃描更準確。 範例 RegEx 是員工 \_ 識別碼的 \| 雇員識別碼。

掃描系統接著可以使用此規則來檢查資料行中的實際資料和資料行名稱，以嘗試識別在其中找到員工識別碼模式的每個實例。

## <a name="steps-to-create-a-custom-classification-rule"></a>建立自訂分類規則的步驟

若要建立自訂分類規則：

1. 遵循上一節中的指示來建立自訂分類。 您將在分類規則設定中加入此自訂分類，以便系統在找到資料行中的相符項時套用此自訂分類。

2. 選取 [ **管理中心** ] 圖示。

3. 選取 [ **分類規則** ] 區段。

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="分類規則磚" border="true":::

4. 選取 [新增]。

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="新增分類規則" border="true":::

5. [ **新的分類規則** ] 對話方塊隨即開啟。 填入新規則的設定資訊。

    :::image type="content" source="media/create-a-custom-classification-and-classification-rule/createclassificationrule.png" alt-text="建立新的分類規則" border="true":::

|欄位     |說明  |
|---------|---------|
|名稱   |    必要。 最大值為100個字元。    |
|描述      |選擇性。 最大值為256個字元。    |
|分類名稱    | 必要。 從下拉式清單中選取分類的名稱，以便在找到相符的情況下將它套用至掃描器。        |
|州   |  必要。 選項為 [已啟用] 或 [已停用]。 預設為啟用。    |
|資料模式    |選擇性。 表示儲存在資料欄位中之資料的正則運算式。 限制很大。 在先前的範例中，資料模式會測試實際上是單字的員工識別碼 `Employee{GUID}` 。  |
|資料行模式    |選擇性。 表示您要比對之資料行名稱的正則運算式。 限制很大。          |

在 [ **資料模式**] 下，有兩個選項：

- **相異相符閾值**：在掃描器上執行資料模式之前，必須在資料行中找到的相異資料值總數。 建議值為8。 此值可以在2到32的範圍內手動調整。 系統需要此值，以確保資料行包含足夠的資料，以供掃描器精確地分類。 例如，包含多個資料列的資料行，而這些資料列全都包含值1，則不會進行分類。 如果資料行包含一個具有值的資料列，而其餘的資料列也不會分類。 如果您指定多個模式，則此值會套用至每個模式。

- **最小相符閾值**：您可以使用此設定來設定要套用分類的掃描程式必須找到的資料行中資料值符合的最小百分比。 建議值為60%。 您必須小心使用此設定。 如果您降低低於60% 的等級，您可能會在您的目錄中引進「假」分類。 如果您指定多個資料模式，則會停用此設定，並將值修正為60%。

## <a name="next-steps"></a>後續步驟

現在您已建立分類規則，可以將它新增至掃描規則集，讓您的掃描在掃描時使用該規則。 如需詳細資訊，請參閱 [建立掃描規則集](create-a-scan-rule-set.md)。
