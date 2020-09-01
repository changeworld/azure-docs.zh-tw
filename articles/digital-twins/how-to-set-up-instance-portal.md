---
title: 設定執行個體和驗證 (入口網站)
titleSuffix: Azure Digital Twins
description: 瞭解如何使用 Azure 入口網站設定 Azure 數位 Twins 服務的實例
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 300a11c36b11230d2bd75534f152a0a5955743ed
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181779"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>設定 Azure 數位 Twins 實例和驗證 (入口網站) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文涵蓋 **設定新 Azure 數位 Twins 實例**的步驟，包括建立實例和設定驗證。 完成本文之後，您將會有可開始進行程式設計的 Azure 數位 Twins 實例。

這一版的文章會使用 Azure 入口網站手動逐一完成這些步驟。 Azure 入口網站是統一的 Web 式主控台，提供有別於命令列工具的替代方案。
* 若要使用 CLI 手動完成這些步驟，請參閱這篇文章的 CLI 版本： [*如何：設定 (cli) 的實例和驗證 *](how-to-set-up-instance-cli.md)。
* 若要使用部署腳本範例來執行自動安裝，請參閱本文的腳本版本： [*如何：設定實例和驗證 (腳本) *](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="create-the-azure-digital-twins-instance"></a>建立 Azure 數位 Twins 實例

在本節中，您將使用[Azure 入口網站](https://ms.portal.azure.com/)**建立 Azure 數位 Twins 的新實例**。 流覽至入口網站，並使用您的認證登入。

在入口網站中，從選取 [Azure 服務] 首頁功能表中的 [ _建立資源_ ] 開始。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="從 Azure 入口網站的首頁中選取 [建立資源]":::

在搜尋方塊中搜尋 *Azure 數位 Twins* ，然後從結果中選擇 **azure 數位 Twins (Preview) ** 服務。 選取 [ _建立_ ] 按鈕，以建立服務的新實例。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="從 Azure 數位 Twins 服務頁面選取 [建立]":::

在 [ *建立資源* ] 頁面上，填入下列值：
* **訂**用帳戶：您所使用的 Azure 訂用帳戶
  - **資源群組**：用來部署實例的資源群組。 如果您還沒有現有的資源群組，您可以選取 [ *建立新* 的連結]，並輸入新資源群組的名稱，在這裡建立一個資源群組。
* **位置**：適用于部署的 Azure 數位啟用 Twins 區域。 如需區域支援的詳細資訊，請造訪 [*依區域提供的 azure 產品 (Azure 數位 Twins) *](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
* **資源名稱**： Azure 數位 Twins 實例的名稱。 新實例的名稱在訂用帳戶的區域內必須是唯一的 (也就是說，如果您的訂用帳戶在已使用您所選名稱的區域中有另一個 Azure 數位 Twins 實例，系統會要求您挑選不同的名稱) 。

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="填寫描述的值以建立 Azure 數位 Twins 資源":::

完成時，選取 [ _審核 + 建立_]。 這會帶您前往 [摘要] 頁面，您可以在其中查看所輸入的實例詳細資料，並點擊 [ _建立_]。 

### <a name="verify-success-and-collect-important-values"></a>驗證成功並收集重要值

推送 *建立*之後，您可以在入口網站圖示列的 Azure 通知中，查看實例部署的狀態。 通知將會指出部署成功的時間，您將能夠選取 [ _移至資源_ ] 按鈕來查看您建立的實例。

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="顯示成功部署並反白顯示 [移至資源] 按鈕的 Azure 通知的查看":::

或者，如果部署失敗，通知將會指出原因。 觀察錯誤訊息的建議，然後重試建立實例。

>[!TIP]
>建立實例之後，您可以隨時在 Azure 入口網站搜尋列中搜尋實例的名稱，以返回其頁面。

從實例的 *[總覽* ] 頁面中，記下其 *名稱*、 *資源群組*和 *主機名稱*。 當您繼續使用 Azure 數位 Twins 實例時，這些都是您可能需要的重要值。 如果其他使用者將針對該實例進行程式設計，您應該與這些使用者共用這些值。

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="從實例的總覽頁面中反白顯示重要值":::

您現在已準備好開始使用 Azure 數位 Twins 實例。 接下來，您將授與適當的 Azure 使用者權限來管理它。

## <a name="set-up-user-access-permissions"></a>設定使用者存取權限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

首先，在 Azure 入口網站中開啟 Azure 數位 Twins 實例的頁面。 從實例的功能表中，選取 [ *存取控制] (IAM) *。 選取 [*新增角色指派*] 底下的 [*新增*] 按鈕。

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="從 [存取控制 (IAM) ] 頁面中選取要新增角色指派":::

在 [ *新增角色指派* ] 頁面上，填入 [Azure 訂用帳戶) 中具有 [足夠許可權](#prerequisites-permission-requirements) 的使用者必須完成的值 (：
* **角色**：從下拉式功能表中選取*Azure 數位 Twins 擁有者 (預覽) *
* **指派存取權給**：從下拉式功能表選取*Azure AD 使用者、群組或服務主體*
* **選取**：搜尋要指派之使用者的名稱或電子郵件地址。 當您選取結果時，使用者將會顯示在 [ *選取的成員* ] 區段中。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="將列出的欄位填入 [新增角色指派] 對話方塊":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

當您完成輸入詳細資料時，請按 [ *儲存* ] 按鈕。

### <a name="verify-success"></a>確認是否成功

您可以在 [ *存取控制] (IAM) > 角色指派*下，查看您所設定的角色指派。 使用者應該會出現在清單中，其中包含 *Azure 數位 Twins 擁有者 (預覽) *的角色。 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="在 Azure 入口網站中查看 Azure 數位 Twins 實例的角色指派":::

您現在已準備好開始使用 Azure 數位 Twins 實例，並已獲指派管理該實例的許可權。 接下來，您將設定用戶端應用程式存取的許可權。

## <a name="set-up-access-permissions-for-client-applications"></a>設定用戶端應用程式的存取權限

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

首先，流覽至 Azure 入口網站中的 [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) (您可以使用此連結，或使用入口網站搜尋列) 來尋找它。 從 [服務] 功能表中選取 *應用程式註冊* ，然後選取 [ *+ 新增註冊*]。

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="查看 Azure 入口網站中的 Azure AD 服務頁面，並反白顯示 [應用程式註冊] 功能表選項和 [+ 新增註冊] 按鈕":::

在接下來的 [ *註冊應用程式* ] 頁面中，填入要求的值：
* **名稱**：要與註冊相關聯的 Azure AD 應用程式顯示名稱
* **支援的帳戶類型**： *僅 (預設目錄-單一租使用者) 中選取此組織目錄中的帳戶 *
* 重新**導向 URI**： Azure AD 應用程式*Azure AD 應用程式回復 URL* 。 將 *公用用戶端/原生 (mobile & desktop) * 的 URI `http://localhost` 。

當您完成時，請按 [ *註冊* ] 按鈕。

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text="[註冊應用程式] 頁面的 [註冊應用程式] 頁面，其中已填入所述的值":::

註冊完成設定之後，入口網站會將您重新導向至其詳細資料頁面。

### <a name="provide-azure-digital-twins-api-permission"></a>提供 Azure 數位 Twins API 許可權

接下來，使用 Azure 數位 Twins Api 的基準許可權，設定您所建立的應用程式註冊。

從應用程式註冊的入口網站頁面，選取功能表中的 [ *API 許可權* ]。 在 [下列許可權] 頁面上，按下 [ *+ 新增許可權* ] 按鈕。

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="查看 Azure 入口網站中的應用程式註冊，反白顯示 [API 許可權] 功能表選項和 [+ 新增許可權] 按鈕":::

在接下來的 [ *要求 API 許可權* ] 頁面中，切換至 [ *我的組織使用的 api* ] 索引標籤，並搜尋 *azure 數位 twins*。 從搜尋結果中選取 *Azure 數位 Twins* ，以繼續指派 Azure 數位 Twins api 的許可權。

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="顯示 Azure 數位 Twins 的 [要求 API 許可權] 頁面搜尋結果查看":::

>[!NOTE]
> 如果您的現有 Azure 數位 Twins 實例是從服務的先前公開預覽版 (在2020年7月之前) 仍在訂用帳戶中，則您必須改為尋找 *Azure 智慧空間服務* 。 這是一組相同 Api 的舊名稱，在此步驟之後，將不會變更您的體驗。

接下來，您將選取要為這些 Api 授與的許可權。 展開 [ **讀取 (1]) ** 許可權，然後核取顯示 [ *讀取* ] 的方塊，以授與此應用程式註冊讀取器和寫入器許可權。

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text="針對 Azure 數位 Twins Api 選取 [讀取] 許可權的 [要求 API 許可權] 頁面的視圖":::

完成時點擊 *新增許可權* 。

### <a name="verify-success"></a>確認是否成功

回到 [ *API 許可權* ] 頁面，確認現在有 Azure 數位 Twins 的專案反映讀取/寫入權限：

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="入口網站的 Azure AD 應用程式註冊 API 許可權的入口網站視圖，顯示 Azure 數位 Twins 的「讀取/寫入存取權」":::

您也可以在應用程式註冊的 *manifest.js*內確認 Azure 數位 Twins 的連線，這會在您新增 API 許可權時使用 Azure 數位 Twins 資訊自動更新。

若要這樣做，請從功能表中選取 [ *資訊清單* ]，以查看應用程式註冊的資訊清單程式碼。 滾動至程式碼視窗底部，然後在底下尋找這些欄位 `requiredResourceAccess` 。 這些值應該符合以下螢幕擷取畫面中的值：

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Azure AD 應用程式註冊的資訊清單入口網站視圖。在 ' requiredResourceAccess ' 底下的 ' resourceAppId ' 值為 0b07f429-9f4b-4714-9392-cc5e8e80c8b0，而 ' >resourceaccess > 識別碼 ' 值為4589bd03-58cb-4e6c-b17f-b580e39652f8":::

### <a name="collect-important-values"></a>收集重要值

接下來，從功能表列選取 [ *總覽* ]，以查看應用程式註冊的詳細資料：

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="入口網站查看應用程式註冊的重要值":::

記下**您**頁面上顯示的*應用程式 (用戶端) 識別碼*和*目錄 (租使用者) 識別碼*。 稍後將需要這些值，以 [針對 Azure 數位 Twins api 驗證用戶端應用程式](how-to-authenticate-client.md)。 如果您不是要為這類應用程式撰寫程式碼的人員，您應該將這些值與將會與人共用。

### <a name="other-possible-steps-for-your-organization"></a>您組織的其他可能步驟

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>後續步驟

瞭解如何藉由撰寫用戶端應用程式的驗證碼，將用戶端應用程式連線至您的實例：
* [*How to：撰寫應用程式驗證碼*](how-to-authenticate-client.md)