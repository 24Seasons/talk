# 📄 스크립트

![2](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/02.png?raw=true)

요즘 들어 해지는 시간이 점점 늦춰진다는 느낌이 들고 있지 않으신가요? <br> 낮과 밤의 길이가 같아지는 오늘, 춘분을 맞이해서 24절기톡의 첫 번째 발표를 진행하려고 합니다.

## AWS로 나만의 네트워크 구축하기

AWS 네트워크에 대해서, VPC와 기본 사용법에 대해서 알아보도록 하겠습니다. <br>AWS 서비스의 콘솔 사용법 등의 자세한 내용 다루지 않습니다.

## 클라우드 서비스란?

![3](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/03.png?raw=true)

클라우드 서비스란 타사 제공 업체가 인터넷을 통해 사용자가 언제 어디서든지 원하는 만큼의 IT 리소스를 사용할 수 있도록 해주는 서비스라고 할 수 있습니다.

## AWS의 클라우드

![4](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/04.png?raw=true)

보이시는 이미지는 AWS 클라우드의 글로벌 인프라 맵입니다. 여러 리전들에 분포가 되어있습니다.

![5](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/05.png?raw=true)

AWS 클라우드를 규모에 따라 나누어 보았습니다. <br>각각의 리전 내에 두 개 이상의 사용 영역이 있고, 가용 영역은 데이터 센터들을 가지고 있습니다.

![6](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/06.png?raw=true)

이렇게 한 개 이상의 데이터 센터들이 모여서 가용 영역을 이루고 있습니다.<br>
각 가용 영역은 물리적으로 떨어뜨려서 위치를 시키는데, 재해, 정전 등의 사고 발생 시 하나에 문제가 생겨도 다른 곳에서는 사용이 가능하도록 하기 위해서입니다.<br>
(여기에 vpc와 서브넷까지 추가해서 그려보겠습니다.)

![7](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/07.png?raw=true)

AWS 클라우드 내에 리전이 있고, VPC 내에 가용 영역, 그 내부에 서브넷이 있고 ec2 인스턴스가 있습니다.<br>
여기서 가장 중요한 부분이 VPC입니다. 앞으로 설명할 내용들을 들으면 왜 이 VPC가 중요하고, aws의 리소스들을 VPC 내에 위치 시켜서 사용해야 하는가에 대해서 알 수 있습니다.

## VPC란?

Virtual Private Cloud의 약자로 AWS 클라우드 내 논리적으로 독립된 섹션을 제공합니다.

![8](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/08.png?raw=true)

초기 서비스는 EC2 인스턴스를 생성하면 이렇게 AWS Cloud 내에서 어디인지도 모르게 생성이 되었다고 합니다.<br>
인스턴스들이 랜덤하게 한 네트워크 안에 생겼습니다.

![9](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/09.png?raw=true)

2009년 이후부터는 이렇게 기본적으로 VPC를 포함하고 있게 되었다고 합니다.<br>
각각의 독립된 VPC 내부에 여러 리소스들을 위치시킬 수 있게 된 것이죠. <br>
이렇게 되면 VPC는 프라이빗 아이피를 할당받게 되고, 그 내부에서 더 작은 단위의 서브넷을 만들게 됩니다.

![10](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/10.png?raw=true)

예를 들어 DB처럼 보안이 매우 중요한, 외부에서 접근하면 안 되는 서비스들은 프라이빗 서브넷에 위치 시켜서 외부와의 경로를 완벽하게 차단 시키고.<br>
웹 서비스 같은 경우 유저들이 이용할 수 있도록 퍼블릭 서브넷에 위치 시켜서 접근을 허용하는 것이죠.<br>
이처럼 vpc와 서브넷을 사용해서 각각의 서비스들의 요구에 맞게 가상 네트워크를 설정할 수 있게 해줄 수 있습니다.

![11](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/11.png?raw=true)

그럼 이제 본격적으로 이 VPC를 구축하는 과정을 조금 더 자세하게 순차적으로 살펴보겠습니다.<br>
사용할 수 있는 아이피 주소의 범위를 지정해 주고, 가용 영역 별로 서브넷을 설정해 주고, 라우트 테이블을 설정해서 인터넷과 통신할 수 있도록 해줍니다. <br>
이렇게 모든 연결이 되면 그다음에 접속 권한을 위한 보안 설정을 합니다.

## IP 주소 범위 선택

![12](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/12.png?raw=true)

VPC 생성 시 아이피 범위는 RFC1918이라는 사설 아이피 대역에 맞춰서 구축을 해야 합니다.

### 사설 아이피

사설 아이피란 VPC 내부에서 사용하는 아이피 대역입니다.
이거에 관련해서 [해당 블로그에서](https://medium.com/harrythegreat/aws-%EA%B0%80%EC%9E%A5%EC%89%BD%EA%B2%8C-vpc-%EA%B0%9C%EB%85%90%EC%9E%A1%EA%B8%B0-71eef95a7098) 아주 적절한 예시가 있어서 간단히 소개해 드리면

퍼블릭 아이피 주소 : 우리 집 주소<br>
프라이빗 아이피(사설 아이피) : 안방

집에서 누가 “안방에서 리모컨 좀 가져다줘"라고 하면 옆집을 가는 게 아니라 우리 집에 있는 ‘안방'으로 찾아갑니다. 안방이 프라이빗 아이피가 되는 것이죠.<br>
’안방', ‘작은방'처럼 내부에서 쓰는 주소를 사설 아이피 대역이라고 부르고 내부 네트워크 내에서 위치를 찾아갈 때 사용합니다.

### RFC 1918

그럼 RFC1918은 무엇이고 왜 사용해야 할까요? 생각보다 단순합니다.<br>
다른 네트워크와 겹치는 범위를 피하기 위함입니다. 공인이 아닌 사설 아이피를 사용할 때는 이 범위 안에서 사용하도록 하는 규칙이죠.<br>
이 RFC 규약을 지키지 않고 사용하면 어떻게 될까요?<br>

54로 시작하는 아이피가 퍼블릭으로 사용되고 있는데, 54로 시작되는 아이피를 프라이빗으로 사용했다면 네트워크가 엉켜버립니다.<br>
프라이빗 아이피 내에서 외부로 요청을 보내는 아이피가 54라면 외부로 빠져나가지 않고 내부에서 갇혀 있는 것입니다.

## 서브넷 설정

![13](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/13.png?raw=true)

이렇게 아이피 범위를 정하고 나면 서브넷이라는 조금 더 작은 단위의 공간을 만듭니다. 가용 영역 내부에 존재하고 EC2, RDS 같은 리소스들을 위치 시킬 수 있습니다. 아직까지는 어디하고도 통신을 할 수가 없습니다.

## 라우트 설정

![14](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/14.png?raw=true)

이제 인터넷과 통신하기 위해 라우팅 테이블과 라우터 설정을 해야 합니다. 라우터는 목적지이고, 라우팅 테이블은 목적지로 향하는 이정표입니다.<br>
이 의미는 내 VPC로 향하는 트래픽은 내 VPC 내에 머물러라, 라우팅 하라는 의미입니다.

기본적 설정은 이렇게 내부 범위에서 찾음, 외부로 통하는 트래픽은 처리가 불가능합니다.<br>
예를 들면 이렇게만 설정을 하면 EC2 인스턴에 서버 소스코드를 설치하기 위해 git clone을 받을 수 없습니다.

![15](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/15.png?raw=true)

인터넷 게이트웨이를 통해 외부 인터넷과 통신을 할 수 있습니다. 오른쪽에 있는 서브넷은 인터넷 게이트웨이와 연결을 해줬습니다.

local ip가 아닌 다른 곳으로 가려고 한다면 (모든 네트워크를 목적지로 한다면) 인터넷 게이트웨이로 보내라는 의미입니다.
10.~ 의 내부 네트워크 범위가 아니라면 외부로 보내게 되죠. 이렇게 연결되어 있는 걸 퍼블릭 서브넷이라고 부릅니다.

이렇게까지 하면 퍼블릭 서브넷은 외부와 통신이 가능하지만 프라이빗 서브넷을 외부와 통신이 불가능합니다.
인스턴스를 프라이빗하게 관리하면서 외부와 통신을 하고 싶다면 어떻게 해야 할까요?

![16](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/16.png?raw=true)

퍼블릭 서브넷에 위치한 NAT Gateway를 통해서 인터넷 게이트웨이 처럼 local ip가 아닌 다른 목적지에 대해서는 NAT Gateway로 보내고 이를 통해서 외부와 통신이 가능합니다.

이때 프라이빗 서브넷은 퍼블릭 아이피가 없기 때문에 내부에서 외부의 방향으로만 통신이 가능하고 반대로의 통신은 불가능합니다.
이렇게 하면 드디어 외부와 통신하기 위한 경로까지 모두 만들었습니다. 이제 마지막 보안 설정을 알아보겠습니다.

## 보안 설정

보안을 위해서는 연결된 경로들을 모두에게 열어두는 것이 아니라 딱 필요한 곳에서만 사용할 수 있도록 제한하는것이 좋습니다.

![17](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/17.png?raw=true)

네트워크 ACL과 보안 그룹은 인바운드, 아웃바운드 규칙을 설정해서 방화벽 같은 역할을 할 수 있습니다.
네트워크 ACL은 서브넷 단위로 적용할 수 있고, 보안 그룹은 개별 인스턴스 별로 적용할 수 있습니다.

![18](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/18.png?raw=true)

네트워크 ACL의 콘솔 화면에서 아래의 규칙을 보면 인바운드, 아웃바운드 모두 모든 트래픽에 대해서 허용을 합니다.
네트워크 ACL은 stateless 방식으로 동작합니다.
stateless 들어올 때 커넥션을 기억하지 못하고 열어 줬으면 나가는 설정도 따로 해주어야합니다. 그렇기 때문에 모든 범위를 열어둡니다.

![19](https://github.com/24Seasons/talk/blob/main/contents/210321-danny/images/19.png?raw=true)

보안 그룹은 이렇게 인스턴스 별로 설정이 가능합니다.
예를 들어 왼쪽이 클라이언트 서버이고 오른쪽이 백엔드 서버라면 보안 그룹의 규칙을 클라이언트 서버는 80, 443 포트로 들어오는 모든 요청에 대해서 허용을 할 수 있고,
백엔드 서버는 클라이언트의 보안 그룹의 id를 소스로 사용해서 클라이언트로부터 들어오는 요청에 대해서만 허용할 수 있습니다.

즉 sg-xx 아이디를 사용하는 보안 그룹에 속안 인스턴스만 접속이 가능합니다.
보안 그룹은 statefull 하게 동작합니다.
인바운드 트래픽이 들어올 때 커넥션을 기억하고, 들어올 때 허용했기 때문에 나갈때도 허용을 하게됩니다.
