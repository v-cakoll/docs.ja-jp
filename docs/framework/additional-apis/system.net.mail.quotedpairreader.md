---
title: QuotedPairReader クラス (System.Net)
ms.date: 06/12/2020
ms.technology: dotnet-networking
topic_type:
- apiref
api_name:
- System.Net.QuotedPairReader
- System.Net.QuotedPairReader.CountQuotedChars
api_location:
- System.dll
api_type:
- Assembly
ms.openlocfilehash: 9b0bf835a34eecc651894f4336648b73a81b665c
ms.sourcegitcommit: 45c8eed045779b70a47b23169897459d0323dc89
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2020
ms.locfileid: "84990537"
---
# <a name="quotedpairreader-class"></a><span data-ttu-id="b6c51-102">QuotedPairReader クラス</span><span class="sxs-lookup"><span data-stu-id="b6c51-102">QuotedPairReader class</span></span>

<span data-ttu-id="b6c51-103">引用符で囲まれた文字列で引用符で囲まれた文字 (エスケープ) を決定します。</span><span class="sxs-lookup"><span data-stu-id="b6c51-103">Determines which characters are quoted (escaped) in a quoted string.</span></span> <span data-ttu-id="b6c51-104">このクラスは継承できません。</span><span class="sxs-lookup"><span data-stu-id="b6c51-104">This class cannot be inherited.</span></span>

```csharp
internal static class QuotedPairReader
```

> [!WARNING]
> <span data-ttu-id="b6c51-105">このクラスは内部的なものであり、コードで直接使用するためのものではありません。</span><span class="sxs-lookup"><span data-stu-id="b6c51-105">This class is internal and is not meant to be used directly in your code.</span></span>
>
> <span data-ttu-id="b6c51-106">Microsoft では、どのような状況でも、実稼働アプリケーションでのこのクラスの使用はサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="b6c51-106">Microsoft does not support the use of this class in a production application under any circumstance.</span></span>

## <a name="countquotedchars-method"></a><span data-ttu-id="b6c51-107">CountQuotedChars メソッド</span><span class="sxs-lookup"><span data-stu-id="b6c51-107">CountQuotedChars method</span></span>

<span data-ttu-id="b6c51-108">指定した文字列に含まれる、前に引用符で囲まれた複数の円記号を含む、連続する引用符で囲まれた文字の数をカウントします。</span><span class="sxs-lookup"><span data-stu-id="b6c51-108">Counts the number of consecutive quoted characters, including multiple preceding quoted backslashes, in the specified string.</span></span> <span data-ttu-id="b6c51-109">たとえば、文字列とのインデックスを指定した場合、メソッドはを `a\\\b` `4` 返します。これは、 `4` `b` が引用符で囲まれており、その前に3つの円記号があるためです。</span><span class="sxs-lookup"><span data-stu-id="b6c51-109">For example, given the string `a\\\b` and an index of `4`, the method returns `4`, because `b` is quoted and so are the three preceding backslashes.</span></span>

```csharp
internal static int CountQuotedChars(string data, int index, bool permitUnicodeEscaping)
```

### <a name="parameters"></a><span data-ttu-id="b6c51-110">パラメーター</span><span class="sxs-lookup"><span data-stu-id="b6c51-110">Parameters</span></span>

- <span data-ttu-id="b6c51-111">`data` <xref:System.String></span><span class="sxs-lookup"><span data-stu-id="b6c51-111">`data` <xref:System.String></span></span>

  <span data-ttu-id="b6c51-112">連続する引用符で囲まれた文字をカウントするデータ文字列。</span><span class="sxs-lookup"><span data-stu-id="b6c51-112">The data string in which to count consecutive quoted characters.</span></span>

- <span data-ttu-id="b6c51-113">`index` <xref:System.Int32></span><span class="sxs-lookup"><span data-stu-id="b6c51-113">`index` <xref:System.Int32></span></span>

  <span data-ttu-id="b6c51-114">連続する引用符で囲まれた文字を含む、指定した文字列内の位置。</span><span class="sxs-lookup"><span data-stu-id="b6c51-114">The position in the specified string up to and including which consecutive quoted characters should be counted.</span></span>

- <span data-ttu-id="b6c51-115">`permitUnicodeEscaping` <xref:System.Boolean></span><span class="sxs-lookup"><span data-stu-id="b6c51-115">`permitUnicodeEscaping` <xref:System.Boolean></span></span>

  <span data-ttu-id="b6c51-116">`true`Unicode 文字のエスケープを許可するにはそれ以外の場合は `false` 。</span><span class="sxs-lookup"><span data-stu-id="b6c51-116">`true` to permit Unicode characters to be escaped; otherwise, `false`.</span></span>

### <a name="return-value"></a><span data-ttu-id="b6c51-117">戻り値</span><span class="sxs-lookup"><span data-stu-id="b6c51-117">Return value</span></span>

<xref:System.Int32?displayProperty=nameWithType>

<span data-ttu-id="b6c51-118">`0`指定したインデックス位置にある文字がエスケープされない場合は。それ以外の場合は、の文字を含む、連続する引用符で囲まれた文字の数 `index` 。</span><span class="sxs-lookup"><span data-stu-id="b6c51-118">`0` if the character at the specified index is not escaped; otherwise, the number of consecutive quoted characters up to and including the character at `index`.</span></span>

### <a name="exceptions"></a><span data-ttu-id="b6c51-119">例外</span><span class="sxs-lookup"><span data-stu-id="b6c51-119">Exceptions</span></span>

<xref:System.FormatException?displayProperty=nameWithType>

<span data-ttu-id="b6c51-120">エスケープされた Unicode 文字が見つかりましたが、許可されていません。</span><span class="sxs-lookup"><span data-stu-id="b6c51-120">An escaped Unicode character was found but is not permitted.</span></span>

## <a name="requirements"></a><span data-ttu-id="b6c51-121">必要条件</span><span class="sxs-lookup"><span data-stu-id="b6c51-121">Requirements</span></span>

<span data-ttu-id="b6c51-122">**名前空間:** <xref:System.Net></span><span class="sxs-lookup"><span data-stu-id="b6c51-122">**Namespace:** <xref:System.Net></span></span>

<span data-ttu-id="b6c51-123">**アセンブリ:** システム (System.dll)</span><span class="sxs-lookup"><span data-stu-id="b6c51-123">**Assembly:** System (in System.dll)</span></span>