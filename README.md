# Project Name : ATmega128을 이용한 전압,주파수 측정 회로 설계
--------------------------------------------------------------
## 수행 목표 : 
              반전증폭기 2개를 사용하여 초기 전압을 설계한다.
              ATmega128에 입력된 디지털 전압의 값을 아날로그로 나타낼 수 있도록 설계한다.
              함수발생기를 통해 발생한 주파수의 값을 아날로그로 나타낼 수 있도록 설계한다.
 ---------------------------------------------------------------             
## 사용기술/사용기기 : PSIM, WinAVR , Function-Generator , Oscilloscope
 ----------------------------------------------------------------

## 설계한 회로도
<img width ="50%" src= "https://user-images.githubusercontent.com/122782748/214090196-d3f72fab-dc88-45e9-b931-6c797535cce8.png"/>


## 실험 내용
<img width = "60%" src="https://user-images.githubusercontent.com/122782748/214091731-0712339f-7e19-4980-9e8e-ceef04202502.png"/>
<img width = "60%" src="https://user-images.githubusercontent.com/122782748/214093022-4f607c4e-5aa8-4b4e-9f2b-704b2a03d3c2.png"/>
<img width = "60%" src="https://user-images.githubusercontent.com/122782748/214093348-b8556246-2987-4d0e-9eb3-268e8c83778c.png"/>

## 사용 코드
<img width = "60%" src="https://user-images.githubusercontent.com/122782748/214094102-9b02d728-04eb-439d-8aa4-163a36ec68f1.png"/>
위의 <그림 2-1?은 초기 설정 코드를 나타낸 것임. 
9행의 unsigned char Num_code[10]코드는 0~9의 숫자를 나타내며
10행의 unsigned char seg_code[4]코드는 첫 번째, 두 번째, 세 번째, 네 번째의 7-Segment를 나타냄.
그 아래의 코드는 변수들을 선언한 것이며 F포트는 ADC의 입력단이며 채널은 1로 설정함.
B포트는 스위치 포트이며 0을 입력시, 전압을 측정하며 1을 입력시 주파수 측정으로 변환이 가능함. 그리고 A포트는 7-Segment, C포트는 74AHC574인 3상 래치와 연결됨.
---
<img width = "60%" src="https://user-images.githubusercontent.com/122782748/214096086-7e283a8d-e6de-47c1-9b23-752763eaae51.png"/>
위의 그림 2-2는 PINB=0인 전압을 측정하여 표시하는 코드이며 Vref = 2.56으로 설정함. 그 이유는 ATmega128A의 내부의 2.56V를 기준전압으로 사용하기 때문임.
그리고 fValue = ad_value/1023.0 * Vref의 공식을 사용하는 이유는 A/D변환의 결과값의 범위가 0~1023인 디지털 값을 다시 실제 입력된 아날로그 전압값으로 나타내야 하기 때문임.
52행의 displayvaule = maxvalue * 9.41인 이유는 교류전압의 peak값을 표시하기 위해서
증폭기를 통해 낮춘 전압을 다시 보정해야하기 때문임. 이렇게 전압 측정 코드를 통해 그림 1-3의 결과인 10.6V를 나타낼 수 있음. 측정값이 10.6V인 이유는 측정시 함수발생기의 Vpp가 24V가 아닌 20V였기 때문임.

<img width="60%" src = "https://user-images.githubusercontent.com/122782748/214096971-1aa1edde-f651-4937-82a6-3ea612be2307.png"/>
위의 그림 2-3은 PINB(스위치)=1인 상태인 주파수 측정 코드임. 64행에서 i<200인 이유는 딜레이가 총 5ms이기 때문임. NHz를 측정하기 위해서는 1초당 N사이클을 측정해야 하는데
1000ms = 1s이기 때문에 64행의 i<200이라는 수치가 도출됨.

<img width="60%" src = "https://user-images.githubusercontent.com/122782748/214098080-cb9e4fd0-0504-48f2-86a1-4e5b5ae4bf40.png"/>
위의 그림  2-4는 LCD Display 함수 코드를 나타냄. A포트는 7-Segment의 0~9의 숫자를 나타내며 D포트는 7-Segment의 순서를 나타낸 것임. integer10은 10의 자릿수를 나타내며 첫 번째 7-Segment의 숫자를 나타냄. integer1은 1의 자릿수를 나타내며 두 번째 7-Segment를 나타냄. 세 번째 7-Segment는 소수점으로 나타냈으며 소수점은 0x7f로 표현함. 104행~107행은 네 번째 7-Segment의 소수점 첫째자리를 구하는 코드임.
이를 통해서 <그림 1-5>의 측정값을 구할 수 있음.

<img width="60%" src = "https://user-images.githubusercontent.com/122782748/214098832-f8af0f28-cb0e-4f80-96ca-3384133242b3.png"/>
위의 <그림2-5>는 주파수 Display 함수 코드를 나타낸 것임. A포트는 7-Segment의 0~9까지 숫자를 나타내며 D포트는 7-Segment의 순서를 나타냄. j1000은 1000의 자리 수를 나타내며
j100은 100의 자리수를 나타냄. 하지만 7-Segment가 4개이고 세 번째 자리는 소수점으로 할 것이고 정수에 해당하는 값을 10의 자리로 나타내야 하기 때문에 첫 번째 7-Segment는 j10부터 입력함. 세 번 째 자리의 0x7f는 소수점을 4번째 7-Segment는 소수점 첫째자리를 나타내지만 주파수 표시이므로 0으로 지정함.

## 고찰

실험 조건으로 입력전압을 12V ( Vpp = 24V)로 진행을 하려고 했으나 12V가 아닌 10.6V가 나오는 이유는 함수발생기로 Vpp=24V의 입력이 불가능했기 때문이다.
이에 대한 차선책으로 Vpp를 20V로 설정하였다. 이상적인 값으로 실험하고 싶었지만 그렇지 못해 아쉬웠다. 그리고 설계를 하면서 예상치 못한 곳에서 시간을 많이 썼는데
7-Segment의 에노드를 사용해야하는데 캐소드가 섞여 있었다. 캐소드와 에노드는 Ground와 Vin이 반대이기 때문에 꼼꼼히 확인하고 설계했어야했는데 다음부터 잘 살펴봐야겠다.
또한 fValue = ad_value/1023.0 * Vref라는 식을 활용하여 7-Segment에 아날로그 값이 나오도록 설계를 함으로써 ATmega128이 어떻게 작동하는지 눈으로 확인해 볼 수 있어서 유익한 실험이었다.

