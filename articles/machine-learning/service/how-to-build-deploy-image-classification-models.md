---
title: Azure Machine Learning Package for Computer Vision을 사용하여 이미지 분류 모델을 작성하고 배포합니다.
description: Azure Machine Learning Package for Computer Vision을 사용하여 컴퓨터 비전 이미지 분류 모델을 작성, 학습, 테스트 및 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: bd9f01e76c68fa41616818251b5b54553059cbcc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="build-and-deploy-image-classification-models-with-azure-machine-learning"></a>Azure Machine Learning으로 이미지 분류 모델 작성 및 배포

이 문서에서는 AMLPCV(**Azure Machine Learning Package for Computer Vision**)를 사용하여 이미지 분류 모델을 학습, 테스트 및 배포하는 방법을 알아봅니다. 

컴퓨터 비전 도메인의 많은 문제는 이미지 분류를 사용하여 해결할 수 있습니다. 이러한 문제에는 다음과 같은 질문에 답하는 모델 구축이 포함됩니다.
+ _이미지에 개체가 있습니까? 예: "dog", "car", "ship" 등등_
+ _이 환자의 망막 스캔을 통해 안과 질환에 대해 어떤 종류의 심각도가 표시됩니까?_

AMLPCV를 사용하여 이 모델을 구축하고 배포하는 경우 다음 단계를 수행하십시오.
1. 데이터 집합 만들기
2. 이미지 시각화 및 주석
3. 이미지 증강
4. DNN(심층 신경망) 모델 정의
5. 분류자 학습
6. 평가 및 시각화
7. 웹 서비스 배포
8. 웹 서비스 부하 테스트

[CNTK](https://www.microsoft.com/cognitive-toolkit/)는 딥 러닝 프레임워크로 사용되며 학습은 ([딥 러닝 데이터 과학 VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning?tab=Overview))과 같은 GPU 기반 컴퓨터에서 로컬로 수행되고 배포에는 Azure ML Operationalization CLI가 사용됩니다.

각 모듈 및 클래스에 대한 자세한 내용은 [패키지 참조 설명서](https://aka.ms/aml-packages/vision)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

1. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

1. 다음 계정 및 응용 프로그램을 설정하고 설치해야 합니다.
   - Azure Machine Learning 실험 계정 
   - Azure Machine Learning 모델 관리 계정
   - Azure Machine Learning Workbench 설치

   이 세 가지를 아직 만들거나 설치하지 않은 경우 [Azure Machine Learning 빠른 시작 및 Workbench 설치](../service/quickstart-installation.md) 문서를 참조하세요. 

1. Azure Machine Learning Package for Computer Vision을 설치해야 합니다. [패키지를 설치하는 방법은 여기](https://aka.ms/aml-packages/vision)를 참조하세요.

## <a name="sample-data-and-notebook"></a>샘플 데이터 및 노트북

### <a name="get-the-jupyter-notebook"></a>Jupyter Notebook 가져오기

여기에 설명된 샘플을 직접 실행하려면 노트북을 다운로드하세요.

> [!div class="nextstepaction"]
> [Jupyter Notebook 가져오기](https://aka.ms/aml-packages/vision/notebooks/image_classification)

### <a name="load-the-sample-data"></a>샘플 데이터 로드

다음 예제에서는 63개의 식기류 이미지로 구성된 데이터 집합을 사용합니다. 각 이미지에는 4 개의 다른 클래스(bowl, cup, cutlery, plate) 중 하나에 속한 것으로 레이블이 지정됩니다. 이 예제에서는 샘플을 신속하게 실행할 수 있도록 이미지 수가 작습니다. 실제로는 클래스당 100개 이상의 이미지가 제공되어야 합니다. 모든 이미지는 "bowl", "cup", "cutlery", "plate"라는 하위 디렉터리의 *"../sample_data/imgs_recycling/"* 에 있습니다.

![Azure Machine Learning 데이터 집합](media/how-to-build-deploy-image-classification-models/recycling_examples.jpg)

## <a name="storage-context"></a>저장소 컨텍스트

저장소 컨텍스트는 증강 이미지 또는 DNN 모델 파일과 같은 다양한 출력 파일을 저장할 위치를 결정하는 데 사용됩니다. 저장소 컨텍스트에 대한 자세한 내용은 [저장소 컨텍스트 설명서](https://review.docs.microsoft.com/en-us/python/api/cvtk.core.context.storagecontext?view=azure-python&branch=smoke-test)를 참조하세요. 

일반적으로 저장소 컨텍스트는 명시적으로 설정할 필요가 없습니다. 단, Azure Machine Learning Workbench의 프로젝트 크기 제한인 25MB를 피하려면 Azure Machine Learning Package for Computer Vision의 출력 디렉터리를 Azure Machine Learning 프로젝트 외부("../../../../cvtk_output")로 설정하는 것이 좋습니다. "cvtk_output" 디렉터리가 더 이상 필요하지 않게 되면 제거해야 합니다.


```python
import warnings
warnings.filterwarnings("ignore")
import json, numpy as np, os, timeit 
from azureml.logging import get_azureml_logger
from imgaug import augmenters
from IPython.display import display
from sklearn import svm
from cvtk import ClassificationDataset, CNTKTLModel, Context, Splitter, StorageContext
from cvtk.augmentation import augment_dataset
from cvtk.core.classifier import ScikitClassifier
from cvtk.evaluation import ClassificationEvaluation, graph_roc_curve, graph_pr_curve, graph_confusion_matrix, basic_plot
import matplotlib.pyplot as plt
%matplotlib inline

# Disable printing of logging messages
from azuremltkbase.logging import ToolkitLogger
ToolkitLogger.getInstance().setEnabled(False)

# Set storage context.
out_root_path = "../../../cvtk_output"
Context.create(outputs_path=out_root_path, persistent_path=out_root_path, temp_path=out_root_path)
```




    {
        "storage": {
            "outputs_path": "../../../cvtk_output",
            "persistent_path": "../../../cvtk_output",
            "temp_path": "../../../cvtk_output"
        }
    }



## <a name="create-a-dataset"></a>데이터 집합 만들기

종속성을 가져오고 저장소 컨텍스트를 설정한 후에는 데이터 집합 개체를 만들 수 있습니다.

Azure Machine Learning Package for Computer Vision을 사용하여 이 개체를 만들려면 로컬 디스크에 이미지의 루트 디렉터리를 제공합니다. 디렉터리는 식기류 데이터 집합과 동일한 일반 구조를 따라야 합니다. 즉, 실제 이미지가 있는 하위 디렉터리가 있어야 합니다.
- root
    - 레이블 1
    - 레이블 2
    - ...
    - 레이블 n
  
다양한 데이터 집합에 대해 이미지 분류 모델을 학습시키는 것은 아래 코드의 루트 경로인 `dataset_location`을 다른 이미지를 가리키도록 변경하는 것만큼 쉽습니다.


```python
# Root image directory 
dataset_location = os.path.abspath(os.path.join(os.getcwd(), "../sample_data/imgs_recycling"))

dataset_name = 'recycling'
dataset = ClassificationDataset.create_from_dir(dataset_name, dataset_location)
print("Dataset consists of {} images with {} labels.".format(len(dataset.images), len(dataset.labels)))
print("Select information for image 2: name={}, label={}, unique id={}.".format(
    dataset.images[2].name, dataset.images[2]._labels[0].name, dataset.images[2]._storage_id))
```

    F1 2018-04-23 17:12:57,593 INFO azureml.vision:machine info {"is_dsvm": true, "os_type": "Windows"} 
    F1 2018-04-23 17:12:57,599 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Dataset consists of 63 images with 4 labels.
    Select information for image 2: name=msft-plastic-bowl20170725152154282.jpg, label=bowl, unique id=3.

데이터 집합 개체는 [Bing Image Search API](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)를 사용하여 이미지를 다운로드하는 기능을 제공합니다. 

두 가지 유형의 검색 쿼리가 지원됩니다. 
+ 일반 텍스트 쿼리
+ 이미지 URL 쿼리

이러한 쿼리는 클래스 레이블과 함께 JSON으로 인코딩된 텍스트 파일 내에 제공되어야 합니다. 예: 

```json
{
    "bowl": [
                    "plastic bowl",
                    "../imgs_recycling/bowl"
    ],
    "cup": [
                    "plastic cup",
                    "../imgs_recycling/cup",
                    "http://cdnimg2.webstaurantstore.com/images/products/main/123662/268841/dart-solo-ultra-clear-conex-tp12-12-oz-pet-plastic-cold-cup-1000-case.jpg"
    ],
    "cutlery": [
                    "plastic cutlery",
                    "../imgs_recycling/cutlery",
                    "http://img4.foodservicewarehouse.com/Prd/1900SQ/Fineline_2514-BO.jpg"
    ],
    "plate": [
                    "plastic plate",
                    "../imgs_recycling/plate"
    ]
}
```

또한 Bing Image Search API 키가 포함되도록 컨텍스트 개체를 명시적으로 만들어야 합니다. 이를 위해서는 Bing Image Search API를 구독해야 합니다.

## <a name="visualize-and-annotate-images"></a>이미지 시각화 및 주석 달기

다음 위젯을 사용하여 데이터 집합 개체의 이미지를 시각화하고 레이블을 수정할 수 있습니다. 

"Widget Javascript not detected" 오류가 발생하면 아래 명령을 실행하여 문제를 해결합니다.
<br>`jupyter nbextension enable --py --sys-prefix widgetsnbextension`


```python
from ui_utils.ui_annotation import AnnotationUI
annotation_ui = AnnotationUI(dataset, Context.get_global_context())
display(annotation_ui.ui)
```

![Azure Machine Learning 데이터 집합](media/how-to-build-deploy-image-classification-models/image_annotation.png)

## <a name="augment-images"></a>증강 이미지

[`augmentation` 모듈](https://docs.microsoft.com/en-us/python/api/cvtk.augmentation)은 [imgaug](https://github.com/aleju/imgaug) 라이브러리에 설명된 모든 변환을 사용하여 데이터 집합 개체를 증강하는 기능을 제공합니다. 이미지 변환은 단일 파이프라인으로 그룹화할 수 있으며 이 경우 파이프라인의 모든 변환이 각 이미지에 동시에 적용됩니다. 

다른 보강 단계를 별도로 적용하거나 다른 방식으로 적용하려면 여러 파이프라인을 정의하고 여기에 *augment_dataset* 함수를 전달할 수 있습니다. 이미지 증강에 대한 자세한 내용과 예제는 [imgaug 설명서](https://github.com/aleju/imgaug)를 참조하세요.

증강된 이미지를 학습 집합에 추가하면 작은 데이터 집합에 특히 유용합니다. 학습 이미지의 수가 증가하면 DNN 학습 프로세스가 느려지기 때문에 증강 없이 실험을 시작하는 것이 좋습니다.


```python
# Split the dataset into train and test  
train_set_orig, test_set = dataset.split(train_size = 0.66, stratify = "label")
print("Number of training images = {}, test images = {}.".format(train_set_orig.size(), test_set.size()))
```

    F1 2018-04-23 17:13:01,780 INFO azureml.vision:splitter splitting a dataset 
    F1 2018-04-23 17:13:01,805 INFO azureml.vision:dataset creating dataset for scenario=classification 
    F1 2018-04-23 17:13:01,809 INFO azureml.vision:dataset creating dataset for scenario=classification 
    Number of training images = 41, test images = 22.
    


```python
augment_train_set = False

if augment_train_set:
    aug_sequence = augmenters.Sequential([
            augmenters.Fliplr(0.5),             # horizontally flip 50% of all images
            augmenters.Crop(percent=(0, 0.1)),  # crop images by 0-10% of their height/width
        ])
    train_set = augment_dataset(train_set_orig, [aug_sequence])
    print("Number of original training images = {}, with augmented images included = {}.".format(train_set_orig.size(), train_set.size()))
else:
    train_set = train_set_orig  
```

## <a name="define-dnn-models"></a>DNN 모델 정의

이 패키지에는 아래와 같은 미리 학습된 심층 신경망 모델이 지원됩니다. 
+ AlexNet
+ Resnet-18
+ Resnet-34
+ Resnet-50
+ Resnet-101
+ Resnet-152

이러한 DNN은 분류자 또는 featurizer(기능 부여자)로 사용할 수 있습니다. 

네트워크에 대한 자세한 내용은 [여기](https://github.com/Microsoft/CNTK/blob/master/PretrainedModels/Image.md)에서 찾을 수 있으며 전이 학습(Transfer Learning)에 대한 기본적인 소개는 [여기](https://blog.slavv.com/a-gentle-intro-to-transfer-learning-2c0b674375a0)에서 찾을 수 있습니다.

이 패키지의 기본 이미지 분류 매개 변수는 224x224픽셀 해상도와 Resnet-18 DNN입니다. 이 매개 변수는 다양한 작업에서 잘 작동하도록 선택되었습니다. 정확도는 예를 들어, 이미지 해상도를 500x500픽셀로 높이거나 보다 심층적인 모델(Resnet-50)을 선택하여 향상시킬 수 있습니다. 단, 매개 변수를 변경하면 학습 시간이 크게 늘어날 수 있습니다. [정확도를 높이는 방법](https://docs.microsoft.com/azure/machine-learning/service/how-to-improve-accuracy-for-computer-vision-models)에 대한 문서를 참조하세요.


```python
# Default parameters (224 x 224 pixels resolution, Resnet-18)
lr_per_mb = [0.05]*7 + [0.005]*7 +  [0.0005]
mb_size = 32
input_resoluton = 224
base_model_name = "ResNet18_ImageNet_CNTK"

# Suggested parameters for 500 x 500 pixels resolution, Resnet-50
# (see in the Appendix "How to improve accuracy", last row in table)
# lr_per_mb   = [0.01] * 7 + [0.001] * 7 + [0.0001]
# mb_size    = 8
# input_resoluton = 500
# base_model_name = "ResNet50_ImageNet_CNTK"

# Initialize model
dnn_model = CNTKTLModel(train_set.labels,
                       base_model_name=base_model_name,
                       image_dims = (3, input_resoluton, input_resoluton))
```

    Successfully downloaded ResNet18_ImageNet_CNTK
    

## <a name="train-the-classifier"></a>분류자 학습

미리 학습된 DNN에 대해 다음 메서드 중 하나를 선택할 수 있습니다.

  - **DNN 구체화**: 직접 분류를 수행하도록 DNN을 학습시킵니다. DNN 학습은 느리지만 최상의 정확도를 위해 모든 네트워크 가중치를 학습 중에 향상시킬 수 있기 때문에 일반적으로 최상의 결과를 이끌어 냅니다.

  - **DNN 기능화(featurization)**: 이미지의 저 차원 표현(512, 2048 또는 4096 부동)을 확보하기 위해 DNN을 있는 그대로 실행합니다. 이렇게 확보한 표현은 별도의 분류자를 학습시키기 위한 입력으로 사용됩니다. 이 방법은 DNN이 변경되지 않기 때문에 DNN 구체화에 비해 훨씬 빠르지만 정확도는 그리 좋지 않습니다. 그럼에도 불구하고 선형 SVM과 같은 외부 분류자를 학습시키면(아래 코드 참조) 강력한 기준선을 제공할 수 있고 문제의 실현 가능성을 이해하는 데 도움이 될 수 있습니다.
  
TensorBoard는 학습 진행률을 시각화하는 데 사용할 수 있습니다. TensorBoard를 활성화하려면:
1. 아래 코드와 같이 `tensorboard_logdir=PATH` 매개 변수를 추가합니다.
1. 새 콘솔에서 `tensorboard --logdir=PATH` 명령을 사용하여 TensorBoard 클라이언트를 시작합니다.
1. TensorBoard의 지시에 따라 웹 브라우저를 엽니다. 기본적으로 localhost:6006이 열립니다. 


```python
# Train either the DNN or a SVM as classifier 
classifier_name = "dnn"

if classifier_name.lower() == "dnn":  
    dnn_model.train(train_set, lr_per_mb = lr_per_mb, mb_size = mb_size, eval_dataset=test_set) #, tensorboard_logdir=r"tensorboard"
    classifier = dnn_model
elif classifier_name.lower() == "svm":
    learner = svm.LinearSVC(C=1.0, class_weight='balanced', verbose=0)
    classifier = ScikitClassifier(dnn_model, learner = learner)
    classifier.train(train_set)
else:
    raise Exception("Classifier unknown: " + classifier)   
```

    F1 2018-04-23 17:13:28,238 INFO azureml.vision:Fit starting in experiment  1541466320 
    F1 2018-04-23 17:13:28,239 INFO azureml.vision:model starting training for scenario=classification 
    <class 'int'>
    1 worker
    Training transfer learning model for 15 epochs (epoch_size = 41).
    non-distributed mode
    Training 15741700 parameters in 53 parameter tensors.
    Training 15741700 parameters in 53 parameter tensors.
    Learning rate per minibatch: 0.05
    Momentum per minibatch: 0.9
    PROGRESS: 0.00%
    Finished Epoch[1 of 15]: [Training] loss = 2.820586 * 41, metric = 68.29% * 41 5.738s (  7.1 samples/s);
    Evaluation Set Error :: 29.27%
    Finished Epoch[2 of 15]: [Training] loss = 0.286728 * 41, metric = 9.76% * 41 0.752s ( 54.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[3 of 15]: [Training] loss = 0.206938 * 41, metric = 4.88% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 41.46%
    Finished Epoch[4 of 15]: [Training] loss = 0.098931 * 41, metric = 2.44% * 41 0.785s ( 52.2 samples/s);
    Evaluation Set Error :: 48.78%
    Finished Epoch[5 of 15]: [Training] loss = 0.046547 * 41, metric = 0.00% * 41 0.724s ( 56.6 samples/s);
    Evaluation Set Error :: 43.90%
    Finished Epoch[6 of 15]: [Training] loss = 0.059709 * 41, metric = 4.88% * 41 0.636s ( 64.5 samples/s);
    Evaluation Set Error :: 34.15%
    Finished Epoch[7 of 15]: [Training] loss = 0.005817 * 41, metric = 0.00% * 41 0.710s ( 57.7 samples/s);
    Evaluation Set Error :: 14.63%
    Learning rate per minibatch: 0.005
    Finished Epoch[8 of 15]: [Training] loss = 0.014917 * 41, metric = 0.00% * 41 0.649s ( 63.2 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[9 of 15]: [Training] loss = 0.040539 * 41, metric = 2.44% * 41 0.777s ( 52.8 samples/s);
    Evaluation Set Error :: 9.76%
    Finished Epoch[10 of 15]: [Training] loss = 0.024606 * 41, metric = 0.00% * 41 0.626s ( 65.5 samples/s);
    Evaluation Set Error :: 7.32%
    PROGRESS: 0.00%
    Finished Epoch[11 of 15]: [Training] loss = 0.004225 * 41, metric = 0.00% * 41 0.656s ( 62.5 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[12 of 15]: [Training] loss = 0.004364 * 41, metric = 0.00% * 41 0.702s ( 58.4 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[13 of 15]: [Training] loss = 0.007974 * 41, metric = 0.00% * 41 0.721s ( 56.9 samples/s);
    Evaluation Set Error :: 4.88%
    Finished Epoch[14 of 15]: [Training] loss = 0.000655 * 41, metric = 0.00% * 41 0.711s ( 57.7 samples/s);
    Evaluation Set Error :: 4.88%
    Learning rate per minibatch: 0.0005
    Finished Epoch[15 of 15]: [Training] loss = 0.024865 * 41, metric = 0.00% * 41 0.688s ( 59.6 samples/s);
    Evaluation Set Error :: 4.88%
    Stored trained model at ../../../cvtk_output\model_trained\ImageClassification.model
    F1 2018-04-23 17:13:45,097 INFO azureml.vision:Fit finished in experiment  1541466320 
    


```python
# Plot how the training and test accuracy increases during gradient descent. 
if classifier_name == "dnn":
    [train_accs, test_accs, epoch_numbers] = classifier.train_eval_accs
    plt.xlabel("Number of training epochs") 
    plt.ylabel("Classification accuracy") 
    train_plot = plt.plot(epoch_numbers, train_accs, 'r-', label = "Training accuracy")
    test_plot = plt.plot(epoch_numbers, test_accs, 'b-.', label = "Test accuracy")
    plt.legend()
```

![png](media/how-to-build-deploy-image-classification-models/output_17_0.png)


## <a name="evaluate-and-visualize-model-performance"></a>모델 성능 평가 및 시각화

평가 모듈을 사용하여 독립적인 테스트 데이터 집합에서 학습된 모델의 성능을 평가할 수 있습니다. 계산되는 평가 메트릭 중 일부는 다음과 같습니다.
 
+ 정확도(기본적으로 클래스 평균)
+ PR 곡선
+ ROC 곡선
+ 곡선 아래 영역
+ 혼동 행렬


```python
# Run the classifier on all test set images
ce = ClassificationEvaluation(classifier, test_set, minibatch_size = mb_size)

# Compute Accuracy and the confusion matrix
acc = ce.compute_accuracy()
print("Accuracy = {:2.2f}%".format(100*acc))
cm  = ce.compute_confusion_matrix()
print("Confusion matrix = \n{}".format(cm))

# Show PR curve, ROC curve, and confusion matrix
fig, ((ax1, ax2, ax3)) = plt.subplots(1,3)
fig.set_size_inches(18, 4)
graph_roc_curve(ce, ax=ax1)
graph_pr_curve(ce, ax=ax2)
graph_confusion_matrix(ce, ax=ax3)
plt.show()
```

    F1 2018-04-23 17:14:37,449 INFO azureml.vision:evaluation doing evaluation for scenario=classification 
    F1 2018-04-23 17:14:37,450 INFO azureml.vision:model scoring dataset for scenario=classification 
    Accuracy = 95.45%
    Confusion matrix = 
    [[ 0  1  0  1]
     [ 0  7  0  0]
     [ 0  0  2  0]
     [ 0  0  0 11]]
    


![png](media/how-to-build-deploy-image-classification-models/output_20_1.png)



```python
# Results viewer UI
labels = [l.name for l in dataset.labels] 
pred_scores = ce.scores #classification scores for all images and all classes
pred_labels = [labels[i] for i in np.argmax(pred_scores, axis=1)]

from ui_utils.ui_results_viewer import ResultsUI
results_ui = ResultsUI(test_set, Context.get_global_context(), pred_scores, pred_labels)
display(results_ui.ui)
```

![Azure Machine Learning 데이터 집합](media/how-to-build-deploy-image-classification-models/Image_Classification_Results.png)


```python
# Precision / recall curve UI
precisions, recalls, thresholds = ce.compute_precision_recall_curve() 
thresholds = list(thresholds)
thresholds.append(thresholds[-1])
from ui_utils.ui_precision_recall import PrecisionRecallUI
pr_ui = PrecisionRecallUI(100*precisions[::-1], 100*recalls[::-1], thresholds[::-1])
display(pr_ui.ui) 
```

![Azure Machine Learning 데이터 집합](media/how-to-build-deploy-image-classification-models/image_precision_curve.png)

## <a name="operationalization-deploy-and-consume"></a>운영화: 배포 및 소비

운영화는 모델 및 코드를 웹 서비스로 게시하고 이러한 서비스를 소비하여 비즈니스 결과를 산출하는 프로세스입니다. 

모델이 학습된 후에는 [Azure Machine Learning CLI](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/cli-for-azure-machine-learning)를 사용하여 모델을 웹 서비스로 배포하여 소비할 수 있습니다. 모델은 로컬 시스템이나 ACS(Azure Container Service) 클러스터에 배포 할 수 있습니다. ACS를 사용하면 웹 서비스를 수동으로 확장하거나 자동 확장 기능을 사용할 수 있습니다.

**Azure CLI로 로그인**

유효한 구독이 있는 [Azure](https://azure.microsoft.com/) 계정으로 다음 CLI 명령을 사용하여 로그인합니다.
<br>`az login`

+ 다른 Azure 구독으로 전환하려면 아래 명령을 사용합니다.
<br>`az account set --subscription [your subscription name]`

+ 현재 모델 관리 계정을 보려면 아래 명령을 사용합니다.
  <br>`az ml account modelmanagement show`

**배포 환경 만들기 및 설정**

배포 환경은 한 번만 설정하면 됩니다. 아직 환경이 없으면 [해당 지침](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/deployment-setup-configuration#environment-setup)을 사용하여 배포 환경을 설정합니다. 

필요에 따라 로컬 또는 클러스터 배포 설정 단계를 적절하게 수행합니다.
+ 로컬 배포는 Linux 및 Windows 10 컴퓨터에서 지원되지만 Windows 데이터 과학 VM 또는 딥 러닝 VM에서는 지원되지 않습니다. 
+ 클러스터 환경 배포는 Linux 및 Windows 모두에서 지원됩니다. 

활성 배포 환경을 보려면 다음 CLI 명령을 사용합니다.
<br>`az ml env show`
   
배포 환경을 만들고 설정하는 샘플 Azure CLI 명령

```CLI
az provider register -n Microsoft.MachineLearningCompute
az provider register -n Microsoft.ContainerRegistry
az provider register -n Microsoft.ContainerService
az ml env setup --cluster -n [your environment name] -l [Azure region e.g. westcentralus] [-g [resource group]]
az ml env set -n [environment name] -g [resource group]
az ml env cluster
```
    
### <a name="manage-web-services-and-deployments"></a>웹 서비스 및 배포 관리

다음 API를 사용하여 모델을 웹 서비스로 배포하고 웹 서비스를 관리하며 배포를 관리할 수 있습니다.

|Task|API|
|----|----|
|배포 개체 만들기|`deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model, aml_env="cluster")`
|웹 서비스 배포|`deploy_obj.deploy()`|
|이미지 채점|`deploy_obj.score_image(local_image_path_or_image_url)`|
|웹 서비스 삭제|`deploy_obj.delete()`|
|웹 서비스 없이 Docker 이미지 빌드|`deploy_obj.build_docker_image()`|
|기존 배포 나열|`AMLDeployment.list_deployment()`|
|배포 이름이 있는 서비스가 있으면 삭제|`AMLDeployment.delete_if_service_exist(deployment_name)`|

**API 설명서:** 각 모듈 및 클래스에 대한 자세한 내용은 [패키지 참조 설명서](https://aka.ms/aml-packages/vision)를 참조하세요.

**CLI 참조:** 배포와 관련된 복잡한 작업은 [모델 관리 CLI 참조](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-cli-reference)를 참조하세요.

**Azure Portal에서 배포 관리**: [Azure Portal](https://ms.portal.azure.com/)에서 배포를 추적 및 관리할 수 있습니다. Azure Portal에서 이름을 사용하여 Machine Learning 모델 관리 계정 페이지를 찾습니다. 그런 다음 모델 관리 계정 페이지 > 모델 관리 > 서비스로 이동합니다.


```python
# ##### OPTIONAL###### 
# Interactive CLI setup helper, including model management account and deployment environment.
# If you haven't setup you CLI before or if you want to change you CLI settings, you can use this block to help you interactively.
#
# UNCOMMENT THE FOLLOWING LINES IF YOU HAVE NOT CREATED OR SET THE MODEL MANAGEMENT ACCOUNT AND DEPLOYMENT ENVIRONMENT
#
# from azuremltkbase.deployment import CliSetup
# CliSetup().run()
```


```python
# # Optional. Persist your model on disk and reuse it later for deployment. 
# from cvtk import TFFasterRCNN, Context
# import os
# save_model_path = os.path.join(Context.get_global_context().storage.persistent_path, "saved_classifier.model")
# # Save model to disk
# dnn_model.serialize(save_model_path)
# # Load model from disk
# dnn_model = CNTKTLModel.deserialize(save_model_path)
```


```python
from cvtk.operationalization import AMLDeployment

# set deployment name
deployment_name = "wsdeployment"

# Optional Azure Machine Learning deployment cluster name (environment name) and resource group name
# If you don't provide here. It will use the current deployment environment (you can check with CLI command "az ml env show").
azureml_rscgroup = "<resource group>"
cluster_name = "<cluster name>"

# If you provide the cluster information, it will use the provided cluster to deploy. 
# Example: deploy_obj = AMLDeployment(deployment_name=deployment_name, associated_DNNModel=dnn_model,
#                            aml_env="cluster", cluster_name=cluster_name, resource_group=azureml_rscgroup, replicas=1)

# Create deployment object
deploy_obj = AMLDeployment(deployment_name=deployment_name, aml_env="cluster", associated_DNNModel=dnn_model, replicas=1)

# Check if the deployment name exists, if yes remove it first.
if deploy_obj.is_existing_service():
    AMLDeployment.delete_if_service_exist(deployment_name)
    
# Create the web service
print("Deploying to Azure cluster...")
deploy_obj.deploy()
print("Deployment DONE")
```

### <a name="consume-the-web-service"></a>웹 서비스 사용 

모델을 웹 서비스로 배포하면 다음 방법 중 하나를 사용하여 웹 서비스로 이미지를 채점할 수 있습니다.

- `deploy_obj.score_image(image_path_or_url)`를 사용하여 배포 개체로 웹 서비스를 직접 채점합니다.

- `AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`를 사용하여 서비스 엔드포인트 URL 및 서비스 키(로컬 배포에는 없음)를 사용합니다.

- HTTP 요청을 직접 형성하여 웹 서비스 엔드포인트를 채점합니다. 이 옵션은 고급 사용자를 위한 것입니다.

### <a name="score-with-existing-deployment-object"></a>기존 배포 개체로 채점

```
deploy_obj.score_image(image_path_or_url)
```


```python
# Score with existing deployment object

# Score local image with file path
print("Score local image with file path")
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url and remove image resizing
print("Score image url")
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url)
print("serialized_result_in_json:", serialized_result_in_json)

# Score image url with added paramters. Add softmax to score.
print("Score image url with added paramters. Add softmax to score")
from cvtk.utils.constants import ClassificationRESTApiParamters
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224], parameters={ClassificationRESTApiParamters.ADD_SOFTMAX:True})
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Time image scoring
import timeit

for img_index, img_obj in enumerate(test_set.images[:10]):
    print("Calling API for image {} of {}: {}...".format(img_index, len(test_set.images), img_obj.name))
    tic = timeit.default_timer()
    return_json = deploy_obj.score_image(img_obj.storage_path, image_resize_dims=[224,224])
    print("   Time for API call: {:.2f} seconds".format(timeit.default_timer() - tic))
    print(return_json)
```

### <a name="score-with-service-endpoint-url-and-service-key"></a>서비스 엔드포인트 URL 및 서비스 키로 채점

`AMLDeployment.score_existing_service_with_image(image_path_or_url, service_endpoint_url, service_key=None)`

```python
# Import related classes and functions
from cvtk.operationalization import AMLDeployment

service_endpoint_url = "" # please replace with your own service url
service_key = "" # please replace with your own service key
# score local image with file path
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key)
print("serialized_result_in_json:", serialized_result_in_json)

# score image url
image_path_or_url = "https://cvtkdata.blob.core.windows.net/publicimages/microsoft_logo.jpg"
print("Image source:",image_path_or_url)
serialized_result_in_json = AMLDeployment.score_existing_service_with_image(image_path_or_url,service_endpoint_url, service_key = service_key, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```

### <a name="score-endpoint-with-http-request-directly"></a>HTTP 요청으로 엔드포인트 직접 채점

다음 예제 코드는 Python에서 HTTP 요청을 직접 형성합니다. 다른 프로그래밍 언어로도 이 작업을 수행할 수 있습니다.


```python
def score_image_list_with_http(images, service_endpoint_url, service_key=None, parameters={}):
    """Score image list with http request

    Args:
        images(list): list of (input image file path, base64 image string, url or buffer)
        service_endpoint_url(str): endpoint url
        service_key(str): service key, None for local deployment.
        parameters(dict): service additional paramters in dictionary


    Returns:
        result (list): list of serialized result 
    """
    import requests
    from io import BytesIO
    import base64
    routing_id = ""

    if service_key is None:
        headers = {'Content-Type': 'application/json',
                   'X-Marathon-App-Id': routing_id}
    else:
        headers = {'Content-Type': 'application/json',
                   "Authorization": ('Bearer ' + service_key), 'X-Marathon-App-Id': routing_id}
    payload = []
    for image in images:
        encoded = None
        # read image
        with open(image,'rb') as f:
            image_buffer = BytesIO(f.read()) ## Getting an image file represented as a BytesIO object
        # convert your image to base64 string
        encoded = base64.b64encode(image_buffer.getvalue())
        image_request = {"image_in_base64": "{0}".format(encoded), "parameters": parameters}
        payload.append(image_request)
    body = json.dumps(payload)
    r = requests.post(service_endpoint_url, data=body, headers=headers)
    try:
        result = json.loads(r.text)
    except:
        raise ValueError("Incorrect output format. Result cant not be parsed: " + r.text)
    return result

# Test with images
images = [test_set.images[0].storage_path, test_set.images[1].storage_path] # A list of local image files
score_image_list_with_http(images, service_endpoint_url, service_key)
```

### <a name="parse-serialized-result-from-web-service"></a>웹 서비스에서 직렬화된 결과 구문 분석

웹 서비스의 출력은 JSON 문자열입니다. 이 JSON 문자열은 다른 DNN 모델 클래스로 구문 분석할 수 있습니다.


```python
image_path_or_url = test_set.images[0].storage_path
print("Image source:",image_path_or_url)
serialized_result_in_json = deploy_obj.score_image(image_path_or_url, image_resize_dims=[224,224])
print("serialized_result_in_json:", serialized_result_in_json)
```


```python
# Parse result from json string
import numpy as np
parsed_result = CNTKTLModel.parse_serialized_result(serialized_result_in_json)
print("Parsed result:", parsed_result)
# Map result to image class
class_index = np.argmax(np.array(parsed_result))
print("Class index:", class_index)
dnn_model.class_map
print("Class label:", dnn_model.class_map[class_index])
```


## <a name="next-steps"></a>다음 단계

아래 문서에서 Azure Machine Learning Package for Computer Vision에 대해 알아봅니다.

+ [이 모델의 정확도를 높이는 방법](how-to-improve-accuracy-for-computer-vision-models.md)을 알아봅니다.

+ [패키지 개요를 읽고 설치 방법을 알아봅니다](https://aka.ms/aml-packages/vision).

+ 이 패키지에 대한 [참조 설명서](https://docs.microsoft.com/python/api/overview/azure-machine-learning/computer-vision)를 탐색합니다.

+ [Azure Machine Learning용 다른 Python 패키지](reference-python-package-overview.md)를 자세히 알아봅니다.
