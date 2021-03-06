---
title: ASP.NET Core 中的內容標頭
author: rick-anderson
description: 瞭解 ASP.NET Core 資料保護內容標頭的執行詳細資料。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 45463c9d96ad58e1721f7cb5c16912f783f646ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051716"
---
# <a name="context-headers-in-aspnet-core"></a>ASP.NET Core 中的內容標頭

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>背景和理論

在資料保護系統中，「金鑰」表示可提供已驗證加密服務的物件。 每個金鑰都是由 GUID)  (的唯一識別碼來識別，並隨附它的演算法資訊和 entropic 材質。 這是因為每個金鑰都有獨特的熵，但是系統無法強制執行這項工作，而且我們也需要將金鑰通道中現有金鑰的演算法資訊修改為手動變更金鑰環的開發人員。 為了達成我們的安全性需求，在這些情況下，資料保護系統具有 [密碼編譯靈活性](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption)的概念，可讓您安全地在多個密碼編譯演算法中使用單一 entropic 值。

大部分支援密碼編譯靈活性的系統，都是在承載內包含有關演算法的一些識別資訊。 演算法的 OID 通常是很好的候選項。 不過，我們遇到的其中一個問題是，有多種方法可以指定相同的演算法：「AES」 (CNG) 和 managed Aes、AesManaged、AesCryptoServiceProvider、AesCng 和 RijndaelManaged (指定特定參數) 類別實際上是相同的，因此我們必須將所有這些都對應到正確的 OID。 如果開發人員想要提供自訂的演算法 (或甚至是 AES！ ) 的另一種執行，他們就必須告訴我們它的 OID。 這個額外的註冊步驟讓系統設定特別頭痛。

回頭回頭，我們決定我們從錯誤的方向中找出問題。 OID 會告訴您該演算法是什麼，但我們並不在意這一點。 如果我們需要在兩個不同的演算法中安全地使用單一 entropic 值，我們就不需要知道演算法的實際用途。 我們真正關心的是它們的行為。 任何適當的對稱區塊加密演算法也都是強式隨機的 (PRP) ：修正輸入 (索引鍵、連結模式、IV、純文字) 和加密文字輸出的機率，與其他任何對稱式區塊加密演算法（指定相同的輸入）不同。 同樣地，任何適當的索引雜湊函式也是強式虛擬亂數函式 (PRF) ，而且在指定固定的輸入集時，其輸出會回應非常正面與任何其他的索引雜湊函數不同。

我們會使用此強式 Prp 和 PRFs 的概念來建立內容標頭。 此內容標頭基本上可作為任何指定作業所使用的演算法的穩定指紋，並提供資料保護系統所需的密碼編譯靈活性。 此標頭是可重現的，稍後用來作為子機碼 [衍生](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)程式的一部分。 有兩種不同的方式可根據基礎演算法的操作模式來建立內容標頭。

## <a name="cbc-mode-encryption--hmac-authentication"></a>CBC 模式加密 + HMAC 驗證

<a name="data-protection-implementation-context-headers-cbc-components"></a>

內容標頭包含下列元件：

* [16 位]值 00 00，這是標示為「CBC 加密 + HMAC 驗證」的標記。

* [32 位]對稱區塊加密演算法的金鑰長度 (位元組、位元組由大到小的) 。

* [32 位]區塊大小 (對稱區塊加密演算法的位元組位元組由大到小的) 。

* [32 位]HMAC 演算法的金鑰長度 (位元組、位元組由大到小的) 。  (目前的金鑰大小一律符合摘要大小。 ) 

* [32 位]以位元組為單位的摘要大小 (HMAC 演算法的位元組由) 大到小。

* `EncCBC(K_E, IV, "")`，此為對稱區塊加密演算法的輸出，指定空字串輸入，其中 IV 是全零的向量。 的結構 `K_E` 如下所述。

* `MAC(K_H, "")`，這是指定空字串輸入的 HMAC 演算法輸出。 的結構 `K_H` 如下所述。

在理想的情況下，我們可以傳遞和的全零向量 `K_E` `K_H` 。 不過，在執行任何)  (作業之前，我們想要避免基礎演算法檢查是否存在弱式金鑰，而不是使用簡單或可重複的模式，例如全零的向量。

相反地，我們會在計數器模式中使用 NIST SP800-108 KDF (查看 [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf)，Sec. 5.1) 具有長度為零的索引鍵、標籤和內容，以及 HMACSHA512 作為基礎 PRF。 我們會衍生 `| K_E | + | K_H |` 位元組的輸出，然後將結果分解為 `K_E` 和 `K_H` 本身。 這會以數學方式表示，如下所示。

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a>範例： AES-192-CBC + HMACSHA256

例如，假設對稱式區塊加密演算法是 AES-192-CBC 且驗證演算法為 HMACSHA256 的情況。 系統會使用下列步驟來產生內容標頭。

First、let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` 、where `| K_E | = 192 bits` 和 `| K_H | = 256 bits` per 指定的演算法。 這會導致 `K_E = 5BB6..21DD` `K_H = A04A..00A9` 下列範例中的和：

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

接下來，計算 `Enc_CBC (K_E, IV, "")` 指定和以上的 AES-192-CBC `IV = 0*` `K_E` 。

`result := F474B1872B3B53E4721DE19C0841DB6F`

接下來，請計算 `MAC(K_H, "")` 上述指定的 HMACSHA256 `K_H` 。

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

這會產生以下的完整內容標頭：

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

此內容標頭是驗證加密演算法組的指紋， (AES-192-CBC 加密 + HMACSHA256 驗證) 。 [上述](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components)元件如下所述：

* 標記 `(00 00)`

* 區塊加密金鑰長度 `(00 00 00 18)`

* 區塊加密區塊大小 `(00 00 00 10)`

* HMAC 金鑰長度 `(00 00 00 20)`

* HMAC 摘要大小 `(00 00 00 20)`

* 區塊加密 PRP 輸出 `(F4 74 - DB 6F)` 和

* HMAC PRF 輸出 `(D4 79 - end)` 。

> [!NOTE]
> CBC 模式加密 + HMAC 驗證內容標頭的建立方式相同，不論演算法的執行是由 Windows CNG 或 managed SymmetricAlgorithm 和 KeyedHashAlgorithm 類型提供。 如此一來，在不同作業系統上執行的應用程式就能可靠地產生相同的內容標頭，即使在作業系統之間的演算法是不同的。  (實務上，KeyedHashAlgorithm 不必是正確的 HMAC。 它可以是任何金鑰雜湊演算法類型。 ) 

### <a name="example-3des-192-cbc--hmacsha1"></a>範例： 3DES-192-CBC + HMACSHA1

First、let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` 、where `| K_E | = 192 bits` 和 `| K_H | = 160 bits` per 指定的演算法。 這會導致 `K_E = A219..E2BB` `K_H = DC4A..B464` 下列範例中的和：

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

接下來，計算 `Enc_CBC (K_E, IV, "")` 所提供的 3des-192-CBC， `IV = 0*` `K_E` 如上所示。

`result := ABB100F81E53E10E`

接下來，請計算 `MAC(K_H, "")` 上述指定的 HMACSHA1 `K_H` 。

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

這會產生完整的內容標頭，其為已驗證加密演算法組的指紋 (3DES-192-CBC encryption + HMACSHA1 驗證) ，如下所示：

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

元件的細分方式如下：

* 標記 `(00 00)`

* 區塊加密金鑰長度 `(00 00 00 18)`

* 區塊加密區塊大小 `(00 00 00 08)`

* HMAC 金鑰長度 `(00 00 00 14)`

* HMAC 摘要大小 `(00 00 00 14)`

* 區塊加密 PRP 輸出 `(AB B1 - E1 0E)` 和

* HMAC PRF 輸出 `(76 EB - end)` 。

## <a name="galoiscounter-mode-encryption--authentication"></a>Galois/計數器模式加密 + 驗證

內容標頭包含下列元件：

* [16 位]值 00 01，這是表示「GCM 加密 + 驗證」的標記。

* [32 位]對稱區塊加密演算法的金鑰長度 (位元組、位元組由大到小的) 。

* [32 位]在經過驗證的加密作業期間，nonce 大小 (以位元組為單位、以位元組為單位的) 使用。 針對我們的系統 (，這會在 nonce 大小 = 96 位修正。 ) 

* [32 位]區塊大小 (對稱區塊加密演算法的位元組位元組由大到小的) 。 針對 GCM (，這會在區塊大小 = 128 個位修正。 ) 

* [32 位]驗證標記大小 (以位元組為單位，由已驗證加密函式產生的位元組由) 大到小。 針對我們的系統 (，這會在標記大小 = 128 個位修正。 ) 

* [128 位]的標記 `Enc_GCM (K_E, nonce, "")` ，也就是對稱區塊加密演算法的輸出，指定空字串輸入，其中 nonce 是96位全零的向量。

`K_E` 是使用與 CBC 加密 + HMAC 驗證案例中的相同機制所衍生。 不過，由於這裡沒有任何 `K_H` 作用，我們基本上會有 `| K_H | = 0` ，而且演算法會折迭到以下形式。

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a>範例： AES-256-GCM

First、let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` 、where `| K_E | = 256 bits` 。

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

接下來，計算 `Enc_GCM (K_E, nonce, "")` 指定和以上的 AES-256-GCM 的驗證標記 `nonce = 096` `K_E` 。

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

這會產生以下的完整內容標頭：

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

元件的細分方式如下：

* 標記 `(00 01)`

* 區塊加密金鑰長度 `(00 00 00 20)`

* nonce 大小 `(00 00 00 0C)`

* 區塊加密區塊大小 `(00 00 00 10)`

* 驗證標記大小 `(00 00 00 10)` 和

* 執行區塊密碼的驗證標記 `(E7 DC - end)` 。
