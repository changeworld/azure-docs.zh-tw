---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 41f2d4540f665137d34d262546cdc1a2edfbae3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77608751"
---
## <a name="prerequisites"></a>必要條件
在撰寫 CDN 管理程式碼之前，您必須做一些準備工作，讓程式碼與 Azure Resource Manager 進行互動。 若要執行這項準備作業，您必須：

* 建立資源群組，以包含本教學課程中建立的 CDN 設定檔
* 設定 Azure Active Directory，針對應用程式提供驗證
* 將權限套用到資源群組，如此一來，就只有來自您 Azure AD 租用戶的授權使用者可以與 CDN 設定檔進行互動

### <a name="creating-the-resource-group"></a>建立資源群組
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [建立資源]。
3. 搜尋 [資源群組]，然後在 [資源群組] 窗格中，按一下 [建立]。

    ![建立新的資源群組](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. 將您的資源群組命名為 CdnConsoleTutorial 。  選取您的訂用帳戶，並選擇離您最近的位置。  您可以視需要按一下 [釘選到儀表板] 核取方塊，將資源群組釘選到入口網站的儀表板上。  釘選可方便日後尋找。  進行選擇之後，按一下 [建立] 。

    ![為資源群組命名](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. 建立資源群組之後，如果您未將它釘選到儀表板，可以依序按一下 [瀏覽] 和 [資源群組] 來尋找。  如需開啟，請按一下資源群組。  請記下您的**訂用帳戶 ID**。 我們稍後將會用到此資訊。

    ![為資源群組命名](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>建立 Azure AD 應用程式，然後套用權限
有兩種方式可使用 Azure Active Directory 來驗證應用程式︰個別使用者或服務主體。 服務主體類似於 Windows 中的服務帳戶。  我們並未授與特定使用者權限來與 CDN 設定檔互動，而是改為將權限授與服務主體。  服務主體通常用於自動化的非互動式處理程序。  雖然本教學課程是撰寫互動式主控台應用程式，但我們將著重在服務主體處理方法。

建立服務主體包含數個步驟，其中包括建立 Azure Active Directory 應用程式。  若要建立它，我們將[遵循此教學課程](../articles/active-directory/develop/howto-create-service-principal-portal.md)。

> [!IMPORTANT]
> 請務必遵循[教學課程連結](../articles/active-directory/develop/howto-create-service-principal-portal.md)中的所有步驟。  您必須如所述方式確實完成，這點「很重要」。  請務必記下您的**租用戶識別碼**、**租用戶網域名稱** (除非您指定了自訂網域，否則通常是 *.onmicrosoft.com* 網域)、**用戶端識別碼**和**用戶端驗證金鑰**，因為我們稍後需要用到這項資訊。  務必謹慎地保護您的**用戶端識別碼**和**用戶端驗證金鑰**，因為任何人都可以使用這些認證，以服務主體形式來執行作業。
>
> 當您要進入名為設定多租用戶應用程式的步驟時，選取 [否]。
>
> 當您要進入[將應用程式指派給角色](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)步驟時，請使用稍早建立的資源群組 *CdnConsoleTutorial*，但指派 [CDN 設定檔參與者] 角色，而不是 [讀取者] 角色。  當您將應用程式指派給資源群組中的 **CDN 設定檔參與者** 角色之後，請返回本教學課程。 
>
>

一旦您建立服務主體並指派 **CDN 設定檔參與者**角色之後，資源群組的 [使用者] 刀鋒視窗看起來應該如下列映像所示。

![[使用者] 刀鋒視窗](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>互動式使用者驗證
如果比起服務主體，您比較需要使用互動式個別使用者驗證，則此程序與服務主體的程序很類似。  事實上，您必須遵循相同的程序，但要進行些微變更。

> [!IMPORTANT]
> 如果您選擇使用個別使用者驗證，而不是服務主體，只需遵循以下這些步驟。
>
>

1. 當您建立應用程式，而不是 **Web 應用程式**時，請選擇 [原生應用程式]。

    ![原生應用程式](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. 在下一個頁面上，系統將提示您輸入**重新導向 URI**。  URI 不會進行驗證，但請記住您的輸入。 您稍後將會用到此資訊。
3. 不需要 **用戶端驗證金鑰**。
4. 我們不會將服務主體指派給 **CDN 設定檔參與者** 角色，而是會指派個別使用者或群組。  在此範例中，您可以看到我已將「CDN 示範使用者」指派給 **CDN 設定檔參與者**角色。  

    ![個別使用者存取](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
