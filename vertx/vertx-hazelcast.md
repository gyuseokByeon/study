http://yakolla.tistory.com/95

http://vertx.io/docs/vertx-hazelcast/java/

https://github.com/hazelcast/hazelcast

Vertx는 cluster manager의 디폴트 구현체로 hazelcast를 사용합니다.
Vertx clustermanager는 pluggable 구조이기 때문에 다른 cluster manager 구현체를 사용할 수 있습니다.

Vertx cluster manager는 이런 기능을 지원합니다. 
- Discovery : 분산 cluster 내에서 새롭게 조인할 서버 어플리케이션 상태 체크나 아웃시킬 서버 어플리케이션의 상태 체크등 
- subscriber list 관리 (브로드캐스트, 멀티캐스트, 유니캐스트)
- 분산 맵 지원
- 분산 락
- 분산 카운터 

클러스터 매니져는 노드 간의 전송을 이벤트 버스로 핸들링하진 않는다. 노드 간 전송은 TCP 커넥션으로 이루어진다.

커맨드 라인에 실행할거면 Vertx 인스톨 폴더에 vertx-hazelast jar파일이 있어야 한다. 
메이븐이나 그래들을 사용하면 프로젝트에 의존성을 선언해라 

헤이즐캐스트 라이브러리가 클래스 패스에 위치하면 Vertx는 클러스터 매니져로 헤이즐캐스트를 자동으로 사용한다. -> 소스 내 확인

다른 클래스 매니져가 클래스패스에 올라가지 않도록 확인해라

또한 Vertx를 생성할 때 구체화된 옵션을 통해서 클러스터 매니져를 구체화할 수 있다. 

설정 파일 넘기는 방법
default-cluster.xml 파일이 jar안에 패키지 되어있다. -> 소스 확인

설정을 오버라이드 하고 싶다면 cluster.xml 설정파일을 생성해 클래스패스에 넣어주면 된다. 
임베디드 하고 싶으면 jar 안에 root에 넣어놔라
cluster.xml이 현재 디렉터리 에 있다면, conf 디렉터리에 있다면 
vertx.hazelcast.config 환경변수를 이용하는 방법
vertx.hazelcast.config가 설정되어 있으면 설정은 오버라이드 되고 만약 이 설정으로 로딩에 실패하면 폴백으로 다른 cluster.xml을 찾거나 디폴트 컨피그레이션 설정정보를 로딩한다.

-Dhazelcast.config는 헤이즐캐스트 변수이므로 vertx에서는 지원하지 않는다.

헤이즐캐스트는 디폴트로 멀티캐스트와 TCP를 포함해 트랜스포트 합니다. 

Using an existing Hazelcast cluster
클러스터 매니져내에 기존 HazelcastInstance가 있으면 재사용할 수 있다. -> 재사용한다는게 무슨 말이지 ? 기존 클러스터에 조인한다는 말인가 옵션만 재사용한단 말인가, HazelcastInstance는 헤이즐캐스트내 객체인듯
이 경우는 vertx cluster의 오너가 아니다. 
커스텀 헤이즐캐스트를 사용하려면 이런 옵셔이 필요하다. -> 설정값 의미 

클러스ㅓ 내에서 ha를 사용할 때 헤이즐캐스트 클라이언트 사용하지 말아라. 그 클라이언트는 클러스터를 떠나거나 데이터를 유실하거나 불안전한 상태에서 클러스터를 떠나도 노티를 주지 않는다.
https://github.com/vert-x3/vertx-hazelcast/issues/24 

(스마트 클라이언트 클러스터 매니져를 사용할 때 버텍스 인스턴스가 죽거나 연결이 해제되면 인스턴스의 이벤트버스 등록은 제거되지 않습니다.
멤버를 제거하는 콜이 호출되지 않고 메세지는 죽은 어드레스로 전송하게 됩니다. 3.6 브랜치에서 패치됨 )
