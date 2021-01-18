---
title: Palo Alto 整合
titleSuffix: Azure Defender for IoT
description: 適用于 IoT 的 Defender 已整合其連續的 ICS 威脅監視平臺與 Palo Alto 的新一代防火牆，以更快且更有效率地封鎖重大威脅。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 85a7622223861f857ce75b8136b509ba279f3d96
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557624"
---
# <a name="about-the-palo-alto-integration"></a>關於 Palo Alto 整合

適用于 IoT 的 Defender 已整合其連續的 ICS 威脅監視平臺與 Palo Alto 的新一代防火牆，以更快且更有效率地封鎖重大威脅。

以下是可用的整合類型：

- 自動封鎖選項：適用于 IoT-Palo Alto 整合的 Direct Defender

- 傳送封鎖至中央管理系統的建議： Defender 進行 IoT-Panorama 整合

## <a name="configure-immediate-blocking-by-specified-palo-alto-firewall"></a>透過指定的 Palo Alto 防火牆設定立即封鎖

在重大情況下，例如惡意程式碼相關警示，您可以啟用自動封鎖。 這是藉由在 Defender for IoT 中設定轉送規則來完成，以將封鎖命令直接傳送至特定的 Palo Alto 防火牆。

當 Defender for IoT 識別出重大威脅時，它會傳送警示，其中包含封鎖受感染來源的選項。 在警示的詳細資料中選取 [ **封鎖來源** ] 會啟用轉送規則，此規則會將封鎖命令傳送到指定的 Palo Alto 防火牆。

若要設定：

1. 在左窗格中，選取 [ **轉送**]。

   :::image type="content" source="media/integration-paloalto/forwarding.png" alt-text="轉送警示畫面。":::

1. 選取 [ **建立轉送規則**]。

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="建立轉送規則":::

1. 若要設定 Palo Alto NGFW 轉送規則：  
 
   - 定義標準規則參數，然後從 [ **動作** ] 下拉式清單方塊中，選取 [ **傳送至 Palo Alto NGFW**。
   
   :::image type="content" source="media/integration-paloalto/edit.png" alt-text="編輯您的轉送規則。":::

1. 在 [動作] 窗格中，設定下列參數：

   - **主機**：輸入 NGFW 伺服器 IP 位址。
   - **埠**：輸入 NGFW 伺服器埠。
   - **Username**：輸入 NGFW 伺服器使用者名稱。
   - **密碼**：輸入 NGFW 伺服器密碼。
   - **設定：設定** 下列選項，以允許 Palo Alto 防火牆封鎖可疑來源：
     - **封鎖不合法** 的函式代碼：通訊協定違規-不合法的域值違反 ICS 通訊協定規格 (潛在的惡意探索) 。
     - **封鎖未經授權的 plc 程式設計/固件更新**：未經授權的 plc 變更。
     - **封鎖未授權的 PLC/停止**：) 的 plc 停止 (停機時間。
     - **封鎖惡意程式碼相關警示**：封鎖產業惡意程式碼嘗試 (TRITON、NotPetya 等 ) 。 您可以選取 **自動封鎖** 的選項。 在此情況下，封鎖會自動且立即執行。
     - **封鎖未經授權的掃描**：未經授權的掃描 (可能的偵察) 。
     
1. 選取 [提交]  。

若要封鎖可疑來源： 

1. 在 [ **警示** ] 窗格中，選取與 Palo Alto 整合相關的警示。 [ **警示詳細資料** ] 對話方塊隨即出現。
   
   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="警示詳細資料":::

1. 若要自動封鎖可疑來源，請選取 [ **區塊來源**]。 [ **請確認** ] 對話方塊隨即出現。

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="確認 [請確認] 畫面上的封鎖。":::

1. 在 [ **請確認** ] 對話方塊中，選取 **[確定**]。 Palo Alto 防火牆封鎖了可疑來源。

## <a name="sending-blocking-policies-to-palo-alto-panorama"></a>傳送封鎖原則至 Palo Alto 全景

Defender for IoT 和 Palo Alto Networks 有現成的整合，可在 Palo Alto Networks 的 NMS、全景中自動建立新的原則。 這項整合需要由全景管理員確認，而且不允許自動封鎖。

整合適用于下列事件：

- **未經授權的 PLC 變更：** 裝置的階梯邏輯或固件的更新。 這可能代表合法的活動或入侵裝置的嘗試。 藉由插入惡意程式碼（例如遠端存取特洛伊程式 (RAT) 或導致實體處理常式的參數（例如，旋轉的操作人員）以不安全的方式運作，可能會造成危害。

- **通訊協定違規：** 違反通訊協定規格的封包結構或域值。 這可能表示應用程式設定錯誤，或惡意嘗試危害裝置。 例如，在目標裝置中造成緩衝區溢位狀況。

- **PLC 停止：** 此命令會導致裝置停止運作，進而造成由 PLC 控制的實體處理常式產生風險。

- **在 ICS 網路中發現的產業惡意程式碼：** 使用原生通訊協定（例如 TRITON 和 Industroyer）操縱 ICS 裝置的惡意程式碼。 適用于 IoT 的 Defender 也會偵測已橫向移至 ICS 和 SCADA 環境的 IT 惡意程式碼，例如 Conficker、WannaCry 和 NotPetya。

- **掃描惡意程式碼：** 收集在前攻擊階段中系統組態相關資料的偵察工具。 例如，Havex 特洛伊程式會使用 OPC 來掃描裝置的產業網路，這是 Windows SCADA 系統用來與 ICS 裝置通訊的標準通訊協定。

## <a name="the-process"></a>程序

當 Defender for IoT 偵測到預先設定的使用案例時，[ **封鎖來源** ] 按鈕會新增至警示。 然後，當 **CyberX** 使用者選取 [ **封鎖來源** ] 按鈕時，Defender for IoT 會藉由傳送預先定義的轉送規則，在全景上建立原則。

只有當全景系統管理員將其推送至網路中的相關 NGFW 時，才會套用此原則。

在 IT 網路中，可能有動態 IP 位址。 因此，在這些子網中，原則必須以 FQDN (DNS 名稱) 而非 IP 位址為基礎。 適用于 IoT 的 Defender 會執行反向查閱，並將具有動態 IP 位址的裝置與其 FQDN (DNS 名稱) 每個設定的時數為單位。

此外，適用于 IoT 的 Defender 會將電子郵件傳送給相關的全景使用者，以通知 Defender 針對 IoT 所建立的新原則正在等候核准。 下圖顯示 IoT-Panorama 整合架構的 Defender。

:::image type="content" source="media/integration-paloalto/structure.png" alt-text="CyberX-全景整合架構":::

## <a name="create-panorama-blocking-policies-in-defender-for-iot-configuration"></a>在 Defender 中為 IoT 設定建立全景封鎖原則

### <a name="to-configure-dns-lookup"></a>設定 DNS 查閱

1. 在左窗格中，選取 [ **系統設定**]。

1. 在 [ **系統設定** ] 窗格中，選取 [ **DNS 設定**] :::image type="icon" source="media/integration-paloalto/settings.png"::: 。

   :::image type="content" source="media/integration-paloalto/configuration.png" alt-text="設定 DNS 設定。":::

1. 在 [ **編輯 DNS 設定** ] 對話方塊中，設定下列參數：

   - **狀態**： DNS 解析程式的狀態。

   - **DNS 伺服器位址**：輸入網路 dns 伺服器的 IP 位址或 FQDN。
   - **Dns 伺服器埠**：輸入用來查詢 DNS 伺服器的埠。
   - **子網**：設定動態 IP 位址子網範圍。 Defender for IoT 會在 DNS 伺服器中反向查閱其 IP 位址，以符合其目前的 FQDN 名稱。
   - **排程反向查閱**：定義排程選項，如下所示：
     - 依特定時間：指定每日執行反向查閱的時機。
     - 依固定間隔 (小時) ：設定執行反向查閱的頻率。
   - **標籤數目**：指示 Defender 讓 IoT 自動將網路 IP 位址解析為裝置 fqdn。 <br />若要設定 DNS FQDN 解析，請新增要顯示的網域標籤數目。 從左至右顯示最多30個字元。
1. 選取 [儲存]  。
1. 若要確保您的 DNS 設定正確無誤，請選取 [ **查閱測試**]。 測試可確保 DNS 伺服器 IP 位址和 DNS 伺服器埠都已正確設定。

### <a name="to-configure-a-forwarding-rule-to-blocks-suspected-traffic-with-the-palo-alto-firewall"></a>使用 Palo Alto 防火牆設定轉送規則以封鎖疑似的流量

1. 在左窗格中，選取 [ **轉送**]。 [轉送] 窗格隨即出現。

   :::image type="content" source="media/integration-paloalto/forward.png" alt-text="轉送畫面。":::

1. 在 [ **轉送** ] 窗格中，選取 [ **建立轉送規則**]。

   :::image type="content" source="media/integration-paloalto/forward-rule.png" alt-text="建立轉送規則":::

1. 若要設定 Palo Alto 全景轉送規則：

   定義標準規則參數，然後從 [ **動作** ] 下拉式清單方塊中，選取 [ **傳送至 Palo Alto 全景**]。 [動作詳細資料] 窗格隨即出現。

   :::image type="content" source="media/integration-paloalto/details.png" alt-text="選取動作":::

1. 在 [動作] 窗格中，設定下列參數：

   - **主機**：輸入全景伺服器的 IP 位址。

   - **埠**：輸入全景伺服器埠。
   - **Username**：輸入全景 server 使用者名稱。
   - **密碼**：輸入全景伺服器密碼。
   - **報表位址**：定義執行封鎖的方式，如下所示：
   
     - **依 Ip 位址**：一律會根據 ip 位址在全景上建立封鎖原則。
     
     - **依 fqdn 或 Ip 位址**：根據 Fqdn 在全景上建立封鎖原則（如果有的話），否則為 ip 位址。
     
   - **電子郵件**：設定原則通知電子郵件的電子郵件地址
     > [!NOTE]
     > 請確定您已在 Defender for IoT 中設定郵件伺服器。 如果未輸入任何電子郵件地址，Defender for IoT 不會傳送通知電子郵件。
   - 在 **警示偵測時執行 DNS 查閱 (核取方塊)**：當您在報表位址中設定 [依 FQDN] 或 [IP 位址] 選項時。 預設會選取此核取方塊。 如果只設定 IP 位址，則會停用此選項。
   - **設定：設定** 下列選項，以允許透過 Palo Alto 全景封鎖可疑來源：
   
     - **封鎖不合法** 的函式代碼：通訊協定違規-不合法的域值違反 ICS、通訊協定規格 (潛在的惡意探索) 。
     
     - **封鎖未經授權的 plc 程式設計/固件更新**：未經授權的 plc 變更。
     
     - **封鎖未授權的 PLC/停止**：) 的 plc 停止 (停機時間。
     
     - **封鎖惡意程式碼相關警示**：封鎖產業惡意程式碼嘗試 (TRITON、NotPetya 等 ) 。 您可以選取 **自動封鎖** 的選項。 在此情況下，封鎖會自動且立即執行。
     
     - **封鎖未經授權的掃描**：未經授權的掃描 (可能的偵察) 。
     
1. 選取 [提交]  。

### <a name="to-block-the-suspicious-source"></a>封鎖可疑來源

1. 在 [ **警示** ] 窗格中，選取與 Palo Alto 整合相關的警示。 **警示的 [詳細資料**] 對話方塊隨即出現。

   :::image type="content" source="media/integration-paloalto/unauthorized.png" alt-text="警示詳細資料":::        

1. 若要自動封鎖可疑來源，請選取 [ **區塊來源**]。

1. 在 [ **請確認** ] 對話方塊中，選取 **[確定]。**

   :::image type="content" source="media/integration-paloalto/please.png" alt-text="確認":::

## <a name="next-steps"></a>後續步驟

瞭解如何 [轉寄警示資訊](how-to-forward-alert-information-to-partners.md)。
