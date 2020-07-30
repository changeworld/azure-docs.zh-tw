---
title: 設定實例和驗證（入口網站）
titleSuffix: Azure Digital Twins
description: 請參閱如何使用 Azure 入口網站設定 Azure 數位 Twins 服務的實例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 73b7171b89b26926992e95f77e376e7bb7731eff
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408266"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>設定 Azure 數位 Twins 實例和驗證（入口網站）

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文涵蓋**設定新的 Azure 數位 Twins 實例**的步驟，包括建立實例和設定驗證。 完成本文之後，您將擁有可開始進行程式設計的 Azure 數位 Twins 實例。

這一版的文章會使用 Azure 入口網站手動逐一執行這些步驟。 Azure 入口網站是統一的 Web 式主控台，提供有別於命令列工具的替代方案。
* 若要使用 CLI 手動執行這些步驟，請參閱這篇文章的 CLI 版本：[*如何：設定實例和驗證（CLI）*](how-to-set-up-instance-cli.md)。
* 若要使用部署腳本範例執行自動安裝，請參閱本文的腳本版本：[*如何：設定實例和驗證（已編寫腳本）*](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
 
然後，使用您的認證登入[Azure 入口網站](https://ms.portal.azure.com/)。

## <a name="prerequisites-permission-requirements"></a>必要條件：許可權需求

若要能夠完成本文中的所有步驟，您必須將其分類為 Azure 訂用帳戶中的擁有者。 

您可以在 Azure 入口網站的 [訂用帳戶][頁面](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)中檢查許可權等級（您可以使用此連結，或使用入口網站搜尋*列來尋找*訂用帳戶）。 尋找您所使用的訂用帳戶名稱，並在 [*我的角色*] 資料行中查看其角色。 如果您是擁有者，此值為*owner*：

:::image type="content" source="media/how-to-set-up-instance/portal/subscriptions-role.png" alt-text="Azure 入口網站中的 [訂閱] 頁面，以擁有者身分顯示使用者" lightbox="media/how-to-set-up-instance/portal/subscriptions-role.png":::

如果您發現值是「*參與者*」或「*擁有*者」以外的專案，您可以透過下列其中一種方式繼續進行：
* 請洽詢您的訂用帳戶擁有者並要求擁有者，以代表您完成本文中的步驟
* 請洽詢訂用帳戶擁有者或訂用帳戶上具有使用者存取管理員角色的人員，並要求他們提高訂用帳戶的擁有者許可權，讓您能夠繼續進行。 這適用于您的組織和 it 內的角色。

## <a name="create-the-azure-digital-twins-instance"></a>建立 Azure 數位 Twins 實例

在本節中，您將使用 Azure 入口網站**建立新的 Azure 數位 Twins 實例**。

登入[Azure 入口網站](https://ms.portal.azure.com/)之後，請從在 [Azure 服務] 首頁功能表中選取 [_建立資源_] 開始。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="從 Azure 入口網站的首頁選取 [建立資源]":::

在搜尋方塊中搜尋*Azure 數位 Twins* ，然後從結果中選擇 [ **azure 數位 Twins （預覽）** ] 服務。 選取 [_建立_] 按鈕，以建立服務的新實例。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="從 [Azure 數位 Twins 服務] 頁面選取 [建立]":::

在下列 [*建立資源*] 頁面上，填入下列指定的值：
* **訂**用帳戶：您所使用的 Azure 訂用帳戶
  - **資源群組**：要在其中部署實例的資源群組。 如果您還沒有考慮到現有的資源群組，您可以選取 [*建立新*的] 連結並輸入新資源群組的名稱，在這裡建立一個。
* **位置**：部署的 Azure 數位 Twins 啟用區域。 如需區域支援的詳細資訊，請造訪[*依區域提供的 azure 產品（Azure 數位 Twins）*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
* **資源名稱**：您的 Azure 數位 Twins 實例的名稱。 新實例的名稱在您訂用帳戶的區域中必須是唯一的（也就是說，如果您的訂用帳戶在已使用所選名稱的區域中有另一個 Azure 數位 Twins 實例，系統會要求您挑選不同的名稱）。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="填入描述的值，以建立 Azure 數位 Twins 資源":::

完成後，選取 [審核] [ _+ 建立_]。 這會帶您前往 [摘要] 頁面，您可以在其中查看您輸入的實例詳細資料，然後按 [_建立_]。 

### <a name="verify-success"></a>確認是否成功

推送*Create*之後，您可以在入口網站圖示列的 Azure 通知中，查看實例部署的狀態。 通知會指出部署成功的時間，而您將能夠選取 [_移至資源_] 按鈕來查看您建立的實例。

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="顯示成功部署並反白顯示 [移至資源] 按鈕的 Azure 通知的觀點":::

或者，如果部署失敗，通知會指出原因。 請觀察錯誤訊息中的建議，然後再次嘗試建立實例。

>[!TIP]
>建立實例之後，您可以在 [Azure 入口網站搜尋] 列中搜尋實例的名稱，隨時返回其頁面。

在實例的 *[總覽*] 頁面中，記下其*名稱*、*資源群組*和*主機名稱*。 當您繼續使用 Azure 數位 Twins 實例時，這些都是您可能需要的重要值。 如果其他使用者會針對該實例進行程式設計，您應該與他們共用這些值。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="反白顯示實例 [總覽] 頁面中的重要值":::

您現在已有 Azure 數位 Twins 實例可以開始使用。 接下來，您會提供適當的 Azure 使用者許可權來進行管理。

## <a name="set-up-user-access-permissions"></a>設定使用者存取權限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

首先，在 Azure 入口網站中開啟 Azure 數位 Twins 實例的頁面。 從實例的功能表中，選取 [*存取控制（IAM）*]。 選取 [*新增角色指派*] 底下的 [*新增*] 按鈕。

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="從 [存取控制（IAM）] 頁面選取要新增角色指派":::

在下列 [*新增角色指派*] 頁面上，填入值（必須由 Azure 訂用帳戶的擁有者完成）：
* **角色**：從下拉式功能表中選取 [ *Azure 數位 Twins 擁有者（預覽）* ]
* **指派存取**權：從下拉式功能表中選取 [ *Azure AD 使用者、群組或服務主體*]
* **選取**：搜尋要指派的使用者名稱或電子郵件地址。 當您選取結果時，使用者會顯示在 [選取的*成員*] 區段中。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="將列出的欄位填入 [新增角色指派] 對話方塊":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

當您完成輸入詳細資料時，請按 [*儲存*] 按鈕。

### <a name="verify-success"></a>確認是否成功

您可以在 [*存取控制（IAM）] > [角色指派*] 下，查看您所設定的角色指派。 使用者應該會顯示在清單中，其中包含*Azure 數位 Twins 擁有者（預覽）* 的角色。 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="在 Azure 入口網站中查看 Azure 數位 Twins 實例的角色指派":::

您現在已有 Azure 數位 Twins 實例可開始使用，並已指派許可權來管理它。 接下來，您將設定用戶端應用程式存取它的許可權。

## <a name="set-up-access-permissions-for-client-applications"></a>設定用戶端應用程式的存取權限

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

一開始先流覽至 Azure 入口網站中的[Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) （您可以使用此連結，或使用入口網站搜尋列來尋找）。 從 [服務] 功能表中選取 [*應用程式註冊*]，然後選取 [ *+ 新增註冊*]。

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="Azure 入口網站中 Azure AD 服務頁面的視圖，反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::

在接下來的 [*註冊應用程式*] 頁面中，填入要求的值：
* **名稱**：要與註冊建立關聯的 Azure AD 應用程式顯示名稱
* **支援的帳戶類型**：選取 *[僅限此組織目錄中的帳戶] （僅限預設目錄-單一租使用者）*
* 重新**導向 URI**： Azure AD 應用程式的*Azure AD 應用程式回復 URL* 。 您可以使用 `http://localhost` 。

當您完成時，請按 [*註冊*] 按鈕。

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text="[註冊應用程式] 頁面的視圖，其中已填入所述的值":::

註冊完成設定後，入口網站會將您重新導向至其 [詳細資料] 頁面。

### <a name="provide-azure-digital-twins-api-permission"></a>提供 Azure 數位 Twins API 許可權

接下來，設定您使用 Azure 數位 Twins Api 的基準許可權建立的應用程式註冊。

從應用程式註冊的入口網站頁面，從功能表中選取 [ *API 許可權*]。 在 [下列許可權] 頁面上，按 [ *+ 新增許可權*] 按鈕。

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="Azure 入口網站中的應用程式註冊視圖，反白顯示 [API 許可權] 功能表選項和 [+ 新增許可權] 按鈕":::

在接下來的 [*要求 API 許可權*] 頁面中，切換至 [*我的組織使用的 api* ] 索引標籤，並搜尋*azure 數位 twins*。 從搜尋結果中選取 [ *Azure 數位 Twins* ]，以繼續指派 Azure 數位 Twins api 的許可權。

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="顯示 Azure 數位 Twins 的 [要求 API 許可權] 頁面搜尋結果的視圖":::

接下來，您將選取要授與這些 Api 的許可權。 展開 [**讀取] （1）** 許可權並核取 [*讀取*] 核取方塊，以授與此應用程式註冊讀取器和寫入器許可權。

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text="[要求 API 許可權] 頁面的視圖選取 Azure 數位 Twins Api 的 [讀取] 許可權":::

完成時，請點擊 [*新增許可權*]。

### <a name="verify-success"></a>確認是否成功

回到 [ *API 許可權*] 頁面，確認現在有一個可反映讀取/寫入權限的 Azure 數位 Twins 專案：

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="Azure AD 應用程式註冊之 API 許可權的入口網站視圖，其中顯示 Azure 數位 Twins 的「讀取/寫入存取」":::

您也可以在應用程式註冊的*manifest.js*中驗證與 Azure 數位 Twins 的連線，這在您新增 API 許可權時，會使用 Azure 數位 Twins 資訊自動更新。

若要這樣做，請從功能表中選取 [*資訊清單*]，以查看應用程式註冊的資訊清單代碼。 在程式碼視窗的底部，尋找中的這些欄位 `requiredResourceAccess` 。 值應符合下列螢幕擷取畫面中的值：

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Azure AD 應用程式註冊之資訊清單的入口網站視圖。位於 ' requiredResourceAccess ' 底下，' resourceAppId ' 值為 0b07f429-9f4b-4714-9392-cc5e8e80c8b0，' resourceAccess > id ' 值為4589bd03-58cb-4e6c-b17f-b580e39652f8":::

### <a name="collect-important-values"></a>收集重要值

接下來，從功能表列選取 *[總覽*]，以查看應用程式註冊的詳細資料：

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="應用程式註冊重要值的入口網站視圖":::

記**下頁面上**顯示的 [*應用程式（用戶端）識別碼*] 和 [*目錄（租使用者）] 識別碼*。 稍後會需要這些值，以對[Azure 數位 Twins api 驗證用戶端應用程式](how-to-authenticate-client.md)。 如果您不是要為這類應用程式撰寫程式碼的人員，您應該將這些值與將成為的人員共用。

### <a name="other-possible-steps-for-your-organization"></a>貴組織的其他可能步驟

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>後續步驟

請參閱如何撰寫用戶端應用程式的驗證碼，將您的用戶端應用程式連接到您的實例：
* [*如何：撰寫應用程式驗證碼*](how-to-authenticate-client.md)