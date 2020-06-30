# Lambda

## Hello World!

1. **[서비스]** → **[컴퓨팅]** → [**Lambda]** 을 통해 **Lambda** 콘솔 접속
2. **[함수 생성] → [블루 프린트] → [hello-world-python]** 을 통해 **블루 프린트** 구성
3. **[함수 이름 설정] → [새 역할 생성] → [역할 이름 설정]** 후 **함수 생성** 실행
    - 기존 Lambda 권한이 없기 때문에, 새 역할 생성을 진행한다.

<p align="center">
  <img width="500" height="400" src="https://user-images.githubusercontent.com/52126612/84654658-8ad6e000-af4a-11ea-8cce-643ca1b1c2b3.png">
</p>

4. 함수 생성이 완료되면 함수 **코드 확인 및 수정**이 가능하다.
    - 언어 변경 (python → java)
    - 테스트 케이스 추가 및 결과 확인

<p align="center">
  <img width="800" height="300" src="https://user-images.githubusercontent.com/52126612/84654653-89a5b300-af4a-11ea-872e-044f4a46340d.png">
</p>


5. 여러가지 **설정 기능** 제공
    - 메모리, 제한 시간, VPC 설정, 환경 변수, 태그, 동시성, 비동기식 호출 등 설정 가능
6. **CloudWatch**를 통해 Lambda 함수를 모니터링하고 지표를 보고함
    - CloudWatch에는 사용 중인 모든 AWS 서비스에 대한 지표가 표시되며, 이외에도 사용자 지정 대시보드를 추가로 생성할 수 있으며, 알림을 보내거나 임계값을 초과한 경우 모니터링 중 서버 리소스를 자동으로 변경하는 Alert 생성 가능 (추가 인스턴스 시작에 대한 결정)
