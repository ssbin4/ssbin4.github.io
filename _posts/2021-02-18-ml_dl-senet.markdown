---  
layout: post  
title: "SENet(Squeeze-and-Excitation Networks)"  
subtitle: "SENet(Squeeze-and-Excitation Networks)"  
categories: ml/dl
tags: paper_review
comments: true  
---  
  
> SENet 논문 리뷰입니다.


[SENet 논문 링크](https://arxiv.org/abs/1709.01507)

겨울방학 연구참여 중 발표한 내용을 다시 정리한 것입니다. 혹시 잘못된 내용이 있다면 언제든지 알려주시면 감사하겠습니다.

---

* __Introduction__
  ![slide2](/assets/img/ml_dl/senet/slide2.PNG)

  - 2017년 ImageNet Challenge 우승 모델로서, 이전 해의 우승 모델에 비해 약 25%의 성능 향상을 보인 모델이다.
  - 기존 모델들이 상대적으로 주목하지 않은, 채널들 간의 관계에 집중하였다.
  - SE Block, 즉 Squeeze(압축)과 Excitation(자극)의 과정을 거치는 block을 기본 단위로 한다. convolution을 통해 나온 각각의 채널간의 상호 의존성을 명시적으로 모델링해주어 전체 네트워크의 표현력을 높인 것이 특징이다.
  - attention mechanism: 중요한 특징은 강조하고 덜 중요한 특징은 덜 강조하는 방식을 사용했다. 이 때, global information을 사용한다.

---

* __Related Works__ 
  - Deeper architectures

  ![slide3](/assets/img/ml_dl/senet/slide3.PNG)

  [VGGNet](https://arxiv.org/abs/1409.1556), [Inception](https://arxiv.org/abs/1409.4842), [Resnet](https://arxiv.org/abs/1512.03385) 등 이전의 모델들은 더 깊은 아키텍쳐를 만들기 위한 노력에 집중하였다. 이는 feature hierarchy 상에서 spatial encoding의 quality를 높임으로써 더 좋은 네트워크를 만들기 위한 노력에 해당한다.

  - Cross-channel Correlations

  ![slide4](/assets/img/ml_dl/senet/slide4.PNG)

  이전의 채널 간 상호관계에 대한 연구들에서는 특징들 간의 새로운 조합에 집중하였다.
  이는 sptial structure에 관계없이([Xception](https://arxiv.org/abs/1610.02357)) 또는 1x1 convolutional filter를 중간에 삽입하는 방식([NiN](https://arxiv.org/abs/1312.4400))으로 이루어졌다. 이러한 논문들은 채널들 간의 관계가 local receptive field에 대한 instance-agnostsic function들의 조합으로 나타내어 질 수 있다는 가정에 기반한 것인데, SENet에서는 local이 아닌 global information을 사용하여 채널들 간의 non-linearity를 추가한 것이라고 한다.

  - Attention and gating mechanisms

  ![slide5](/assets/img/ml_dl/senet/slide5.PNG)

  attention mechanism은 중요한 부분이나 특징들을 더 집중하여 보도록 하는 것으로, 원래 NLP에서 사용된 것이라고 한다. 이전에 attention mechanism을 computer vision에 적용하려는 시도가 있었는데([residual attention network](https://arxiv.org/abs/1704.06904)), SENet은 gating mechanism이 훨씬 간단하고 효율적이라고 한다.

---

* __SE Blocks__
  
  SENet의 핵심 구성요소인 SE Block에 대해 자세히 알아보자.

  1) transformation 과정

  ![slide6](/assets/img/ml_dl/senet/slide6.PNG)

    일반적인 convolution 연산으로, input X를 filter v를 통해 feature map U로 변형시키는 과정이다. u<sub>c</sub>는 filter가 v<sub>c</sub>일 때 c번째 output channel을 뜻한다.

  2) squeeze 과정

  ![slide7](/assets/img/ml_dl/senet/slide7.PNG)

    squeeze, 즉 주어진 정보를 압축하는 과정이다. 각 feature map의 정보를 하나의 수로 나타내게 되는데, 주어진 식에서 알 수 있듯이 global average pooling을 이용하였다. 논문에서는 이외에 max pooling과 같은 다른 방법들도 사용할 수 있다고 언급하였다.

  3) excitation 과정

  ![slide8](/assets/img/ml_dl/senet/slide8.PNG)

  압축한 정보를 non-linearity function을 통해 조정하는 과정이다. 2개의 fully connected layer를 삽입하여 채널들 간의 dependency를 학습시키는 과정을 거치는데, 첫번째 layer에서는 ReLU, 두 번째 layer에서는 sigmoid를 activation function으로 사용하였다. 이 때, 단순히 2개의 FC layer를 삽입한다면 parameter의 수가 급격히 늘어나므로 이를 막기 위해 dimension reduction 과정을 거친다.

  4) scale 과정

  ![slide9](/assets/img/ml_dl/senet/slide9.PNG)

  excitation 과정을 마친 후 나온 1x1xc matrix를 각각의 feature map에 곱해주는 과정이다. 각각의 feature map의 중요도만큼 재조정이 이루어진다고 할 수 있다.

---

* __Model and Computational Complexity__

  ![slide11](/assets/img/ml_dl/senet/slide11.PNG)

  연산량의 늘어난 수치(표에서 GFLOPs 부분)을 확인해보면 증가가 미미한 것을 확인할 수 있다.

   ![slide12](/assets/img/ml_dl/senet/slide12.PNG)

   parameter의 수는 2개의 FC layer에서만 추가 되고, 이러한 증가는 약 10%에 해당한다고 한다. 이 때, 마지막 두 단계에서의 SE Block을 제거하면 parameter의 수는 4% 증가로 감소하는 반면, 성능 감소는 약 0.1%에 지나지 않으므로 이 방법을 택할 경우 더 효율성이 좋아진다. 그림에서 알 수 있듯 cxc의 FC layer 대신 dimension을 reduction ratio r로 나누고 다시 r을 곱해주는 방식으로 parameter의 수를 줄일 수 있다.

* __Experiments__

  ![slide13](/assets/img/ml_dl/senet/slide13.PNG)

  error rate의 감소가 dramatic 하진 않지만 network depth와 residual block의 유무에 관계 없이 모든 경우에서 error rate의 감소가 확인되었다.

  ![slide14](/assets/img/ml_dl/senet/slide14.PNG)

  mobile에 최적화된 network 들에서도 error rate 감소가 확인되었으며, image classification 이외의 다른 task(object detection 등)에서도 performance의 상승을 확인할 수 있다.

  ![slide16](/assets/img/ml_dl/senet/slide16.PNG)

  ImageNet Challenge 2017의 우승 모델이며, 여기서는 ensemble network를 사용하였고, ResNeXt를 변형한 구조를 base로 하고 SE Block을 추가한 형태로 network를 만들었다고 한다.

---

* __Ablation Study__

  ![slide17](/assets/img/ml_dl/senet/slide17.PNG)

  - reduction ratio

    r이 커질수록 parameter의 수는 급격히 감소하지만 error rate는 많이 변하지 않는다. r=16이 performance와 complexity 간의 balance가 잘 맞아 이를 default로 택했다고 한다.
  
  - squeeze

    squeeze 연산에서 average pooling과 max pooling을 비교해본 결과 average pooling의 성능이 더 좋았지만, 이는 절대적인 것은 아니고 환경에 따라 뒤바뀔 수 있다고 한다.

  - excitation

    2번째 FC layer에서의 activation function을 sigmoid로 하였을 때가, tanh나 ReLU보다 더 성능이 좋았다.

---

* __Role of SE Blocks__

  - Squeeze의 역할

  ![slide18](/assets/img/ml_dl/senet/slide18.PNG)

  squeeze와 parameter 수는 같으면서 squeeze의 역할은 하지 않는 layer를 삽입한 NoSqueeze라는 모델과 비교했을 때, squeeze 연산이 있는 모델이 performance, complexity 두 측면에서 모두 좋았다.

  - Excitation의 역할

  ![slide19](/assets/img/ml_dl/senet/slide19.PNG)

  초기 layer에서는 각기 다른 class에 대한 distribution이 거의 같음이 확인된다. 이는 feature channel의 중요도가 초기 layer에서는 공유되는 것으로 추측할 수 있다.

  ![slide20](/assets/img/ml_dl/senet/slide20.PNG)

  이후 더 깊은 층의 layer에서는 class-specific하게 excitation이 일어나고 있는 것을 확인할 수 있다.

  ![slide21](/assets/img/ml_dl/senet/slide21.PNG)
  
  특이한 점으로 마지막에서 두 번째 layer에서는 거의 모든 activation 값이 1에 가까웠고, 마지막 layer에서는 activation의 변화가 작은 것을 확인할 수 있다. 이를 바탕으로 이 두 개의 SE block을 제거하였을 때, 앞서 언급했던 것처럼 parameter의 수는 감소시키면서 성능 감소는 0.1%만 가져온 것을 확인할 수 있었다.

---

* __Conclusion__

  핵심 아이디어는 Squeeze와 Excitation을 결합한 SE Block을 사용한 것이다. 이는 채널 간 의존성을 모델링해주기 위한 것으로, complexity는 아주 조금 증가하면서 performance를 유의미하게 증가시켜 ImageNet Challenge 2017에서 우승할 수 있었다.

  논문에서는 complexity를 줄이기 위해 dimension reduction이 필수적이었으나, 이후의 논문에서는 채널간 의존성을 파괴한다는 보고가 있었고, dimension reduction을 위한 더 효율적인 방법을 제시했다고 한다.([ECA-Net](https://arxiv.org/abs/1910.03151))

  개인적으로 궁금했던 점은, 보통의 경우 sigmoid는 activation function으로 잘 쓰이지 않는다고 배웠는데, 왜 이 경우에서는 tanh나 ReLU보다 성능이 더 뛰어났는지가 궁금했다.

---

> 참고자료
  - [https://sike6054.github.io/blog/paper/seventh-post/](https://sike6054.github.io/blog/paper/seventh-post/)
  - [https://jayhey.github.io/deep%20learning/2018/07/18/SENet/](https://jayhey.github.io/deep%20learning/2018/07/18/SENet/)
  - [CS231n slides](http://cs231n.stanford.edu/slides/2020/lecture_9.pdf)


