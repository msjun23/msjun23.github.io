---
title: "[Event Camera] Event based HDR Image and HFR Video Generation using cGAN"
excerpt: "How to generate HDR imgae and HFR video from event camera: using Conditional GAN"
toc: true
toc_sticky: true
categories:
  - Paper Review
tag:
  - Paper Review
  - Study
  - Event camera
  - GAN
  - cGAN
use_math: true
last_modified_at: 2022-08-18T14:30-15:30
---

> 이벤트 카메라로부터 얻은 정보를 cGAN의 입력으로 하여 HDR(High Dynamic Range) 이미지 혹은 HFR(High Frame Rate) 비디오를 생성하는 방법론에 대한 논문이다.

# Title
[Event-based High Dynamic Range Image and Very High Frame Rate Video Generation using Conditional Generative Adversarial Networks](https://arxiv.org/abs/1811.08230)

# Abstract
본 논문은 이벤트 카메라를 활용하여 HDR 이미지 혹은 HFR 비디오를 생성하는 방법론을 제안했다. 내가 생각하는 main contribution은 event stacking과 이를 입력으로 하는 cGAN의 활용이다. 그리고 본 논문을 읽기 위해선 **이벤트 카메라**란 무엇인지를 알아야 할 필요가 있다.

## Event Camera
아직까진 많이 알려지지 않은 센서라고 생각한다. 해당 카메라(혹은 센서)가 기존의 카메라 센서에 비해 인지도가 낮고 많이 활용되지 못하는 이유는 무엇일까. 바로 기존의 카메라와는 전혀 다른 동작 방식 때문이라 생각한다.

이벤트 카메라는 기존의 다른 카메라 센서들과는 다르게 각각의 픽셀들이 독립적이고 비동기적으로 밝기 변화에 대응한다. 나름대로 이해한 바를 쉽게 풀어쓰자면, 움직이는 물체만 볼 수 있는 카메라이다. 즉, 기존의 카메라와는 현저하게 다른 데이터를 출력하고, 이미 사용되고 있는 다양한 vision 알고리즘을 바로 적용할 수 없다. 이러한 이유가 이벤트 카메라 활용의 장벽으로 작용하는 것 같다.

그렇다면 굳이 이벤트 카메라를 사용하려는 이유는 무엇인가. 그 이유는 다음과 같다.

> - Low latency
> - High temporal resolution
> - High dynamic range

기존의 카메라에 비해 매우 낮은 지연을 가지고 있고, 각각의 픽셀들이 독립적으로 밝기 변화에 반응하다보니 빛(카메라 센서의 치명적인 문제점)이라는 요소에 보다 강인함을 보인다. 그렇기에 이벤트 카메라로부터 얻은 데이터를 (기존의 카메라에서 얻은 일반적인 이미지처럼) reconstruction을 수행한다면 빛의 세기에 강인한 이미지를 얻을 수 있게되는 것이고, 해당 이미지에서 기존의 vision 알고리즘을 적용한다면, 밝기 변화에 강인한 application이 가능하게 될 것이다. ([이벤트 카메라에 대한 보다 자세한 설명글](https://taeyoung96.github.io/research/EventSurvey/), 출처: Taeyoung's Blog)

이와 같은 이벤트 카메라의 potential을 unlock하고자 한 것이 본 논문의 goal이라고 볼 수 있을 것 같다.

# Introduction
이벤트 카메라는 기존 카메라에 비해 *ms* 단위의 *low latency*와 거의 *$1\mu s$* 수준의 *high temporal resolution* 그리고 *high dynamic range*를 가진다. 이와 같은 이벤트 카메라의 정보를 활용한다면 기존의 카메라보다 더욱 활용도 높은 이미지를 생성할 수 있을 것이다. *Figure 1*에서 이벤트 카메라의 raw 데이터와 APS(Active Pixel Sensor) 프레임, 그리고 본 논문에서 제시한 방식으로 재생성한 이미지를 비교해 볼 수 있을 것이다.

![fig1](/assets/images/PR-Event-based-HDR-HFR/fig1.png)

해당 논문에서는 단순한 이벤트들을 **SBT(Stacking Based on Time), SBE(Stacking Based on Events)**와 같은 새롭게 제안하는 방식으로 *stacking*하여 활용하고, 최종적으로 기존의 카메라에서는 불가능했던 motion blur가 없는 HDR 이미지와 $1ms$ 수준의 FPS를 가지는 HFR 비디오를 생성할 수 있음을 보인다.

# Conditional GANs
Conditional GANs(cGAN, cGANs)은 본 논문에서 메인 architecture로 사용된다. 이벤트 데이터를 활용하는데 cGANs을 사용한 것이 효율이 있음을 보인 정성적인(qualitative) 연구는 이전까지 없었다고 본 논문에서는 서술하고 있다. 동시에 cGANs은 스타일 전이(style transfer)에서 좋은 성능을 보였고, 한가지 representation에서 이미지 도메인으로의 변환에 주로 적용되었음을 논문 ref를 통해 말한다.

이벤트 카메라 데이터가 가지는 이점(low latency, high temporal resolution, high dynamic range)들을 잘 살리면서 이미지 도메인으로 변환(혹은 생성)하기 위해 cGANs을 사용한다는 것이 본 논문의 contribution 중 하나인 듯 하다.

# Proposed method
cGANs은 주어진 이미지 $x$와 랜덤 노이즈 벡터 $z$를 사용하여 출력 이미지 $y$를 만들어 낸다. Generator $G$가 GT(Ground Truth)와 유사한 이미지를 만들고, 이 이미지가 실제인지 생성된 것인지 판별하는 Discriminator $D$가 존재하는 기본 개념은 동일하다.

cGANs을 활용하기 위해, 이벤트 데이터를 바로 사용 가능하도록(off-the-shelf) 하기 위한 새로운 방법론을 제안하고, 학습 네트워크에 대한 내용이 이어진다.

## Event stacking: SBT vs. SBE
먼저 이벤트 카메라를 통해 얻게되는 각각의 이벤트가 어떻게 표현되는지 알아야한다. 각각의 이벤트($e$)는 네 가지 성분의 튜플로 표현된다.

> $(u, v, t, p)$
> 
> $u, v: $ 픽셀 좌표
>
> $t: $ timestamp
> 
> $p = \pm1: $ 이벤트의 극성(polarity)

이러한 이벤트 데이터들을 네트워크의 입력으로 만들기 위해 $p(u,v,t)$와 같이 3D 볼륨 형태로 stacking할 필요가 있다. 이벤트 카메라의 해상도를 $\delta t$, 이벤트를 쌓을 시간(time duration)을 $t_d$라 하고, 한 주기 동안 이벤트를 쌓는다면 그 깊이는 $n=t_d/\delta t$ 가 될 것이다. 즉 $n$채널의 이미지라고 볼 수 있다. 이러한 $n$채널 이미지는 한 주기 동안 발생한 모든 이벤트에 대한 정보를 담고 있다. 하지만 한 가지 문제점이 발생하는데, 바로 입력 이미지의 채널이 너무 커진다는 것이다.

$t_d$를 $10ms$로 설정했을 시, 이벤트 카메라의 해상도(temporal resolution)은 $1\mu s$이므로 $n=10*10^{-3}/10^{-6}=10K$, 즉, 채널이 $10K$인 이미지가 된다.






