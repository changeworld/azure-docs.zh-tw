---
title: 使用您自己的憑證搭配 Azure 資料箱/Azure Data Box Heavy 裝置
description: 如何使用您自己的憑證來存取資料箱或 Data Box Heavy 裝置上的本機 web UI 和 Blog 儲存體。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/08/2020
ms.author: alkohli
ms.openlocfilehash: dab34b26d8237d743e22149ed0da2dd9471d7431
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096005"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>使用您自己的憑證搭配資料箱和 Data Box Heavy 裝置

在訂單處理期間，會產生自我簽署憑證，以存取資料箱或 Data Box Heavy 裝置的本機 web UI 和 Blob 儲存體。 如果您想要透過受信任的通道與您的裝置通訊，可以使用自己的憑證。

本文說明如何安裝您自己的憑證，以及如何還原成預設憑證，然後再將您的裝置退回資料中心。 它也提供憑證需求的摘要。

## <a name="about-certificates"></a>關於憑證

憑證會提供 **公開金鑰** 和實體 (之間的連結，例如已簽署 (由受信任的協力廠商（例如 **憑證授權單位** 單位）) **簽署** 的功能變數名稱) 。  憑證提供便利的方式來散發受信任的公用加密金鑰。 如此一來，憑證便可確保您的通訊受到信任，而且您會將加密資訊傳送到正確的伺服器。

一開始設定您的資料箱裝置時，會自動產生自我簽署憑證。 （選擇性）您可以攜帶自己的憑證。 如果您打算攜帶您自己的憑證，您必須遵循下列指導方針。

在資料箱或 Data Box Heavy 裝置上，會使用兩種類型的端點憑證：

- Blob 儲存體憑證
- 本機 UI 憑證

### <a name="certificate-requirements"></a>憑證需求

憑證必須符合下列需求：

- 端點憑證的格式必須為 `.pfx` 可匯出的私密金鑰。
- 您可以針對每個端點使用個別憑證、多個端點的多網域憑證或萬用字元端點憑證。
- 端點憑證的屬性類似于一般 SSL 憑證的屬性。
- 用戶端電腦上需要有一個 DER 格式的對應憑證 (副檔名 `.cer`) 。
- 上傳本機 UI 憑證之後，您必須重新開機瀏覽器，並清除快取。 請參閱瀏覽器的特定指示。
- 如果裝置名稱或 DNS 功能變數名稱變更，則必須變更憑證。
- 建立端點憑證時，請使用下表：

  |類型 | (SN 的主體名稱)   | (SAN) 的主體替代名稱  |主體名稱範例 |
  |---------|---------|---------|---------|
  |本機 UI| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Blob 儲存體|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |多 SAN 單一憑證|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

如需詳細資訊，請參閱 [憑證需求](../../articles/databox-online/azure-stack-edge-j-series-certificate-requirements.md)。

## <a name="add-certificates-to-device"></a>將憑證新增至裝置

您可以使用自己的憑證來存取本機 web UI，以及存取 Blob 儲存體。

> [!IMPORTANT]
> 如果裝置名稱或 DNS 網域已變更，則必須建立新的憑證。 接著，您應該使用新的裝置名稱和 DNS 網域來更新用戶端憑證和裝置憑證。

若要將您自己的憑證新增至您的裝置，請遵循下列步驟：

1. 移至 [**管理**  >  **憑證**]。

   **名稱** 會顯示裝置名稱。 **Dns 網域** 會顯示 dns 伺服器的功能變數名稱。

   畫面底部會顯示目前使用中的憑證。 針對新的裝置，您會看到在訂單處理期間產生的自我簽署憑證。

   ![資料箱裝置的憑證頁面](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. 如果您需要將 (裝置名稱) 或 **dns (網域** 的 **名稱** 變更為裝置) 的 dns 伺服器網域，請在新增憑證之前，先這麼做。 然後選取 [套用]。

   如果裝置名稱或 DNS 功能變數名稱變更，則憑證必須變更。

   ![將新的裝置名稱和 DNS 網域套用至資料箱](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. 若要新增憑證，請選取 [ **新增憑證** ] 以開啟 [ **新增憑證** ] 面板。 然後選取 **憑證類型** ，也就是 **Blob 儲存體** 或 **本機 web UI**。

   ![在資料箱裝置的憑證頁面上新增憑證面板](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. 選擇 (格式) 的憑證檔案 `.pfx` ，然後輸入匯出憑證時所設定的密碼。 然後選取 [ **驗證 & 新增**]。

   ![將 Blob 端點憑證新增至資料箱的設定](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   成功新增憑證後，[ **憑證** ] 畫面會顯示新憑證的指紋。 憑證的狀態是 **有效** 的。

   ![已成功新增的有效新憑證](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. 若要查看憑證詳細資料，請選取並按一下憑證名稱。 憑證會在一年後到期。

   ![查看資料箱裝置的憑證詳細資料](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. 在您用來存取本機 web UI 的用戶端電腦上安裝新的憑證。 如需相關指示，請參閱下方的 [將憑證匯入到用戶端](#import-certificates-to-client)。

7. 如果您變更了本機 Web UI 的憑證，則必須重新開機瀏覽器，然後再重新開機本機 web UI。 需要執行此步驟，以避免任何 SSL 快取問題。

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

## <a name="import-certificates-to-client"></a>將憑證匯入至用戶端

將憑證新增至您的資料箱裝置之後，您必須將憑證匯入到您用來存取裝置的用戶端電腦。 您會將憑證匯入本機電腦的「受信任的根憑證授權單位」存放區。

若要在 Windows 用戶端上匯入憑證，請遵循下列步驟：

1. 在檔案總管中，以滑鼠右鍵按一下 [憑證檔案] (`.cer` ，格式為 [) ]，然後選取 [ **安裝憑證**]。 這個動作會啟動 [憑證匯入精靈]。

    ![匯入憑證 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. 針對 [ **存放區位置**]，選取 [ **本機電腦**]，然後選取 **[下一步]**。

    ![在 [憑證匯入嚮導] 中選取 [本機電腦] 做為存放區位置](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. 選取 **[將所有憑證放入下列存放區**]，選取 [**信任的根憑證授權** 單位]，然後選取 **[下一步]**

   ![在 [憑證匯入嚮導] 中選取 [受信任的根憑證授權單位] 存放區](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. 檢查您的設定，然後選取 **[完成]**。 訊息會告訴您匯入成功。

   ![檢查您的憑證設定，並完成憑證匯入嚮導](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>還原為預設憑證

將裝置退回 Azure 資料中心之前，您應該還原為訂單處理期間產生的原始憑證。

若要還原為訂單處理期間產生的憑證，請遵循下列步驟：

1. 移至 [**管理**  >  **憑證**]，然後選取 [**還原憑證**]。

   還原憑證會傳回使用在訂單處理期間產生的自我簽署憑證。 您自己的憑證會從裝置中移除。

   ![管理資料箱裝置的憑證中的 [還原憑證] 選項](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. 順利完成憑證回復之後，請移至 [ **關機] 或 [重新開機**]，然後選取 [ **重新開機**]。 需要執行此步驟，以避免任何 SSL 快取問題。

   ![在資料箱裝置上還原憑證之後，關閉或重新開機本機 web UI](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   等候幾分鐘，然後再次登入本機 web UI。
