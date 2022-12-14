# API

- **Application Programming Interface**
- **둘 이상의 컴퓨터 프로그램이 서로 통신하는 방법이자 컴퓨터 사이에 있는 중계 계층**
- Web app in browser ↔ Internet ↔ API ↔ Web Server ↔ Database
- **장점**
    - 제공자는 서비스의 중요한 부분을 드러내지 않아도 됨
        - DB 설계 구조, 드러내고 싶지 않은 데이터베이스의 테이블 정보, 서버의 상수값 등
    - 사용자는 해당 서비스가 어떻게 구현되는지 알 필요 없이 필요한 정보만 받을 수 있음
    - OPEN API의 경우 앱 개발 프로세스를 단순화시키고 시간과 비용을 절약할 수 있음 (ex. OAuth, 지도..)
    - 제공자의 경우 API를 만들게 되면 내부 프로세스가 수정되었을 때 매번 수정하는 것이 아니라 API가 수정이 안되게끔 만들 수 있음
        - 서비스 사용자는 서비스의 잦은 업데이트를 선호하지 않음
        - DB가 변경이 되더라도 API라는 중간 계층이 있기 때문에 업데이트를 제어할 수 있음
        - 사용자는 내부적으로 수정이 일어나더라도 영향을 받지 않게 됨
    - 제공자는 데이터를 한 곳에 모을 수 있음
        - 그냥 여러 페이지를 통해 받은 데이터를 하나의 DB에 저장할 수 있다는 의미인 듯
    - 파파고API와 같이 OPEN API를 개발한 경우, 다른 서비스 제공자가 이를 사용함으로써 데이터를 수집할 수도 있고 서비스 홍보도 가능함 → 확장 가능
- **API 종류**
    - private : 내부적으로 사용하는 경우, 해시키가 있어야 API통신이 가능하게 함
    - public : 모든 사람이 사용할 수 있음. 많은 트래픽을 방지하기 위해 하루 요청수의 제한, 계정당 몇 개 등 제한을 둠
