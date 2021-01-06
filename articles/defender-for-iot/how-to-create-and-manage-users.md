---
title: 建立和管理使用者
description: 建立及管理感應器的使用者和內部部署管理主控台。 您可以將系統管理員、安全性分析師或唯讀使用者的角色指派給使用者。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/3/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 22d0c59110ba033232fbdf41062b49e9a146ca6f
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955069"
---
# <a name="about-defender-for-iot-console-users"></a>關於適用于 IoT 主控台使用者的 Defender

本文說明如何建立和管理感應器的使用者和內部部署管理主控台。 使用者角色包括系統管理員、安全性分析師或唯讀使用者。 每個角色都與一系列適用于感應器或內部部署管理主控台之工具的許可權相關聯。 角色的設計目的是為了協助對 IoT 的 Azure Defender 進行細微且安全的存取。

也可以使用功能來追蹤使用者活動，並啟用 Active Directory 登入。

根據預設，每個感應器和內部部署管理主控台都會與 *cyberx 和支援* 使用者一起安裝。 這些使用者可以存取用於疑難排解和設定的 advanced tools。 系統管理員使用者應該以這些使用者認證登入、建立管理使用者，然後為安全性分析師和唯讀使用者建立額外的使用者。

## <a name="role-based-permissions"></a>以角色為基礎的許可權
以下是可用的使用者角色：

- **唯讀**：唯讀使用者執行工作，例如在裝置地圖上查看警示和裝置。 這些使用者可以存取 [ **導覽**] 下顯示的選項。

- **安全性分析師**：安全性分析師具有唯讀使用者許可權。 他們也可以在裝置上執行動作、確認警示，以及使用調查工具。 這些使用者可以存取 [ **流覽** 和 **分析**] 下顯示的選項。

- 系統 **管理員**：系統管理員可以存取所有工具，包括定義系統組態、建立和管理使用者等。 這些使用者可以存取 [ **導覽**]、[ **分析**] 和 [ **管理**] 底下顯示的選項。

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>內部部署管理主控台工具以角色為基礎的許可權

本節說明適用于內部部署管理主控台的系統管理員、安全性分析師和唯讀使用者的許可權。  

| 權限 | 唯讀 | 安全性分析師 | 系統管理員 |
|--|--|--|--|
| 查看並篩選企業地圖 | ✓ | ✓ | ✓ |
| 建立網站 |  |  | ✓ |
| 管理網站 (新增和編輯區域)  |  |  | ✓ |
| 查看和篩選裝置清查 | ✓ | ✓ | ✓ |
| 查看和管理警示：確認、學習和釘選 | ✓ | ✓ | ✓ |
| 產生報表 |  | ✓ | ✓ |
| 查看風險評量報告 |  | ✓ | ✓ |
| 設定警示排除專案 |  | ✓ | ✓ |
| 查看或定義存取群組 |  |  | ✓ |
| 管理系統設定 |  |  | ✓ |
| 管理使用者 |  |  | ✓ |
| 將警示資料傳送給合作夥伴 |  |  | ✓ |
| 管理憑證 |  |  | ✓ |
| 使用者未啟用時的會話超時 | 30 分鐘 | 30 分鐘  | 30 分鐘  |

#### <a name="assign-users-to-access-groups"></a>指派使用者存取群組

系統管理員可以藉由將使用者指派給特定的 *存取群組*，在 Defender for IoT 中增強使用者存取控制。 存取群組會指派給感應器所在的區域、網站、區域及營業單位。 藉由將使用者指派給存取群組，系統管理員可以對使用者管理和分析裝置偵測的位置取得特定的控制權。 

如此一來，就能容納大型組織，其中的使用者權限可能很複雜，或是由通用群組織安全性原則所決定。 如需詳細資訊，請參閱 [定義全域存取控制](how-to-define-global-user-access-control.md)。

### <a name="role-based-permissions-to-sensor-tools"></a>以角色為基礎的感應器工具許可權

本節說明可供感應器系統管理員、安全性分析師和唯讀使用者使用的許可權。  

| 權限 | 唯讀 | 安全性分析師 | 系統管理員 |
|--|--|--|--|
| 檢視儀表板 | ✓ | ✓ | ✓ |
| 控制項對應縮放視圖 |  |  | ✓ |
| 檢視警示 | ✓ | ✓ | ✓ |
| 管理警示：確認、學習和釘選 |  | ✓ | ✓ |
| 查看時間軸中的事件 |  | ✓ | ✓ |
| 授權裝置、已知的掃描裝置、程式設計裝置 |  | ✓ | ✓ |
| 查看調查資料 | ✓ | ✓ | ✓ |
| 管理系統設定 |  |  | ✓ |
| 管理使用者 |  |  | ✓ |
| 反向查詢的 DNS 伺服器 |  |  | ✓ |
| 將警示資料傳送給合作夥伴 |  | ✓ | ✓ |
| 建立警示批註 |  | ✓ | ✓ |
| 查看程式設計變更歷程記錄 | ✓ | ✓ | ✓ |
| 建立自訂警示規則 |  | ✓ | ✓ |
| 同時管理多個通知 |  | ✓ | ✓ |
| 管理憑證 |  |  | ✓ |
| 使用者未啟用時的會話超時 | 30 分鐘 | 30 分鐘 | 30 分鐘 |

## <a name="define-users"></a>定義使用者

本節說明如何定義使用者。 Cyberx、支援和系統管理員使用者可以新增、移除及更新其他使用者定義。

若要定義使用者：

1. 從感應器或內部部署管理主控台的左窗格中，選取 [ **使用者**]。
1. 在 [ **使用者** ] 視窗中，選取 [ **建立使用者**]。
1. 在 [ **建立使用者** ] 窗格中，定義下列參數：

   - **Username**：輸入使用者名稱。
   - **電子郵件**：輸入使用者的電子郵件地址。
   - **First name**：輸入使用者的名字。
   - **姓氏**：輸入使用者的姓氏。
   - **角色**：定義使用者的角色。 請參閱以 [角色為基礎的許可權](#role-based-permissions)。
   - **存取群組**：如果您要為內部部署管理主控台建立使用者，請定義使用者的存取群組。 請參閱 [定義全域存取控制](how-to-define-global-user-access-control.md)。
   - **密碼**：選取使用者類型，如下所示：
     - **本機使用者**：定義感應器使用者或內部部署管理主控台的密碼。 密碼必須包含至少六個字元，而且必須包含字母和數位。
     - **Active Directory 使用者**：您可以允許使用者使用 Active Directory 認證來登入感應器或管理主控台。 定義的 Active Directory 群組可以與特定的許可權層級相關聯。 例如，設定特定的 Active Directory 群組，並將群組中的所有使用者指派給唯讀使用者類型。

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="管理您的使用者。":::

## <a name="user-session-timeout"></a>使用者會話超時

如果使用者在鍵盤或滑鼠上未處於作用中狀態，則會將它們登出，且必須重新登入。

當使用者在30分鐘內未使用其主控台滑鼠或鍵盤時，會強制執行會話登出。

這項功能預設會啟用，而且在升級時可以停用。 此外，也可以更新會話計數時間。 會話時間以秒為單位定義。 定義適用于每個感應器和內部部署管理主控台。

當未通過活動的超時時，主控台會出現會話超時訊息。

### <a name="control-inactivity-sign-out"></a>控制無活動登出

系統管理員使用者可以啟用和停用無活動登出，並調整無活動臨界值。

若要存取命令：

1. 使用 Defender for IoT 系統管理認證，登入感應器或內部部署管理主控台的 CLI。

1. 輸入 `sudo nano /var/cyberx/properties/authentication`。

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

若要停用此功能，請將變更 `infinity_session_expiration = true` 為 `infinity_session_expiration = false` 。

若要更新登出計數期間，請將 `= <number>` 值調整為所需的時間。

## <a name="track-user-activity"></a>追蹤使用者活動 

您可以在每個感應器的事件時間軸中追蹤使用者活動。 時間軸會顯示事件或受影響的裝置，以及使用者執行活動的時間和日期。

若要查看使用者活動：

1. 登入感應器。
1. 在 [事件時間軸] 中，啟用 [ **使用者作業** ] 選項。 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="查看使用者的活動。":::

## <a name="integrate-with-active-directory-servers"></a>與 Active Directory 伺服器整合 

將感應器或內部部署管理主控台設定為使用 Active Directory。 這可讓 Active Directory 使用者使用其 Active Directory 認證來存取 IoT 主控台的 Defender。

支援兩種類型的 LDAP 型驗證：

- **完整驗證**：從 LDAP 伺服器取出使用者詳細資料。 範例包括名字、姓氏、電子郵件和使用者權限。

- **信任的使用者**：只抓取使用者密碼。 其他使用者的詳細資料則是以感應器中定義的使用者為基礎。

### <a name="active-directory-and-defender-for-iot-permissions"></a>適用于 IoT 的 Active Directory 和 Defender 許可權

您可以將這裡定義的 Active Directory 群組與特定許可權層級建立關聯。 例如，設定特定的 Active Directory 群組，並將 RO 許可權指派給群組中的所有使用者。 如需詳細資料，請參閱 [建立及管理使用者](how-to-create-and-manage-users.md) 。

若要設定 Active Directory：

1. 從左窗格中選取 [ **系統設定**]。

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="查看您的 Active Directory 系統設定。":::

1. 在 [ **系統設定** ] 窗格中，選取 [ **Active Directory**]。

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="編輯您的 Active Directory 設定。":::

1. 在 [**編輯 Active Directory** 設定] 對話方塊中，選取 [**已啟用 Active Directory 整合**] 的 [  >  **儲存**]。 [ **編輯 Active Directory** 設定] 對話方塊會展開，而您現在可以輸入參數以設定 Active Directory。

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="輸入要設定 Active Directory 的參數。":::

    > [!NOTE]
    > - 您必須在這裡以 Active Directory 所顯示的完全相同的方式定義 LDAP 參數。
    > - 針對所有 Active Directory 參數，只使用小寫。 即使 Active Directory 中的設定使用大寫，也請使用小寫。
    > - 您無法同時設定相同網域的 LDAP 和 LDAPS。 不過，您可以同時針對不同的網域使用兩者。

1. 設定 Active Directory 伺服器參數，如下所示：

   | 伺服器參數 | 描述 |
   |--|--|
   | 網域控制站 FQDN | 將完整功能變數名稱 (FQDN) 完全與您的 LDAP 伺服器上所顯示的相同。 例如，輸入 `host1.subdomain.domain.com`。 |
   | 網域控制站埠 | 定義您的 LDAP 設定所在的埠。 |
   | 主域 | 根據您的 LDAP 設定，設定功能變數名稱 (例如 `subdomain.domain.com`) 和連線類型。 |
   | Active Directory 群組 | 輸入在 LDAP 伺服器上 Active Directory 設定中定義的組名。 |
   | 受信任的網域 | 若要新增受信任的網域，請新增受信任網域的功能變數名稱和連線類型。 <br />您只能針對在 [使用者] 下定義的使用者設定受信任網域。 |

1. 選取 [儲存]。

1. 若要新增信任的伺服器，請選取 [ **新增伺服器** ] 並設定另一部伺服器。

## <a name="resetting-a-users-password-for-the-sensor-or-on-premises-management-console"></a>為感應器或內部部署管理主控台重設使用者的密碼

### <a name="cyberx-or-support-user"></a>CyberX 或支援使用者

只有 **CyberX** 和 **支援** 使用者可以存取 **密碼** 復原功能。 如果 **CyberX** 或 **支援** 使用者忘記其密碼，可以透過 Defender 上的 [IoT 登入] 頁面上的 [ **密碼修復** ] 選項重設密碼。

若要重設 CyberX 或支援使用者的密碼：

1. 在 [Defender for IoT 登入] 畫面上，選取 [  **密碼** 復原]。 **密碼修復** 畫面隨即開啟。

1. 選取 [ **CyberX** ] 或 [ **支援**]，然後複製唯一的識別碼。

1. 流覽至 [Azure 入口網站]，然後選取 [ **網站和感應器**]。  

1. 從頂端工具列中選取訂用帳戶 **篩選器** 圖示 :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false":::  ，然後選取您的感應器所連接的訂用帳戶。

1. 選取 [ **復原內部部署管理主控台密碼** ] 索引標籤。

   :::image type="content" source="media/password-recovery-images/recover-button.png" alt-text="選取 [復原內部部署管理] 按鈕，以下載修復檔案。":::

1. 輸入您在 **密碼修復** 畫面上收到的唯一識別碼，然後選取 [ **復原**]。 `password_recovery.zip`下載檔案。

    > [!NOTE]
    > 請勿改變密碼修復檔。 這是已簽署的檔案，如果您使用它進行篡改，將無法運作。

1. 在 [ **密碼修復** ] 畫面上，選取 **[上傳**]。 **[上傳密碼** 復原檔案] 視窗隨即開啟。

   :::image type="content" source="media/password-recovery-images/upload.png" alt-text="上傳您的修復檔案以取得新的密碼。":::

1. 選取 **[流覽]** 以找出您的檔案 `password_recovery.zip` ，或將拖曳 `password_recovery.zip` 至視窗。

    > [!NOTE]
    > 可能會出現錯誤訊息，指出檔案無效。 若要修正這個錯誤訊息，請確定您已選取正確的訂用帳戶，然後再下載 `password_recovery.zip` 並重新下載。  

1. 選取 **[下一步]**，系統就會顯示您的管理主控台的使用者和系統產生的密碼。

### <a name="administrator-security-analyst-and-read-only-user"></a>系統管理員、安全性分析師和唯讀使用者

唯讀和安全性分析師無法重設自己的密碼，而且需要聯繫具有系統管理員、支援或 CyberX 角色的使用者，才能重設其密碼。 系統管理員使用者必須聯絡 **CyberX** 或 **支援** 使用者以重設其密碼。

若要在感應器上重設使用者的密碼：

1. 系統管理員、支援或 CyberX 角色使用者應登入感應器。

1. 從左側面板中選取 [ **使用者** ]。

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="從左側窗格中選取 [使用者] 選項。":::

1. 找出使用者，然後從 [**動作**] 下拉式功能表中選取 [**編輯**]。

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="從 [動作] 下拉式功能表中選取 [編輯]。":::

1. 在 [ **新密碼** ] 中輸入新密碼，然後 **確認新的密碼** 欄位。

1. 選取 [更新]。

若要在內部部署管理主控台重設使用者的密碼：

1. 系統管理員、支援或 CyberX 角色使用者應登入感應器。

1. 從左側面板中選取 [ **使用者** ]。

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="在左面板中，選取使用者的選項。":::

1. 找出您的使用者，然後選取 [編輯] 圖示 :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false"::: 。

1. 在 [ **新密碼** ] 中輸入新密碼，然後 **確認新的密碼** 欄位。

1. 選取 [更新]。

## <a name="see-also"></a>請參閱

[啟用並設定您的感應器](how-to-activate-and-set-up-your-sensor.md) 
[啟用並設定您的內部部署管理主控台](how-to-activate-and-set-up-your-on-premises-management-console.md) 
[追蹤感應器活動](how-to-track-sensor-activity.md)
