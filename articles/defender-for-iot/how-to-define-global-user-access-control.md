---
title: 定義全域使用者存取控制
description: 在大型組織中，除了標準網站和區域結構之外，使用者權限可能很複雜，而且可能是由通用群組織結構所決定。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/08/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 399eee06d472204eaf6dbc333f15cee7c4373739
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624637"
---
# <a name="define-global-access-control"></a>定義全域存取控制

在大型組織中，除了標準網站和區域結構之外，使用者權限可能很複雜，而且可能是由通用群組織結構所決定。

為了支援全域且更複雜的使用者存取權限需求，您可以建立以商務單位、區域和網站為基礎的全球商務拓朴。 然後您可以定義這些實體的使用者存取權限。

使用商務拓朴的存取工具可協助組織透過更妥善地控制使用者在 Azure Defender for IoT 平臺中管理和分析裝置的位置，來實行零信任策略。

## <a name="about-access-groups"></a>關於存取群組

全域存取控制是透過建立使用者存取群組來建立。 存取群組是由哪些使用者可以存取特定商務實體的相關規則所組成。 使用群組可讓您控制在相關業務單位、區域和網站上特定使用者角色之 Defender for IoT 的查看和設定存取權。

例如，允許來自 Active Directory 群組的安全性分析師存取所有西歐汽車和半透明生產線，以及一個區域中的塑膠線。

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="安全性分析師 Active Directory 群組的圖表。":::

建立存取群組之前，建議您先：

- 仔細設定您的商務拓朴。 如需商務拓撲的詳細資訊，請參閱使用 [網站地圖視圖](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)。

- 規劃哪些使用者與您建立的存取群組相關聯。 有兩個選項可用來將使用者指派給存取群組：

  - **指派 Active Directory 群組的群組**：確認您已設定 Active Directory 實例，以與內部部署管理主控台整合。
  
  - **指派本機使用者**：確認您已建立使用者。 如需詳細資訊，請參閱 [定義使用者](how-to-create-and-manage-users.md#define-users)。

系統管理員使用者無法指派給存取群組。 這些使用者預設可以存取所有的商務拓樸實體。

## <a name="create-access-groups"></a>建立存取群組

本節說明如何建立存取群組。 預設會為您建立的第一個群組建立預設的全域營業單位和區域。 當您定義第一個群組時，可以編輯預設實體。

若要建立群組：

1. 從內部部署管理主控台的側邊功能表選取 [ **存取群組** ]。

2. 選取 :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::。 在 [ **新增存取群組** ] 對話方塊中，輸入存取群組的名稱。 主控台支援64個字元。 以可協助您輕鬆分辨此群組與其他群組的方式來指派名稱。

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="您在其中建立存取群組的 [新增存取群組] 對話方塊。":::

3. 如果出現 [ **指派 Active Directory 群組** ] 選項，您可以將一個 Active Directory 的使用者群組指派給此存取群組。

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="在 [建立存取群組] 對話方塊中指派 Active Directory 群組。":::

   如果未顯示此選項，而您想要在存取群組中包含 Active Directory 群組，請選取 [ **系統設定**]。 **在 [整合**] 窗格上，定義群組。 輸入組名，就像在 Active Directory 設定中所顯示的一樣，以小寫的形式輸入。

5. 在 [ **使用者** ] 窗格中，將所需的使用者數目指派給群組。 您也可以將使用者指派給不同的群組。 如果您以這種方式運作，您必須建立並儲存存取群組和規則，然後從 [ **使用者** ] 窗格將使用者指派給群組。

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="管理您的使用者角色，並視需要指派它們。":::

6. 根據您的商務拓朴存取需求，在 [**新增 *名稱* 的規則**] 對話方塊中建立規則。 此處顯示的選項是以您在 [ **企業版視圖** ] 和 [ **網站管理** ] 視窗中所設計的拓撲為基礎。 

   您可以為每個群組建立一個以上的規則。 當您在多個網站上處理複雜的存取權時，您可能需要為每個群組建立一個以上的規則。 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="將規則新增至您的系統。":::

您所建立的規則會出現在 [ **新增存取群組** ] 對話方塊中。 您可以在該處刪除或編輯它們。

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="從這個視窗中，查看並編輯您的所有存取群組。":::

### <a name="about-rules"></a>關於規則

當您要建立規則時，請注意下列資訊：

- 當存取群組包含數個規則時，規則邏輯會匯總所有規則。 例如，規則使用和邏輯，而不是邏輯。

- 若要成功套用規則，您必須在 [ **網站管理** ] 視窗中，將感應器指派至區域。

- 每個規則只能指派一個元素。 例如，您可以為每個規則指派一個商務單位、一個區域和一個網站。 如果您希望某個 Active Directory 群組中的使用者可以存取不同區域中的不同業務單位，請為群組建立更多規則。

- 如果您變更實體，而且變更會影響規則邏輯，則會刪除規則。 如果對拓撲實體所做的變更會影響規則邏輯，以刪除所有規則，則存取群組仍會保留，但使用者無法登入內部部署管理主控台。 系統會通知使用者，以聯繫其系統管理員以登入。

- 如果未選取任何業務單位或區域，使用者將可存取所有已定義的業務單位和區域。

## <a name="see-also"></a>另請參閱

[關於適用于 IoT 主控台使用者的 Defender](how-to-create-and-manage-users.md)
