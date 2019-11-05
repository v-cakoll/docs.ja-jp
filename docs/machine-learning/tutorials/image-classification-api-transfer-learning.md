---
title: 'チュートリアル: 転移学習を利用した自動ビジュアル検査'
description: このチュートリアルでは、転移学習を利用し、ML.NET で TensorFlow ディープ ラーニング モデルをトレーニングする方法を説明します。具体的には、画像検出 API を利用し、コンクリートの表面の画像をひび割れあり/ひび割れなしに分類します。
author: luisquintanilla
ms.author: luquinta
ms.date: 10/25/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: b8aec80134188811eb80ad1394e5a64d65a3c6f0
ms.sourcegitcommit: 9b2ef64c4fc10a4a10f28a223d60d17d7d249ee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/26/2019
ms.locfileid: "72961954"
---
# <a name="tutorial-automated-visual-inspection-using-transfer-learning-with-the-mlnet-image-classification-api"></a><span data-ttu-id="01280-103">チュートリアル: 転移学習と ML.NET Image Classification API を利用した自動ビジュアル検査</span><span class="sxs-lookup"><span data-stu-id="01280-103">Tutorial: Automated visual inspection using transfer learning with the ML.NET Image Classification API</span></span>

<span data-ttu-id="01280-104">転移学習、事前トレーニング済みの TensorFlow モデル、ML.NET Image Classification API を利用してカスタム ディープ ラーニング モデルをトレーニングし、コンクリートの表面の画像をひび割れあり/ひび割れなしに分類する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="01280-104">Learn how to train a custom deep learning model using transfer learning, a pretrained TensorFlow model and the ML.NET Image Classification API to classify images of concrete surfaces as cracked or uncracked.</span></span>

> [!NOTE]
> <span data-ttu-id="01280-105">このチュートリアルでは、プレビュー版の ML.NET Image Classification API を使用します。</span><span class="sxs-lookup"><span data-stu-id="01280-105">This tutorial uses a preview version of the ML.NET Image Classification API.</span></span>

<span data-ttu-id="01280-106">このチュートリアルでは、次の作業を行う方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="01280-106">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
>
> - <span data-ttu-id="01280-107">問題を把握する</span><span class="sxs-lookup"><span data-stu-id="01280-107">Understand the problem</span></span>
> - <span data-ttu-id="01280-108">ML.NET Image Classification API について</span><span class="sxs-lookup"><span data-stu-id="01280-108">Learn about ML.NET Image Classification API</span></span>
> - <span data-ttu-id="01280-109">事前トレーニング済みモデルについて</span><span class="sxs-lookup"><span data-stu-id="01280-109">Understand the pretrained model</span></span>
> - <span data-ttu-id="01280-110">転移学習を利用し、カスタム TensorFlow 画像分類モデルをトレーニングする</span><span class="sxs-lookup"><span data-stu-id="01280-110">Use transfer learning to train a custom TensorFlow image classification model</span></span>
> - <span data-ttu-id="01280-111">カスタム モデルによる画像の分類</span><span class="sxs-lookup"><span data-stu-id="01280-111">Classify images with the custom model</span></span>

## <a name="prerequisites"></a><span data-ttu-id="01280-112">必須コンポーネント</span><span class="sxs-lookup"><span data-stu-id="01280-112">Prerequisites</span></span>

- <span data-ttu-id="01280-113">[Visual Studio 2017 15.6 以降](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2017)が ".NET Core クロスプラット フォーム開発" とともにインストールされていること。</span><span class="sxs-lookup"><span data-stu-id="01280-113">[Visual Studio 2017 15.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2017) with the ".NET Core cross-platform development" workload installed.</span></span>

## <a name="image-classification-transfer-learning-sample-overview"></a><span data-ttu-id="01280-114">画像分類の転移学習の概要</span><span class="sxs-lookup"><span data-stu-id="01280-114">Image classification transfer learning sample overview</span></span>

<span data-ttu-id="01280-115">このサンプルは、事前トレーニング済みディープ ラーニング TensorFlow モデルを利用して画像を分類する C# .NET Core コンソール アプリケーションです。</span><span class="sxs-lookup"><span data-stu-id="01280-115">This sample is a C# .NET Core console application that classifies images using a pretrained deep learning TensorFlow model.</span></span> <span data-ttu-id="01280-116">このサンプルのコードについては、GitHub の [dotnet/machinelearning-samples リポジトリ](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="01280-116">The code for this sample can be found on the [dotnet/machinelearning-samples repository](https://github.com/dotnet/machinelearning-samples/tree/master/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary) on GitHub.</span></span>

## <a name="understand-the-problem"></a><span data-ttu-id="01280-117">問題を把握する</span><span class="sxs-lookup"><span data-stu-id="01280-117">Understand the problem</span></span>

<span data-ttu-id="01280-118">画像分類はコンピューターのビジョンの問題です。</span><span class="sxs-lookup"><span data-stu-id="01280-118">Image classification is a computer vision problem.</span></span> <span data-ttu-id="01280-119">画像分類では、画像を入力として受け取り、指示されたクラスにそれを分類します。</span><span class="sxs-lookup"><span data-stu-id="01280-119">Image classification takes an image as input and categorizes it into a prescribed class.</span></span> <span data-ttu-id="01280-120">画像分類はたとえば次のシナリオで役立ちます。</span><span class="sxs-lookup"><span data-stu-id="01280-120">Some scenarios where image classification is useful include:</span></span>

- <span data-ttu-id="01280-121">顔認識</span><span class="sxs-lookup"><span data-stu-id="01280-121">Facial recognition</span></span>
- <span data-ttu-id="01280-122">感情検出</span><span class="sxs-lookup"><span data-stu-id="01280-122">Emotion detection</span></span>
- <span data-ttu-id="01280-123">医療診断</span><span class="sxs-lookup"><span data-stu-id="01280-123">Medical diagnosis</span></span>
- <span data-ttu-id="01280-124">陸標検出</span><span class="sxs-lookup"><span data-stu-id="01280-124">Landmark detection</span></span>

<span data-ttu-id="01280-125">このチュートリアルでは、カスタム画像分類モデルをトレーニングし、橋床の映像を自動検査し、ひび割れしている構造物を特定します。</span><span class="sxs-lookup"><span data-stu-id="01280-125">This tutorial trains a custom image classification model to perform automated visual inspection of bridge decks to identify structures that are damaged by cracks.</span></span>

## <a name="mlnet-image-classification-api"></a><span data-ttu-id="01280-126">ML.NET Image Classification API</span><span class="sxs-lookup"><span data-stu-id="01280-126">ML.NET Image Classification API</span></span>

<span data-ttu-id="01280-127">ML.NET からは、画像分類を実行するさまざまな方法が与えられます。</span><span class="sxs-lookup"><span data-stu-id="01280-127">ML.NET provides various ways of performing image classification.</span></span> <span data-ttu-id="01280-128">このチュートリアルでは、Image Classification API を使用する転移学習を応用します。</span><span class="sxs-lookup"><span data-stu-id="01280-128">This tutorial applies transfer learning using the Image Classification API.</span></span> <span data-ttu-id="01280-129">Image Classification API では [TensorFlow.NET](https://github.com/SciSharp/TensorFlow.NET) を利用します。これは TensorFlow C++ API 用に C# バインディングを提供する低レベル ライブラリです。</span><span class="sxs-lookup"><span data-stu-id="01280-129">The Image Classification API makes use of [TensorFlow.NET](https://github.com/SciSharp/TensorFlow.NET), a low-level library that provides C# bindings for the TensorFlow C++ API.</span></span>

## <a name="what-is-transfer-learning"></a><span data-ttu-id="01280-130">転移学習とは何か</span><span class="sxs-lookup"><span data-stu-id="01280-130">What is transfer learning?</span></span>

<span data-ttu-id="01280-131">転移学習では、ある問題を解決することで得られた知識が関連する別の問題に応用されます。</span><span class="sxs-lookup"><span data-stu-id="01280-131">Transfer learning applies knowledge gained from solving one problem to another related problem.</span></span>

<span data-ttu-id="01280-132">ディープ ラーニング モデルを最初からトレーニングするには、いくつかのパラメーター、大量のラベル付きトレーニング データ、膨大な量の計算リソース (数百時間の GPU) を設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01280-132">Training a deep learning model from scratch requires setting several parameters, a large amount of labeled training data, and a vast amount of compute resources (hundreds of GPU hours).</span></span> <span data-ttu-id="01280-133">事前トレーニング済みモデルと転移学習を使用すると、トレーニング プロセスをショートカットできます。</span><span class="sxs-lookup"><span data-stu-id="01280-133">Using a pretrained model along with transfer learning allows you to shortcut the training process.</span></span> 

## <a name="training-process"></a><span data-ttu-id="01280-134">トレーニング プロセス</span><span class="sxs-lookup"><span data-stu-id="01280-134">Training process</span></span>

<span data-ttu-id="01280-135">Image Classification API のトレーニング プロセスは、事前トレーニング済み TensorFlow モデルを読み込むことから始まります。</span><span class="sxs-lookup"><span data-stu-id="01280-135">The Image Classification API starts the training process by loading a pretrained TensorFlow model.</span></span> <span data-ttu-id="01280-136">トレーニング プロセスは次の 2 つのステップで構成されます。</span><span class="sxs-lookup"><span data-stu-id="01280-136">The training process consists of two steps:</span></span>

1. <span data-ttu-id="01280-137">ボトルネック フェーズ</span><span class="sxs-lookup"><span data-stu-id="01280-137">Bottleneck phase</span></span>
2. <span data-ttu-id="01280-138">トレーニング フェーズ</span><span class="sxs-lookup"><span data-stu-id="01280-138">Training phase</span></span>

![トレーニング ステップ](./media/image-classification-api-transfer-learning/training.png)

### <a name="bottleneck-phase"></a><span data-ttu-id="01280-140">ボトルネック フェーズ</span><span class="sxs-lookup"><span data-stu-id="01280-140">Bottleneck phase</span></span>

<span data-ttu-id="01280-141">ボトルネック フェーズの間、事前トレーニング済みモデルの固定レイヤーに対して、一連のトレーニング画像が読み込まれ、ピクセル値が入力として使用されます。</span><span class="sxs-lookup"><span data-stu-id="01280-141">During the bottleneck phase, the set of training images is loaded and the pixel values are used as input, or features, for the frozen layers of the pretrained model.</span></span> <span data-ttu-id="01280-142">固定レイヤーには、非公式にボトルネック レイヤーと呼ばれている最後から 2 番目のレイヤーまでに含まれる、ニューラル ネットワーク内のあらゆるレイヤーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="01280-142">The frozen layers include all of the layers in the neural network up to the penultimate layer, informally known as the bottleneck layer.</span></span> <span data-ttu-id="01280-143">このようなレイヤーは、その上でトレーニングが発生せず、操作がパススルーであるため、固定と呼ばれています。</span><span class="sxs-lookup"><span data-stu-id="01280-143">These layers are referred to as frozen because no training will occur on these layers and operations are pass-through.</span></span> <span data-ttu-id="01280-144">モデルでさまざまなクラスを区別するのに役立つ低レベル パターンが計算されるのがこの固定レイヤーです。</span><span class="sxs-lookup"><span data-stu-id="01280-144">It's at these frozen layers where the lower-level patterns that help a model differentiate between the different classes are computed.</span></span> <span data-ttu-id="01280-145">レイヤーの数が多ければ多いほど、この手順は計算処理がそれだけ激しくなります。</span><span class="sxs-lookup"><span data-stu-id="01280-145">The larger the number of layers, the more computationally intensive this step is.</span></span> <span data-ttu-id="01280-146">幸いなことに、これは 1 回限りの計算であるため、結果をキャッシュし、後で異なるパラメーターを使用して実験するときに利用できます。</span><span class="sxs-lookup"><span data-stu-id="01280-146">Fortunately, since this is a one-time calculation, the results can be cached and used in later runs when experimenting with different parameters.</span></span>

### <a name="training-phase"></a><span data-ttu-id="01280-147">トレーニング フェーズ</span><span class="sxs-lookup"><span data-stu-id="01280-147">Training phase</span></span>

<span data-ttu-id="01280-148">ボトルネック フェーズからの出力値が計算されると、それが入力として利用され、モデルの最終的レイヤーが保持されます。</span><span class="sxs-lookup"><span data-stu-id="01280-148">Once the output values from the bottleneck phase are computed, they are used as input to retrain the final layer of the model.</span></span> <span data-ttu-id="01280-149">このプロセスは繰り返され、モデル パラメーターで指定された回数だけ実行されます。</span><span class="sxs-lookup"><span data-stu-id="01280-149">This process is iterative and runs for the number of times specified by model parameters.</span></span> <span data-ttu-id="01280-150">実行のたびに、損失と精度が評価されます。</span><span class="sxs-lookup"><span data-stu-id="01280-150">During each run, the loss and accuracy are evaluated.</span></span> <span data-ttu-id="01280-151">次に、損失を最小限に抑え、精度を最大限に高めることを目的としてモデルを改善するための適切な調整が行われます。</span><span class="sxs-lookup"><span data-stu-id="01280-151">Then, the appropriate adjustments are made to improve the model with the goal of minimizing the loss and maximizing the accuracy.</span></span> <span data-ttu-id="01280-152">トレーニングが完了すると、2 つのモデル形式が出力されます。</span><span class="sxs-lookup"><span data-stu-id="01280-152">Once training is finished, two model formats are output.</span></span> <span data-ttu-id="01280-153">そのうちの 1 つがモデルの `.pb` バージョンであり、もう 1 つがモデルの `.zip` ML.NET シリアル化バージョンです。</span><span class="sxs-lookup"><span data-stu-id="01280-153">One of them is the `.pb` version of the model and the other is the `.zip` ML.NET serialized version of the model.</span></span> <span data-ttu-id="01280-154">ML.NET でサポートされる環境で作業するとき、モデルの `.zip` バージョンを使用することが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="01280-154">When working in environments supported by ML.NET, it is recommended to use the `.zip` version of the model.</span></span> <span data-ttu-id="01280-155">ただし、ML.NET がサポートされない環境では、`.pb` バージョンを使用できます。</span><span class="sxs-lookup"><span data-stu-id="01280-155">However, in environments where ML.NET is not supported, you have the option of using the `.pb` version.</span></span>

## <a name="understand-the-pretrained-model"></a><span data-ttu-id="01280-156">事前トレーニング済みモデルについて</span><span class="sxs-lookup"><span data-stu-id="01280-156">Understand the pretrained model</span></span>

<span data-ttu-id="01280-157">このチュートリアルで使用される事前トレーニング済みモデルは、Residual Network (ResNet) v2 モデルの 101 レイヤー型です。</span><span class="sxs-lookup"><span data-stu-id="01280-157">The pretrained model used in this tutorial is the 101-layer variant of the Residual Network (ResNet) v2 model.</span></span> <span data-ttu-id="01280-158">元のモデルは、画像を 1,000 個のカテゴリに分類する目的でトレーニングされています。</span><span class="sxs-lookup"><span data-stu-id="01280-158">The original model is trained to classify images into a thousand categories.</span></span> <span data-ttu-id="01280-159">このモデルでは、サイズ 224 x 224 の画像を入力として受け取り、トレーニングしたクラス別にクラス確率を出力します。</span><span class="sxs-lookup"><span data-stu-id="01280-159">The model takes as input an image of size 224 x 224 and outputs the class probabilities for each of the classes it's trained on.</span></span> <span data-ttu-id="01280-160">このモデルの一部は、2 つのクラス間で予測する目的でカスタム画像を利用して新しいモデルをトレーニングするために使用されます。</span><span class="sxs-lookup"><span data-stu-id="01280-160">Part of this model is used to train a new model using custom images to make predictions between two classes.</span></span> 

## <a name="create-console-application"></a><span data-ttu-id="01280-161">コンソール アプリケーションの作成</span><span class="sxs-lookup"><span data-stu-id="01280-161">Create console application</span></span>

<span data-ttu-id="01280-162">転移学習と Image Classification API の概要を理解できたところで、アプリケーションを構築しましょう。</span><span class="sxs-lookup"><span data-stu-id="01280-162">Now that you have a general understanding of transfer learning and the Image Classification API, it's time to build the application.</span></span>

1. <span data-ttu-id="01280-163">"DeepLearning_ImageClassification_Binary" という名前の **C# .NET Core コンソール アプリケーション**を作成します。</span><span class="sxs-lookup"><span data-stu-id="01280-163">Create a **C# .NET Core Console Application** called "DeepLearning_ImageClassification_Binary".</span></span>
1. <span data-ttu-id="01280-164">**Microsoft.ML 1.4.0-preview2** NuGet パッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="01280-164">Install the **Microsoft.ML 1.4.0-preview2** NuGet Package:</span></span>
    1. <span data-ttu-id="01280-165">ソリューション エクスプローラーで、プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="01280-165">In Solution Explorer, right-click on your project and select **Manage NuGet Packages**.</span></span>
    1. <span data-ttu-id="01280-166">[パッケージ ソース] として [nuget.org] を選択します。</span><span class="sxs-lookup"><span data-stu-id="01280-166">Choose "nuget.org" as the Package source.</span></span>
    1. <span data-ttu-id="01280-167">**[参照]** タブを選択します。</span><span class="sxs-lookup"><span data-stu-id="01280-167">Select the **Browse** tab.</span></span>
    1. <span data-ttu-id="01280-168">**[プレリリースを含める]** チェックボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="01280-168">Check the **Include prerelease** checkbox.</span></span>
    1. <span data-ttu-id="01280-169">**Microsoft.ML** を探します。</span><span class="sxs-lookup"><span data-stu-id="01280-169">Search for **Microsoft.ML**.</span></span>
    1. <span data-ttu-id="01280-170">**[インストール]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="01280-170">Select the **Install** button.</span></span>
    1. <span data-ttu-id="01280-171">**[変更のプレビュー]** ダイアログの **[OK]** を選択します。表示されているパッケージのライセンス条項に同意する場合は、 **[ライセンスの同意]** ダイアログの **[同意する]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="01280-171">Select the **OK** button on the **Preview Changes** dialog and then select the **I Accept** button on the **License Acceptance** dialog if you agree with the license terms for the packages listed.</span></span>
    1. <span data-ttu-id="01280-172">この手順を **Microsoft.ML.Dnn 0.16.0-preview2** と **Microsoft.ML.ImageAnalytics 1.4.0-preview2** に繰り返します。</span><span class="sxs-lookup"><span data-stu-id="01280-172">Repeat these steps for **Microsoft.ML.Dnn 0.16.0-preview2** and **Microsoft.ML.ImageAnalytics 1.4.0-preview2**.</span></span>

### <a name="prepare-and-understand-the-data"></a><span data-ttu-id="01280-173">データを準備して理解する</span><span class="sxs-lookup"><span data-stu-id="01280-173">Prepare and understand the data</span></span>

> [!NOTE]
> <span data-ttu-id="01280-174">このチュートリアルのデータセットの提供元: Maguire, Marc; Dorafshan, Sattar; and Thomas, Robert J., "SDNET2018:A concrete crack image dataset for machine learning applications" (2018).</span><span class="sxs-lookup"><span data-stu-id="01280-174">The datasets for this tutorial are from Maguire, Marc; Dorafshan, Sattar; and Thomas, Robert J., "SDNET2018: A concrete crack image dataset for machine learning applications" (2018).</span></span> <span data-ttu-id="01280-175">Browse all Datasets.</span><span class="sxs-lookup"><span data-stu-id="01280-175">Browse all Datasets.</span></span> <span data-ttu-id="01280-176">Paper 48.</span><span class="sxs-lookup"><span data-stu-id="01280-176">Paper 48.</span></span> <span data-ttu-id="01280-177">https://digitalcommons.usu.edu/all_datasets/48</span><span class="sxs-lookup"><span data-stu-id="01280-177">https://digitalcommons.usu.edu/all_datasets/48</span></span>

<span data-ttu-id="01280-178">SDNET2018 は、ひび割れあり/ひび割れなしのコンクリート構造物 (橋床、壁、歩道) に関する注釈を含む画像データセットです。</span><span class="sxs-lookup"><span data-stu-id="01280-178">SDNET2018 is an image dataset that contains annotations for cracked and non-cracked concrete structures (bridge decks, walls, and pavement).</span></span> 

![SDNET2018 データセットの橋床のサンプル](./media/image-classification-api-transfer-learning/sdnet2018decksamples.png)

<span data-ttu-id="01280-180">データは 3 つのサブディレクトリで整理されています。</span><span class="sxs-lookup"><span data-stu-id="01280-180">The data is organized in three subdirectories:</span></span>

- <span data-ttu-id="01280-181">D には橋床の画像が含まれています</span><span class="sxs-lookup"><span data-stu-id="01280-181">D contains bridge deck images</span></span>
- <span data-ttu-id="01280-182">P には歩道の画像が含まれています</span><span class="sxs-lookup"><span data-stu-id="01280-182">P contains pavement images</span></span>
- <span data-ttu-id="01280-183">W には壁の画像が含まれています</span><span class="sxs-lookup"><span data-stu-id="01280-183">W contains wall images</span></span>

<span data-ttu-id="01280-184">これらのサブディレクトリにはそれぞれ、追加のサブディレクトリが 2 つ含まれ、プレフィックスが付けられています。</span><span class="sxs-lookup"><span data-stu-id="01280-184">Each of these subdirectories contains two additional prefixed subdirectories:</span></span>

- <span data-ttu-id="01280-185">C はひび割れありの表面に使用されるプレフィックスです</span><span class="sxs-lookup"><span data-stu-id="01280-185">C is the prefix used for cracked surfaces</span></span>
- <span data-ttu-id="01280-186">U はひび割れなしの表面に使用されるプレフィックスです</span><span class="sxs-lookup"><span data-stu-id="01280-186">U is the prefix used for uncracked surfaces</span></span>

<span data-ttu-id="01280-187">このチュートリアルでは、橋床の画像のみ使用します。</span><span class="sxs-lookup"><span data-stu-id="01280-187">In this tutorial, only bridge deck images are used.</span></span>

1. <span data-ttu-id="01280-188">[データセット](https://github.com/dotnet/machinelearning-samples/raw/master/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/assets.zip)をダウンロードし、解凍します。</span><span class="sxs-lookup"><span data-stu-id="01280-188">Download the [dataset](https://github.com/dotnet/machinelearning-samples/raw/master/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/assets.zip) and unzip.</span></span>
1. <span data-ttu-id="01280-189">データ セット ファイルを保存するために、プロジェクトに "assets" という名前のディレクトリを作成します。</span><span class="sxs-lookup"><span data-stu-id="01280-189">Create a directory named "assets" in your project to save your dataset files.</span></span>
1. <span data-ttu-id="01280-190">先ほど解凍したディレクトリから *assets* ディレクトリに *CD* サブディレクトリと *UD* サブディレクトリをコピーします。</span><span class="sxs-lookup"><span data-stu-id="01280-190">Copy the *CD* and *UD* subdirectories from the recently unzipped directory to the *assets* directory.</span></span>

### <a name="create-input-and-output-classes"></a><span data-ttu-id="01280-191">入力クラスと出力クラスを作成する</span><span class="sxs-lookup"><span data-stu-id="01280-191">Create input and output classes</span></span>

1. <span data-ttu-id="01280-192">*Program.cs* ファイルを開き、ファイルの先頭にある既存の `using` ステートメントを次で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="01280-192">Open the *Program.cs* file and replace the existing `using` statements at the top of the file with the following:</span></span>

    [!code-csharp [ProgramUsings](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L1-L7)]

1. <span data-ttu-id="01280-193">*Program.cs* の `Program` クラスの下で `ImageData` という名前のクラスを作成します。</span><span class="sxs-lookup"><span data-stu-id="01280-193">Below the `Program` class in *Program.cs*, create a class called `ImageData`.</span></span> <span data-ttu-id="01280-194">このクラスは、最初に読み込んだデータを表わすために使用されます。</span><span class="sxs-lookup"><span data-stu-id="01280-194">This class is used to represent the initially loaded data.</span></span> 

    [!code-csharp [ImageDataClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L135-L140)]

    <span data-ttu-id="01280-195">`ImageData` には次のプロパティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="01280-195">`ImageData` contains the following properties:</span></span>

    - <span data-ttu-id="01280-196">`ImagePath` は、画像が保存されている完全修飾パスです。</span><span class="sxs-lookup"><span data-stu-id="01280-196">`ImagePath` is the fully qualified path where the image is stored.</span></span>
    - <span data-ttu-id="01280-197">`Label` は、画像が属するカテゴリです。</span><span class="sxs-lookup"><span data-stu-id="01280-197">`Label` is the category the image belongs to.</span></span> <span data-ttu-id="01280-198">これが予測する値です。</span><span class="sxs-lookup"><span data-stu-id="01280-198">This is the value to predict.</span></span>

1. <span data-ttu-id="01280-199">入力データと出力データのクラスを作成する</span><span class="sxs-lookup"><span data-stu-id="01280-199">Create classes for your input and output data</span></span>

    1. <span data-ttu-id="01280-200">`ImageData` クラスの下で、`ModelInput` という名前の新しいクラスに入力データのスキーマを定義します。</span><span class="sxs-lookup"><span data-stu-id="01280-200">Below the `ImageData` class, define the schema of your input data in a new class called `ModelInput`.</span></span>

        [!code-csharp [ModelInputClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L142-L151)]

        <span data-ttu-id="01280-201">`ModelInput` には次のプロパティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="01280-201">`ModelInput` contains the following properties:</span></span>

        - <span data-ttu-id="01280-202">`ImagePath` は、画像が保存されている完全修飾パスです。</span><span class="sxs-lookup"><span data-stu-id="01280-202">`ImagePath` is the fully qualified path where the image is stored.</span></span> 
        - <span data-ttu-id="01280-203">`Label` は、画像が属するカテゴリです。</span><span class="sxs-lookup"><span data-stu-id="01280-203">`Label` is the category the image belongs to.</span></span> <span data-ttu-id="01280-204">これが予測する値です。</span><span class="sxs-lookup"><span data-stu-id="01280-204">This is the value to predict.</span></span>
        - <span data-ttu-id="01280-205">`Image` は画像の `byte[]` 表現です。</span><span class="sxs-lookup"><span data-stu-id="01280-205">`Image` is the `byte[]` representation of the image.</span></span> <span data-ttu-id="01280-206">モデルでは、トレーニングのために画像データがこの種類になることが求められます。</span><span class="sxs-lookup"><span data-stu-id="01280-206">The model expects image data to be of this type for training.</span></span>
        - <span data-ttu-id="01280-207">`LabelAsKey` は `Label` の数値表現です。</span><span class="sxs-lookup"><span data-stu-id="01280-207">`LabelAsKey` is the numerical representation of the `Label`.</span></span> 

        <span data-ttu-id="01280-208">`Image` と `LabelAsKey` のみ、モデルのトレーニングと予測に使用されます。</span><span class="sxs-lookup"><span data-stu-id="01280-208">Only `Image` and `LabelAsKey` are used to train the model and make predictions.</span></span> <span data-ttu-id="01280-209">`ImagePath` プロパティと `Label` プロパティは、元の画像ファイルの名前やカテゴリにアクセスするときに便利なため、維持されます。</span><span class="sxs-lookup"><span data-stu-id="01280-209">The `ImagePath` and `Label` properties are kept for convenience to access the original image file name and category.</span></span>

    1. <span data-ttu-id="01280-210">次に、`ModelInput` クラスの下で、`ModelOutput` という名前の新しいクラスに出力データのスキーマを定義します。</span><span class="sxs-lookup"><span data-stu-id="01280-210">Then, below the `ModelInput` class, define the schema of your output data in a new class called `ModelOutput`.</span></span> 

        [!code-csharp [ModelOutputClass](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L153-L160)]

        <span data-ttu-id="01280-211">`ModelOutput` には次のプロパティが含まれます。</span><span class="sxs-lookup"><span data-stu-id="01280-211">`ModelOutput` contains the following properties:</span></span>

        - <span data-ttu-id="01280-212">`ImagePath` は、画像が保存されている完全修飾パスです。</span><span class="sxs-lookup"><span data-stu-id="01280-212">`ImagePath` is the fully qualified path where the image is stored.</span></span>
        - <span data-ttu-id="01280-213">`Label` は、画像が属する元のカテゴリです。</span><span class="sxs-lookup"><span data-stu-id="01280-213">`Label` is the original category the image belongs to.</span></span> <span data-ttu-id="01280-214">これが予測する値です。</span><span class="sxs-lookup"><span data-stu-id="01280-214">This is the value to predict.</span></span> 
        - <span data-ttu-id="01280-215">`PredictedLabel` はモデルで予測された値です。</span><span class="sxs-lookup"><span data-stu-id="01280-215">`PredictedLabel` is the value predicted by the model.</span></span>

        <span data-ttu-id="01280-216">`ModelInput` と同様に、予測には `PredictedLabel` のみが必要になります。モデルによる予測が含まれているためです。</span><span class="sxs-lookup"><span data-stu-id="01280-216">Similar to `ModelInput`, only the `PredictedLabel` is required to make predictions since it contains the prediction made by the model.</span></span> <span data-ttu-id="01280-217">`ImagePath` プロパティと `Label` プロパティは、元の画像ファイルの名前やカテゴリにアクセスするときに便利なため、保持されます。</span><span class="sxs-lookup"><span data-stu-id="01280-217">The `ImagePath` and `Label` properties are retained for convenience to access the original image file name and category.</span></span>

### <a name="define-paths-and-initialize-variables"></a><span data-ttu-id="01280-218">パスを定義し、変数を初期化する</span><span class="sxs-lookup"><span data-stu-id="01280-218">Define paths and initialize variables</span></span>

1. <span data-ttu-id="01280-219">`Main` メソッド内でアセットの場所を定義します。</span><span class="sxs-lookup"><span data-stu-id="01280-219">Inside the `Main` method, define the location of your assets.</span></span>

    [!code-csharp [DefinePaths](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L15-L16)]

1. <span data-ttu-id="01280-220">次に、[MLContext](xref:Microsoft.ML.MLContext) の新しいインスタンスを使用して `mlContext` 変数を初期化します。</span><span class="sxs-lookup"><span data-stu-id="01280-220">Then, initialize the `mlContext` variable with a new instance of [MLContext](xref:Microsoft.ML.MLContext).</span></span>

    [!code-csharp [MLContext](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L18)]

<span data-ttu-id="01280-221">[MLContext](xref:Microsoft.ML.MLContext) クラスは、すべての ML.NET 操作の開始点で、mlContext を初期化することで、モデル作成ワークフローのオブジェクト間で共有できる新しい ML.NET 環境が作成されます。</span><span class="sxs-lookup"><span data-stu-id="01280-221">The [MLContext](xref:Microsoft.ML.MLContext) class is a starting point for all ML.NET operations, and initializing mlContext creates a new ML.NET environment that can be shared across the model creation workflow objects.</span></span> <span data-ttu-id="01280-222">これは Entity Framework における `DBContext` と概念的には同じです。</span><span class="sxs-lookup"><span data-stu-id="01280-222">It's similar, conceptually, to `DBContext` in Entity Framework.</span></span>

## <a name="load-the-data"></a><span data-ttu-id="01280-223">データを読み込む</span><span class="sxs-lookup"><span data-stu-id="01280-223">Load the data</span></span>

### <a name="create-data-loading-utility-method"></a><span data-ttu-id="01280-224">データ読み込みユーティリティ メソッドを作成する</span><span class="sxs-lookup"><span data-stu-id="01280-224">Create data loading utility method</span></span>

<span data-ttu-id="01280-225">イメージは 2 つのサブディレクトリに保存されます。</span><span class="sxs-lookup"><span data-stu-id="01280-225">The images are stored in two subdirectories.</span></span> <span data-ttu-id="01280-226">データを読み込む前に、`ImageData` オブジェクトの一覧に書式設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01280-226">Before loading the data, it needs to be formatted into a list of `ImageData` objects.</span></span> <span data-ttu-id="01280-227">そのためには、`LoadImagesFromDirectory` メソッドを `Main` メソッドの下で作成します。</span><span class="sxs-lookup"><span data-stu-id="01280-227">To do so, create the `LoadImagesFromDirectory` method below the `Main` method.</span></span>

```csharp
public static IEnumerable<ImageData> LoadImagesFromDirectory(string folder, bool useFolderNameAsLabel = true)
{

}
```

1. <span data-ttu-id="01280-228">`LoadImagesDirectory` 内で次のコードを追加し、サブディレクトリからすべてのファイル パスを取得します。</span><span class="sxs-lookup"><span data-stu-id="01280-228">Inside the `LoadImagesDirectory` add the following code to get all of the file paths from the subdirectories:</span></span>

    [!code-csharp [GetFiles](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L102-L103)]

1. <span data-ttu-id="01280-229">次に、`foreach` ステートメントを利用して各ファイルを反復処理します。</span><span class="sxs-lookup"><span data-stu-id="01280-229">Then, iterate through each of the files using a `foreach` statement.</span></span>

    ```csharp
    foreach (var file in files)
    {

    }
    ```

1. <span data-ttu-id="01280-230">`foreach` ステートメント内で、ファイルの拡張子がサポートされていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="01280-230">Inside the `foreach` statement, check that the file extensions are supported.</span></span> <span data-ttu-id="01280-231">Image Classification API では、JPEG 形式と PNG 形式がサポートされています。</span><span class="sxs-lookup"><span data-stu-id="01280-231">The Image Classification API supports JPEG and PNG formats.</span></span>

    [!code-csharp [CheckExtension](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L107-L108)]

1. <span data-ttu-id="01280-232">次に、ファイルのラベルを取得します。</span><span class="sxs-lookup"><span data-stu-id="01280-232">Then, get the label for the file.</span></span> <span data-ttu-id="01280-233">`useFolderNameAsLabel` パラメーターが `true` に設定されている場合、ファイルが保存されている親ディレクトリがラベルとして使用されます。</span><span class="sxs-lookup"><span data-stu-id="01280-233">If the `useFolderNameAsLabel` parameter is set to `true`, then the parent directory where the file is saved is used as the label.</span></span> <span data-ttu-id="01280-234">それ以外の場合、ラベルは、ファイルのプレフィックスか名前自体にする必要があります。</span><span class="sxs-lookup"><span data-stu-id="01280-234">Otherwise, it expects the label to be a prefix of the file name or the file name itself.</span></span>

    [!code-csharp [GetLabel](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L110-L124)]

1. <span data-ttu-id="01280-235">最後に、`ModelInput` の新しいインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="01280-235">Finally, create a new instance of `ModelInput`.</span></span>

    [!code-csharp [CreateImageData](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L126-L130)]

### <a name="prepare-the-data"></a><span data-ttu-id="01280-236">データを準備する</span><span class="sxs-lookup"><span data-stu-id="01280-236">Prepare the data</span></span>

1. <span data-ttu-id="01280-237">`Main` メソッドに戻り、`LoadFromDirectory` ユーティリティを使用し、トレーニングに使用される画像の一覧を取得します。</span><span class="sxs-lookup"><span data-stu-id="01280-237">Back in the `Main` method, use the `LoadFromDirectory` utility method to get the list of images used for training.</span></span>

    [!code-csharp [LoadImages](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L20)]

1. <span data-ttu-id="01280-238">次に、[`LoadFromEnumerable`](xref:Microsoft.ML.DataOperationsCatalog.LoadFromEnumerable*) メソッドを利用して [`IDataView`](xref:Microsoft.ML.IDataView) に画像を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="01280-238">Then, load the images into an [`IDataView`](xref:Microsoft.ML.IDataView) using the [`LoadFromEnumerable`](xref:Microsoft.ML.DataOperationsCatalog.LoadFromEnumerable*) method.</span></span>

    [!code-csharp [CreateIDataView](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L22)]    

1. <span data-ttu-id="01280-239">データはディレクトリから読み取られた順序で読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="01280-239">The data is loaded in the order it was read from the directories.</span></span> <span data-ttu-id="01280-240">データのバランスを維持するために、[`ShuffleRows`](xref:Microsoft.ML.DataOperationsCatalog.ShuffleRows*) メソッドを利用してシャッフルします。</span><span class="sxs-lookup"><span data-stu-id="01280-240">To balance the data, shuffle it using the [`ShuffleRows`](xref:Microsoft.ML.DataOperationsCatalog.ShuffleRows*) method.</span></span>

    [!code-csharp [ShuffleRows](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L24)]

1. <span data-ttu-id="01280-241">機械学習モデルでは、数値形式で入力する必要があります。</span><span class="sxs-lookup"><span data-stu-id="01280-241">Machine learning models expect input to be in numerical format.</span></span> <span data-ttu-id="01280-242">そのため、トレーニングの前にデータでいくつかの処理を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="01280-242">Therefore, some preprocessing needs to be done on the data prior to training.</span></span> <span data-ttu-id="01280-243">[`MapValueToKey`](xref:Microsoft.ML.ConversionsExtensionsCatalog.MapValueToKey*) 変換と [`LoadImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.LoadImages*) 変換から構成される [`EstimatorChain`](xref:Microsoft.ML.Data.EstimatorChain%601) を作成します。</span><span class="sxs-lookup"><span data-stu-id="01280-243">Create an [`EstimatorChain`](xref:Microsoft.ML.Data.EstimatorChain%601) made up of the [`MapValueToKey`](xref:Microsoft.ML.ConversionsExtensionsCatalog.MapValueToKey*) and [`LoadImages`](xref:Microsoft.ML.ImageEstimatorsCatalog.LoadImages*) transforms.</span></span> <span data-ttu-id="01280-244">`MapValueToKey` 変換では、`Label` 列のカテゴリ値を受け取り、それを数値 `KeyType` に変換し、`LabelAsKey` という名前の新しい列に保存します。</span><span class="sxs-lookup"><span data-stu-id="01280-244">The `MapValueToKey` transform takes the categorical value in the `Label` column, converts it to a numerical `KeyType` value and stores it in a new column called `LabelAsKey`.</span></span> <span data-ttu-id="01280-245">`LoadImages` では、`imageFolder` パラメーターと共に `ImagePath` から値を取得し、トレーニングのために画像を読み込みます。</span><span class="sxs-lookup"><span data-stu-id="01280-245">The `LoadImages` takes the values from the `ImagePath` column along with the `imageFolder` parameter to load images for training.</span></span> <span data-ttu-id="01280-246">`useImageType` を `false` に設定すると、画像が `byte[]` に変換されます。</span><span class="sxs-lookup"><span data-stu-id="01280-246">Setting the `useImageType` to `false` converts the images into a `byte[]`.</span></span> 

    [!code-csharp [DefinePreprocessingPipeline](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L26-L33)]

1. <span data-ttu-id="01280-247">データを `preprocessingPipeline` [`EstimatorChain`](xref:Microsoft.ML.Data.EstimatorChain%601) に適用する [`Fit`](xref:Microsoft.ML.Data.EstimatorChain%601.Fit*) メソッドを使用し、事前処理済みのデータが含まれる [`IDataView`](xref:Microsoft.ML.IDataView) メソッドを返す [`Transform`](xref:Microsoft.ML.Data.TransformerChain`1.Transform*) メソッドを続けます。</span><span class="sxs-lookup"><span data-stu-id="01280-247">Use the [`Fit`](xref:Microsoft.ML.Data.EstimatorChain%601.Fit*) method to apply the data to the `preprocessingPipeline` [`EstimatorChain`](xref:Microsoft.ML.Data.EstimatorChain%601) followed by the [`Transform`](xref:Microsoft.ML.Data.TransformerChain`1.Transform*) method, which returns an [`IDataView`](xref:Microsoft.ML.IDataView) containing the pre-processed data.</span></span>

    [!code-csharp [PreprocessData](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L35-L37)]

1. <span data-ttu-id="01280-248">モデルをトレーニングするには、トレーニング データセットと検証データセットを用意することが重要です。</span><span class="sxs-lookup"><span data-stu-id="01280-248">To train a model, it's important to have a training dataset as well as a validation dataset.</span></span> <span data-ttu-id="01280-249">モデルはトレーニング セットでトレーニングされます。</span><span class="sxs-lookup"><span data-stu-id="01280-249">The model is trained on the training set.</span></span> <span data-ttu-id="01280-250">初見のデータの予測精度は、検証セットに対するパフォーマンスで計測されます。</span><span class="sxs-lookup"><span data-stu-id="01280-250">How well it makes predictions on unseen data is measured by the performance against the validation set.</span></span> <span data-ttu-id="01280-251">そのパフォーマンスの結果に基づき、改善努力の中でモデルが学習したものに調整が加えられます。</span><span class="sxs-lookup"><span data-stu-id="01280-251">Based on the results of that performance, the model makes adjustments to what it has learned in an effort to improve.</span></span> <span data-ttu-id="01280-252">検証セットは、元のデータセットを分割することで取得するか、この目的のために既に用意されていた別の情報源から取得します。</span><span class="sxs-lookup"><span data-stu-id="01280-252">The validation set can come from either splitting your original dataset or from another source that has already been set aside for this purpose.</span></span> <span data-ttu-id="01280-253">今回、事前処理済みのデータセットがトレーニング セット、検証セット、テスト セットに分割されています。</span><span class="sxs-lookup"><span data-stu-id="01280-253">In this case, the pre-processed dataset is split into training, validation and test sets.</span></span>

    [!code-csharp [CreateDataSplits](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L39-L40)]

    <span data-ttu-id="01280-254">上のコード サンプルでは、2 回分割されます。</span><span class="sxs-lookup"><span data-stu-id="01280-254">The code sample above performs two splits.</span></span> <span data-ttu-id="01280-255">まず、事前処理済みのデータが分割され、70% がトレーニングに使用され、残りの 30% が検証に使用されます。</span><span class="sxs-lookup"><span data-stu-id="01280-255">First, the pre-processed data is split and 70% is used for training while the remaining 30% is used for validation.</span></span> <span data-ttu-id="01280-256">次に、30% の検証セットがさらに検証セットとテスト セットに分割され、90% が検証に使用され、10% がテストに使用されます。</span><span class="sxs-lookup"><span data-stu-id="01280-256">Then, the 30% validation set is further split into validation and test sets where 90% is used for validation and 10% is used for testing.</span></span> 

    <span data-ttu-id="01280-257">このようなデータ分割の目的は試験を受けるようなものです。</span><span class="sxs-lookup"><span data-stu-id="01280-257">A way to think about the purpose of these data partitions is taking an exam.</span></span> <span data-ttu-id="01280-258">試験勉強するとき、ノート、教科書、参考書で復習し、試験に出る概念をつかみます。</span><span class="sxs-lookup"><span data-stu-id="01280-258">When studying for an exam, you review your notes, books, or other resources to get a grasp on the concepts that are on the exam.</span></span> <span data-ttu-id="01280-259">トレーニング セットがこれに相当します。</span><span class="sxs-lookup"><span data-stu-id="01280-259">This is what the train set is for.</span></span> <span data-ttu-id="01280-260">次に、模擬試験を受け、自分の知識を確認します。</span><span class="sxs-lookup"><span data-stu-id="01280-260">Then, you might take a mock exam to validate your knowledge.</span></span> <span data-ttu-id="01280-261">ここで役立つのが検証セットです。</span><span class="sxs-lookup"><span data-stu-id="01280-261">This is where the validation set comes in handy.</span></span> <span data-ttu-id="01280-262">実際に試験を受ける前に概念をしっかり理解しているかを確認したくなることでしょう。</span><span class="sxs-lookup"><span data-stu-id="01280-262">You want to check whether you have a good grasp of the concepts before taking the actual exam.</span></span> <span data-ttu-id="01280-263">模擬試験の結果に基づき、間違った箇所や良く理解していなかった箇所をメモし、本番の試験に向けて復習する中、変えるべきところを組み込みます。</span><span class="sxs-lookup"><span data-stu-id="01280-263">Based on those results, you take note of what you got wrong or didn't understand well and incorporate your changes as you review for the real exam.</span></span> <span data-ttu-id="01280-264">最後に、試験を受けます。</span><span class="sxs-lookup"><span data-stu-id="01280-264">Finally, you take the exam.</span></span> <span data-ttu-id="01280-265">テスト セットがこれに相当します。</span><span class="sxs-lookup"><span data-stu-id="01280-265">This is what the test set is used for.</span></span> <span data-ttu-id="01280-266">試験に出ている問題は今まで見たことがありません。トレーニングと検証から学習したことを活用し、手元の課題に自分の知識を応用します。</span><span class="sxs-lookup"><span data-stu-id="01280-266">You've never seen the questions that are on the exam and now use what you learned from training and validation to apply your knowledge to the task at hand.</span></span> 

1. <span data-ttu-id="01280-267">トレーニング データ、検証データ、テスト データそれぞれの値をパーティションに割り当てます。</span><span class="sxs-lookup"><span data-stu-id="01280-267">Assign the partitions their respective values for the train, validation and test data.</span></span>

    [!code-csharp [CreateDatasets](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L42-L44)]

## <a name="define-the-training-pipeline"></a><span data-ttu-id="01280-268">トレーニング パイプラインを定義する</span><span class="sxs-lookup"><span data-stu-id="01280-268">Define the training pipeline</span></span>

<span data-ttu-id="01280-269">モデル トレーニングはいくつかのステップから構成されます。</span><span class="sxs-lookup"><span data-stu-id="01280-269">Model training consists of a couple of steps.</span></span> <span data-ttu-id="01280-270">まず、Image Classification API を利用し、モデルがトレーニングされます。</span><span class="sxs-lookup"><span data-stu-id="01280-270">First, Image Classification API is used to train the model.</span></span> <span data-ttu-id="01280-271">次に、`PredictedLabel` 列のエンコード済みラベルが `MapKeyToValue` 変換により元のカテゴリ値に戻されます。</span><span class="sxs-lookup"><span data-stu-id="01280-271">Then, the encoded labels in the `PredictedLabel` column are converted back to their original categorical value using the `MapKeyToValue` transform.</span></span> 

1. <span data-ttu-id="01280-272">`mapLabelEstimator` 変換と `ImageClassification` 変換の両方から構成されるトレーニング [`EstimatorChain`](xref:Microsoft.ML.Data.EstimatorChain%601) パイプラインを定義します。</span><span class="sxs-lookup"><span data-stu-id="01280-272">Define the training [`EstimatorChain`](xref:Microsoft.ML.Data.EstimatorChain%601) pipeline that consists of both the `mapLabelEstimator` and the `ImageClassification` transforms.</span></span>

    [!code-csharp [DefineTrainingPipeline](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L46-L58)]    

    <span data-ttu-id="01280-273">`ImageClassification` 見積もりツールはいくつかのパラメーターを受け取ります。</span><span class="sxs-lookup"><span data-stu-id="01280-273">The `ImageClassification` estimator takes in several parameters:</span></span>

    - <span data-ttu-id="01280-274">`featuresColumnName` はモデルの入力として使用される列です。</span><span class="sxs-lookup"><span data-stu-id="01280-274">`featuresColumnName` is the column that is used as input for the model.</span></span>
    - <span data-ttu-id="01280-275">`labelColumnName` は予測する値の列です。</span><span class="sxs-lookup"><span data-stu-id="01280-275">`labelColumnName` is the column for the value to predict.</span></span>
    - <span data-ttu-id="01280-276">`arch` では、使用する事前トレーニング済みモデル アーキテクチャが定義されます。</span><span class="sxs-lookup"><span data-stu-id="01280-276">`arch` defines which of the pretrained model architectures to use.</span></span> <span data-ttu-id="01280-277">このチュートリアルでは、ResNetv2 モデルの 101 レイヤー型が使用されます。</span><span class="sxs-lookup"><span data-stu-id="01280-277">This tutorial uses the 101-layer variant of the ResNetv2 model.</span></span>
    - <span data-ttu-id="01280-278">`epoch` では、トレーニング プロセスを通してデータセット全体に行われる繰り返しの最大数が指定されます。</span><span class="sxs-lookup"><span data-stu-id="01280-278">`epoch` specifies the maximum number of iterations over the entire dataset throughout the training process.</span></span> <span data-ttu-id="01280-279">この数値が大きければ大きいほど、モデルがトレーニングされる時間が長くなり、より優れたモデルが生成される可能性が高くなります。</span><span class="sxs-lookup"><span data-stu-id="01280-279">The higher the number, the longer the model trains for and potentially the better model that is produced.</span></span>
    - <span data-ttu-id="01280-280">`batchSize` は、トレーニング時に使用するサンプルの数です。</span><span class="sxs-lookup"><span data-stu-id="01280-280">`batchSize` is the number of samples to use at a time for training.</span></span> <span data-ttu-id="01280-281">1 エポック中、batchSize に等しい複数のバッチがモデルのトレーニングと更新に使用されます。</span><span class="sxs-lookup"><span data-stu-id="01280-281">During one epoch, multiple batches equal to the batchSize are used to train and update the model.</span></span> <span data-ttu-id="01280-282">この数値が低ければ低いほど、各バッチの処理時に必要になるメモリがそれだけ少なくなります。</span><span class="sxs-lookup"><span data-stu-id="01280-282">The lower the number, the less memory required when each batch is processed.</span></span>
    - <span data-ttu-id="01280-283">`testOnTrainSet` からは、検証セットがないとき、トレーニング セットに対してパフォーマンスを検証するようにモデルに指示が出ます。</span><span class="sxs-lookup"><span data-stu-id="01280-283">`testOnTrainSet` tells the model to measure performance against the training set when no validation set is present.</span></span>
    - <span data-ttu-id="01280-284">`metricsCallback` では、トレーニング中に進捗状況を追跡記録する関数がバインドされます。</span><span class="sxs-lookup"><span data-stu-id="01280-284">`metricsCallback` binds a function to track the progress during training.</span></span>
    - <span data-ttu-id="01280-285">`validationSet` は検証データが含まれる [`IDataView`](xref:Microsoft.ML.IDataView) です。</span><span class="sxs-lookup"><span data-stu-id="01280-285">`validationSet` is the [`IDataView`](xref:Microsoft.ML.IDataView) containing the validation data.</span></span>
    - <span data-ttu-id="01280-286">`reuseTrainSetBottleneckCachedValues` からは、後続の実行でボトルネック フェーズからキャッシュされた値を使用するかどうかがモデルに伝えられます。</span><span class="sxs-lookup"><span data-stu-id="01280-286">`reuseTrainSetBottleneckCachedValues` tells the model whether to use the cached values from the bottleneck phase in subsequent runs.</span></span> <span data-ttu-id="01280-287">ボトルネック フェーズは、初回実行時の計算処理が激しいワンタイム パススルー計算です。</span><span class="sxs-lookup"><span data-stu-id="01280-287">The bottleneck phase is a one-time pass-through computation that is computationally intensive the first time it is performed.</span></span> <span data-ttu-id="01280-288">トレーニング データが変わらないとき、エポック数やバッチ サイズを変えて実験する場合、キャッシュした値を利用すると、モデルのトレーニングに必要な時間が大幅に短縮されます。</span><span class="sxs-lookup"><span data-stu-id="01280-288">If the training data does not change and you want to experiment using a different number of epochs or batch size, using the cached values significantly reduces the amount of time required to train a model.</span></span>
    - <span data-ttu-id="01280-289">`reuseValidationSetBottleneckCachedValues` は `reuseTrainSetBottleneckCachedValues` に似ていますが、これは検証データセット用になります。</span><span class="sxs-lookup"><span data-stu-id="01280-289">`reuseValidationSetBottleneckCachedValues` is similar to `reuseTrainSetBottleneckCachedValues` only that in this case it's for the validation dataset.</span></span>
    - <span data-ttu-id="01280-290">`disableEarlyStopping` からは、早期停止方針を採用するかどうかが ImageClassification 見積もりツールに伝えられます。</span><span class="sxs-lookup"><span data-stu-id="01280-290">`disableEarlyStopping` tells the ImageClassification estimator whether to employ an early stopping strategy.</span></span> <span data-ttu-id="01280-291">モデルではトレーニング中に精確な予測を支援する最適な値が検索されるため、パフォーマンスが上がったり下がったりすることがあります。</span><span class="sxs-lookup"><span data-stu-id="01280-291">As the model searches for the optimal values that will help it make accurate predictions during training, performance may increase or decrease.</span></span> <span data-ttu-id="01280-292">最後にモデルが最後のエポックに到達したとき、トレーニングから学習したパターンが最適以下となることがあります。</span><span class="sxs-lookup"><span data-stu-id="01280-292">Ultimately, if the model reaches the last epoch, it may be the case that the patterns it learned from training are suboptimal.</span></span> <span data-ttu-id="01280-293">早期停止では、トレーニングにおけるパフォーマンスの低下を監視し、最適なモデルを維持する目的でトレーニング プロセスを停止します。</span><span class="sxs-lookup"><span data-stu-id="01280-293">Early stopping monitors training for these drops in performance and stops the training process in an effort to preserve an optimal version of the model.</span></span>

1. <span data-ttu-id="01280-294">[`Fit`](xref:Microsoft.ML.Data.EstimatorChain%601.Fit*) メソッドを利用してモデルをトレーニングします。</span><span class="sxs-lookup"><span data-stu-id="01280-294">Use the [`Fit`](xref:Microsoft.ML.Data.EstimatorChain%601.Fit*) method to train your model.</span></span>

    [!code-csharp [TrainModel](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L60)]

## <a name="use-the-model"></a><span data-ttu-id="01280-295">モデルを使用する</span><span class="sxs-lookup"><span data-stu-id="01280-295">Use the model</span></span>

<span data-ttu-id="01280-296">モデルをトレーニングできたところで、モデルを利用して画像を分類しましょう。</span><span class="sxs-lookup"><span data-stu-id="01280-296">Now that you have trained your model, it's time to use it to classify images.</span></span>

<span data-ttu-id="01280-297">`Main` メソッドの下で、コンソールに予測情報を表示する、`OutputPrediction` という名前の新しいユーティリティ メソッドを作成します。</span><span class="sxs-lookup"><span data-stu-id="01280-297">Below the `Main` method, create a new utility method called `OutputPrediction` to display prediction information in the console.</span></span>

[!code-csharp [OuputPredictionMethod](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L94-L98)]

### <a name="classify-a-single-image"></a><span data-ttu-id="01280-298">1 枚の画像を分類する</span><span class="sxs-lookup"><span data-stu-id="01280-298">Classify a single image</span></span>

1. <span data-ttu-id="01280-299">`Main` メソッドの下に `ClassifySingleImage` という名前の新しいメソッドを追加します。このメソッドは画像を 1 回予測し、出力します。</span><span class="sxs-lookup"><span data-stu-id="01280-299">Add a new method called `ClassifySingleImage` below the `Main` method to make and output a single image prediction.</span></span>

    ```csharp
    public static void ClassifySingleImage(MLContext mlContext, IDataView data, ITransformer trainedModel)
    {

    }
    ```

1. <span data-ttu-id="01280-300">`ClassifySingleImage` メソッド内に [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) を作成します。</span><span class="sxs-lookup"><span data-stu-id="01280-300">Create a [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) inside the `ClassifySingleImage` method.</span></span> <span data-ttu-id="01280-301">[`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) は、データの 1 つのインスタンスを渡してから、その予測を実行できる便利な API です。</span><span class="sxs-lookup"><span data-stu-id="01280-301">The [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) is a convenience API, which allows you to pass in and then perform a prediction on a single instance of data.</span></span>

    [!code-csharp [CreatePredictionEngine](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L71)]    

1. <span data-ttu-id="01280-302">1 つの `ModelInput` インスタンスにアクセスするには、[`CreateEnumerable`](xref:Microsoft.ML.DataOperationsCatalog.CreateEnumerable*) メソッドを利用して `data` [`IDataView`](xref:Microsoft.ML.IDataView) を [`IEnumerable`](xref:System.Collections.Generic.IEnumerable%601) に変換し、最初の観察を取得します。</span><span class="sxs-lookup"><span data-stu-id="01280-302">To access a single `ModelInput` instance, convert the `data` [`IDataView`](xref:Microsoft.ML.IDataView) into an [`IEnumerable`](xref:System.Collections.Generic.IEnumerable%601) using the [`CreateEnumerable`](xref:Microsoft.ML.DataOperationsCatalog.CreateEnumerable*) method and then get the first observation.</span></span> 

    [!code-csharp [GetTestInputData](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L73)]

1. <span data-ttu-id="01280-303">[`Predict`](xref:Microsoft.ML.PredictionEngine%602.Predict*) メソッドを使用し、画像を分類します。</span><span class="sxs-lookup"><span data-stu-id="01280-303">Use the [`Predict`](xref:Microsoft.ML.PredictionEngine%602.Predict*) method to classify the image.</span></span>

    [!code-csharp [MakeSinglePrediction](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L75)]

1. <span data-ttu-id="01280-304">`OutputPrediction` メソッドでコンソールに予測を出力します。</span><span class="sxs-lookup"><span data-stu-id="01280-304">Output the prediction to the console with the `OutputPrediction` method.</span></span>

    [!code-csharp [OuputSinglePrediction](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L77-L78)]

1. <span data-ttu-id="01280-305">`Main` メソッド内で、画像のテスト セットを利用して `ClassifySingleImage` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="01280-305">Inside the `Main` method, call `ClassifySingleImage` using the test set of images.</span></span>

    [!code-csharp [ClassifySingleImage](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L62)]

### <a name="classify-multiple-images"></a><span data-ttu-id="01280-306">複数の画像を分類する</span><span class="sxs-lookup"><span data-stu-id="01280-306">Classify multiple images</span></span>

1. <span data-ttu-id="01280-307">`ClassifySingleImage` メソッドの下に `ClassifyImages` という名前の新しいメソッドを追加します。このメソッドは画像を複数回予測し、出力します。</span><span class="sxs-lookup"><span data-stu-id="01280-307">Add a new method called `ClassifyImages` below the `ClassifySingleImage` method to make and output multiple image predictions.</span></span>

    ```csharp
    public static void ClassifyImages(MLContext mlContext, IDataView data, ITransformer trainedModel)
    {

    }
    ```

1. <span data-ttu-id="01280-308">[`Transform`](xref:Microsoft.ML.ITransformer.Transform*) メソッドを利用し、予測を含む [`IDataView`](xref:Microsoft.ML.IDataView) を作成します。</span><span class="sxs-lookup"><span data-stu-id="01280-308">Create an [`IDataView`](xref:Microsoft.ML.IDataView) containing the predictions by using the [`Transform`](xref:Microsoft.ML.ITransformer.Transform*) method.</span></span> <span data-ttu-id="01280-309">`ClassifyImages` メソッド内に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="01280-309">Add the following code inside the `ClassifyImages` method.</span></span>

    [!code-csharp [MakeMultiplePredictions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L83)]

1. <span data-ttu-id="01280-310">予測を反復処理するには、[`CreateEnumerable`](xref:Microsoft.ML.DataOperationsCatalog.CreateEnumerable*) メソッドを利用して `predictionData` [`IDataView`](xref:Microsoft.ML.IDataView) を [`IEnumerable`](xref:System.Collections.Generic.IEnumerable%601) に変換し、最初の 10 件の観察を取得します。</span><span class="sxs-lookup"><span data-stu-id="01280-310">In order to iterate over the predictions, convert the `predictionData` [`IDataView`](xref:Microsoft.ML.IDataView) into an [`IEnumerable`](xref:System.Collections.Generic.IEnumerable%601) using the [`CreateEnumerable`](xref:Microsoft.ML.DataOperationsCatalog.CreateEnumerable*) method and then get the first 10 observations.</span></span>

    [!code-csharp [IEnumerablePredictions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L85)]

1. <span data-ttu-id="01280-311">予測を反復処理し、元のラベルと予測後のラベルを出力します。</span><span class="sxs-lookup"><span data-stu-id="01280-311">Iterate and output the original and predicted labels for the predictions.</span></span>

    [!code-csharp [OutputMultiplePredictions](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L87-L91)]

1. <span data-ttu-id="01280-312">最後に、`Main` メソッド内で、画像のテスト セットを利用して `ClassifyImages` を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="01280-312">Finally, inside the `Main` method, call `ClassifyImages` using the test set of images.</span></span>

    [!code-csharp [ClassifyImages](~/machinelearning-samples/samples/csharp/getting-started/DeepLearning_ImageClassification_Binary/DeepLearning_ImageClassification_Binary/Program.cs#L64)]

## <a name="run-the-application"></a><span data-ttu-id="01280-313">アプリケーションの実行</span><span class="sxs-lookup"><span data-stu-id="01280-313">Run the application</span></span>

<span data-ttu-id="01280-314">コンソール アプリを実行します。</span><span class="sxs-lookup"><span data-stu-id="01280-314">Run your console app.</span></span> <span data-ttu-id="01280-315">出力は下の出力のようになるはずです。</span><span class="sxs-lookup"><span data-stu-id="01280-315">The output should be similar to that below.</span></span> <span data-ttu-id="01280-316">警告メッセージまたは処理中のメッセージが表示される場合がありますが、わかりやすくするため、これらのメッセージは結果から削除してあります。</span><span class="sxs-lookup"><span data-stu-id="01280-316">You may see warnings or processing messages, but these messages have been removed from the following results for clarity.</span></span> <span data-ttu-id="01280-317">簡潔にするため、出力は要約されています。</span><span class="sxs-lookup"><span data-stu-id="01280-317">For brevity, the output has been condensed.</span></span>

<span data-ttu-id="01280-318">**ボトルネック フェーズ**</span><span class="sxs-lookup"><span data-stu-id="01280-318">**Bottleneck phase**</span></span>

<span data-ttu-id="01280-319">画像は `byte[]` として読み込まれており、表示する画像名がないため、画像名には値が出力されません。</span><span class="sxs-lookup"><span data-stu-id="01280-319">No value is printed for the image name because the images are loaded as a `byte[]` therefore there is no image name to display.</span></span>

```test
Phase: Bottleneck Computation, Dataset used:      Train, Image Index: 279, Image Name:
Phase: Bottleneck Computation, Dataset used:      Train, Image Index: 280, Image Name:
Phase: Bottleneck Computation, Dataset used: Validation, Image Index:   1, Image Name:
Phase: Bottleneck Computation, Dataset used: Validation, Image Index:   2, Image Name:
```

<span data-ttu-id="01280-320">**トレーニング フェーズ**</span><span class="sxs-lookup"><span data-stu-id="01280-320">**Training phase**</span></span>

```text
Phase: Training, Dataset used: Validation, Batch Processed Count:   6, Epoch:  21, Accuracy:  0.6797619
Phase: Training, Dataset used: Validation, Batch Processed Count:   6, Epoch:  22, Accuracy:  0.7642857
Phase: Training, Dataset used: Validation, Batch Processed Count:   6, Epoch:  23, Accuracy:  0.7916667
```

<span data-ttu-id="01280-321">**画像分類の出力**</span><span class="sxs-lookup"><span data-stu-id="01280-321">**Classify images output**</span></span>

```text
Classifying single image
Image: 7001-220.jpg | Actual Value: UD | Predicted Value: UD

Classifying multiple images
Image: 7001-220.jpg | Actual Value: UD | Predicted Value: UD
Image: 7001-163.jpg | Actual Value: UD | Predicted Value: UD
Image: 7001-210.jpg | Actual Value: UD | Predicted Value: UD
```

<span data-ttu-id="01280-322">*7001-220.jpg* 画像の検査後、実際のところ、ひび割れがないことがわかりました。</span><span class="sxs-lookup"><span data-stu-id="01280-322">Upon inspection of the *7001-220.jpg* image, you can see that it in fact is not cracked.</span></span> 

![予測に使用された SDNET2018 データセット画像](./media/image-classification-api-transfer-learning/predictedimage.jpg)

<span data-ttu-id="01280-324">おめでとうございます!</span><span class="sxs-lookup"><span data-stu-id="01280-324">Congratulations!</span></span> <span data-ttu-id="01280-325">これで画像を分類するディープ ラーニング モデルが正しく作成されました。</span><span class="sxs-lookup"><span data-stu-id="01280-325">You've now successfully built a deep learning model for classifying images.</span></span>

### <a name="improve-the-model"></a><span data-ttu-id="01280-326">画像を改善する</span><span class="sxs-lookup"><span data-stu-id="01280-326">Improve the model</span></span>

<span data-ttu-id="01280-327">モデルの結果に不満が残る場合、次の手法を試し、パフォーマンスを向上してみることができます。</span><span class="sxs-lookup"><span data-stu-id="01280-327">If you're not satisfied with the results of your model, you can try to improve its performance by trying some of the following approaches:</span></span>

- <span data-ttu-id="01280-328">**データを増やす**:モデルが学習するサンプルの数が多ければ多いほど、パフォーマンスがそれだけ上がります。</span><span class="sxs-lookup"><span data-stu-id="01280-328">**More Data**: The more examples a model learns from, the better it performs.</span></span> <span data-ttu-id="01280-329">[SDNET2018 データセット](https://digitalcommons.usu.edu/cgi/viewcontent.cgi?filename=2&article=1047&context=all_datasets&type=additional)を全部ダウンロードし、それを利用してトレーニングします。</span><span class="sxs-lookup"><span data-stu-id="01280-329">Download the full [SDNET2018 dataset](https://digitalcommons.usu.edu/cgi/viewcontent.cgi?filename=2&article=1047&context=all_datasets&type=additional) and use it to train.</span></span> 
- <span data-ttu-id="01280-330">**データを拡張する**:データに変化を与える一般的な手法は、画像にさまざまな変換 (回転、裏返し、移動、トリミング) を適用してデータを強化することです。</span><span class="sxs-lookup"><span data-stu-id="01280-330">**Augment the data**: A common technique to add variety to the data is to augment the data by taking an image and applying different transforms (rotate, flip, shift, crop).</span></span> <span data-ttu-id="01280-331">これでモデルが学習するサンプルに変化が与えられます。</span><span class="sxs-lookup"><span data-stu-id="01280-331">This adds more varied examples for the model to learn from.</span></span> 
- <span data-ttu-id="01280-332">**トレーニング時間を増やす**:トレーニング時間が長ければ長いほど、モデルがそれだけ微調整されます。</span><span class="sxs-lookup"><span data-stu-id="01280-332">**Train for a longer time**: The longer you train, the more tuned the model will be.</span></span> <span data-ttu-id="01280-333">エポックの数を増やすことで、モデルのパフォーマンスが上がることもあります。</span><span class="sxs-lookup"><span data-stu-id="01280-333">Increasing the number of epochs may improve the performance of your model.</span></span>
- <span data-ttu-id="01280-334">**パイパーパラメーターで実験する**:このチュートリアルで使用されているパラメーターに加え、他のパラメーターを微調整するとパフォーマンスが上がることがあります。</span><span class="sxs-lookup"><span data-stu-id="01280-334">**Experiment with the hyper-parameters**: In addition to the parameters used in this tutorial, other parameters can be tuned to potentially improve performance.</span></span> <span data-ttu-id="01280-335">各エポック後のモデル更新の規模を決定する学習率を変更すると、パフォーマンスが上がることがあります。</span><span class="sxs-lookup"><span data-stu-id="01280-335">Changing the learning rate, which determines the magnitude of updates made to the model after each epoch may improve performance.</span></span>
- <span data-ttu-id="01280-336">**別のモデル アーキテクチャを使用する**:目で見たときのデータによっては、その特徴を最も効果的に学習できるモデルが異なることがあります。</span><span class="sxs-lookup"><span data-stu-id="01280-336">**Use a different model architecture**: Depending on what your data looks like, the model that can best learn its features may differ.</span></span> <span data-ttu-id="01280-337">モデルのパフォーマンスに不満が残る場合、アーキテクチャの変更をお試しください。</span><span class="sxs-lookup"><span data-stu-id="01280-337">If you're not satisfied with the performance of your model, try changing the architecture.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="01280-338">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="01280-338">Additional Resources</span></span>

- <span data-ttu-id="01280-339">[ディープ ラーニングと機械学習](/azure/machine-learning/service/concept-deep-learning-vs-machine-learning)</span><span class="sxs-lookup"><span data-stu-id="01280-339">[Deep Learning vs Machine Learning](/azure/machine-learning/service/concept-deep-learning-vs-machine-learning).</span></span>

## <a name="next-steps"></a><span data-ttu-id="01280-340">次の手順</span><span class="sxs-lookup"><span data-stu-id="01280-340">Next steps</span></span>

<span data-ttu-id="01280-341">このチュートリアルでは、転移学習、事前トレーニング済みの画像分類 TensorFlow モデル、ML.NET Image Classification API を利用してカスタム ディープ ラーニング モデルを構築し、コンクリートの表面の画像をひび割れあり/ひび割れなしに分類する方法について学習しました。</span><span class="sxs-lookup"><span data-stu-id="01280-341">In this tutorial, you learned how to build a custom deep learning model using transfer learning, a pretrained image classification TensorFlow model and the ML.NET Image Classification API to classify images of concrete surfaces as cracked or uncracked.</span></span>

<span data-ttu-id="01280-342">さらに詳しく学習するには、次のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="01280-342">Advance to the next tutorial to learn more.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="01280-343">物体検出</span><span class="sxs-lookup"><span data-stu-id="01280-343">Object Detection</span></span>](object-detection-onnx.md)