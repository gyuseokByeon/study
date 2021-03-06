 
우리의 목적은 컨테이너, VM, 반가상화 워크로드를 위해 공통 가상 네트워킹 인프라 기반을 제공하는 것이다.
우리는 컴퓨터를 이용하는 복잡한 환경을 지원하기 위해 통합된 가상 네트워크가 필요하다고 생각한다.
어플리케이션 워크로드는 각각의 도달하는 것과 그들이 필요한 인프라 기반 서비스 할 수 있다. 

우리는 컨테이너를 위해 이미 가상화 반가상화를 지원하는 SDN을 주목하기로 했다.
현재 네트워킹 솔루션은 x86 가상화 초창기 상태가 연상되는 컨테이너에 사용되고 있다.

현재 컨테이너를 위한 네트워킹은 제약이 크고 느리다. 
지난 한 해 동안 가상화 머신을 위한 네트워킹은 엄청나게 발전하였다.
그리고 현재 하이퍼바이저는 정기적으로 네트워크 가상화 등의 기술을 포함한 고급 네트워킹 기능을 지원한다.

우리는 진보한 네트워크 가상화를 통해 컨테이너가 더 발전할 것이라고 생각합니다.

도커 컨테이너는 리눅스 환경에 호스팅 됩니다. 
당신은 도커를 지원하는 수많은 리눅스 배포판을 선택할 수 있습니다.
우리는 도커 컨테이너 네트워킹를 두 가지 관점으로 지켜볼겁니다.

- 컨테이너 - 컨테이너, 호스트 안에서 가상 브릿지를 사용
- 컨테이너 - 외부세계 

디폴트로 각 컨테이너의 가상 네트워크 인터페이스는 RFC1918에 정의된 도커가 호스트된 서버에서 사용하지 않는 
프라이빗 아이피 어드레스를 부여받습니다. 

도커 데몬은 docker0이라 불리는 가상 이더넷 브릿지를 생성합니다. 
서버 안에 모든 도커 컨테이너는 이걸 통해 접속을 받습니다.

동일한 서버내에 컨테이너 간 통신은 docker0 브릿지를 통해 이루어집니다. 
브릿지는 트래픽을 모든 인터페이스로 포워드합니다.

새로운 컨테이너가 생성이 될 때 도커는 두 개의 가상 인터페이스를 생성합니다. 
컨테이너와 도커에 의해 서브넷 레인지로부터 할당된 아이피 어드레스로 eth0은 연결됩니다.
디폴트 라우트는 eth0으로 인터페이스 포인트를  docker0 브릿지의 도커 호스트의 아이피 어드레스로 연결됩니다. 

다른 인터페이스는 호스트의 네임스페이스를 사용합니다. docker0 브릿지로 연결됩니다. 도커는 두 개의 인터페이스 사이에 논리 링크를 생성합니다. 

새롭게 생성된 컨테이너는 동일한 호스트 내에서 다른 컨테이너들과 통신을 할 수 있습니다.

도커는 NAT IP방식으로 성사된 외부와의 통신을 하도록 host의 iptable를 수정합니다.   수정한 iptables는 외부 트래픽을 허용하고 Nat컨테이너의 아이피와 docker0의 아이피를 연결한다. 

이 두 가지 도커의 특징을 고려해 IBM SDN NE는 개발되었다. 

- 도커는 NAT 방식을 사용해 컨테이너와 외부와의 통신을 가능케 한다. 
이 방식은 외부에서 직접 접근이 가능한 컨테이너 안에 어떤 서비스의 실행도 방어합니다.

- 다른 호스트의 컨테이너는 동일한 네트워크 안에 존재할 수 있다. 이것은 사용자가 공통적으로 사용되는 네트워크 토폴리지를 생성하는 것을 제한합니다. 

위의 두 가지 제약조건 외에 generic한 가상 네트워크 인프라구조를 제공하는 것이 컨테이너 형태로 쉽게 배포할 수 있는 vm이나 bare matal에 호스팅된 워크로드 측면에서 더 유리합니다. 

도커 컨테이너를 위해 사용한 SDN VE 단순한 네트워킹 솔루션이 다음과 같은 이점을 제공합니다. 

- 동일 서버 내의 다수의 컨테이너를 동일 혹은 다른 가상 네트워크를 묶을 수 있다. 
- 가상 네트워크는 컨테이너화된 모든 서버에 span across 할 수 있다. 
- 다른 서버의 컨테이너들은 동일한 가상 네트워크로 위치시킬 수 있다 .
- 컨테이너는 외부 네느워크에서 접속이 가능하다. 
- 컨테이너는 컨테이너 안에 호스팅 되지 않은 데이터 센터 자원에 접속할 수 있다. 
 
IBM SDN VE는 VXLAN 기반의 가상 머신에 가상 네트워크를 제공할 수 있는 오버레이 솔루션이 목적이다. 
현재 IBM SDN VE는 ESX, KVM 하이퍼바이저를 지원하고 있다. 
IBM은 OpenDayLigt에 컨트리뷰션 하고 있다. OpenDayLight는 SDN 컨트롤러 오픈소스 이다. 

IBM SDN VE 멀티 테넌트 가상 네트워크 솔루션이 제공하는 특징 

- 전체 테이터 센터로 확장이 가능한 가상 네트워크 
- 어떠한 VM도 가상 네트워크로 put할 수 있다. 
- 다른 서버에 호스팅된 VM을 동일한 가상 네트워크로 위치시킬 수 있다 .
- 각 서버다수의 가상 네트워크에 참가시킬 수 있따 .
- VXLAN은 실제 물리 네트워크 iP 어드레스로부터 격리시킨 가상 네트워크 IP를 위해 캡술화 합니다. 
- VM은 물리 계층 2 마이그레이트 할 수 있다. 
- 
• Virtual gateways can be used to facilitate bi-directional communication to external IP networks such as the Internet and bi-directional access to services such as DNS or LDAP in the data center.
• A service chaining infrastructure allows network services such as firewalls and load balancers to be inserted between network tiers.


이 솔루션은 보안 관리와 control plane을 포함합니다. 
우리의 목적은 위의 모든 특징을 클라우드에 딜리버리 할 수 있는 확장과 셀프 프로비져닝과 자동화 운용 모델을 도커 컨테이너에 제공하는 것 입니다. 

IBE SDN VE는 컨테이너와 VM, bare metal 을 위한 가상 네트워크 인프라 구조를 제공할 수 있습니다. 
