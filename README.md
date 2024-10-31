# Week3_Basic
# Unity Input System과 CharacterManager에 대한 학습 내용

## 1. 입력 방식의 차이점 및 공통점
Unity의 다양한 입력 방식 중 `SendMessage`와 `Input System`을 비교하면 다음과 같습니다.

- **차이점**:
  - `SendMessage`: 
    - 컴포넌트와 메서드의 갯수가 많아질수록 속도가 느려집니다.
    - 문자열을 통한 접근이므로 오타가 발생할 경우 오류가 발생하기 쉽고, 이는 코드 작성과 디버깅 시 불편함을 야기할 수 있습니다.
  - `Input System`:
    - `Invoke Unity Event` 형태로 이벤트 기반 구조를 사용하여 관리가 편리하며, 인스펙터 창에서 쉽게 등록 가능합니다.
    - 유니티 자체에 포함된 다양한 기능을 활용할 수 있어 좀 더 효율적입니다.

- **공통점**:
  - 입력 방식은 다르지만 최종 결과물은 유사하게 구현됩니다.

## 2. `CharacterManager`와 `Player`의 역할
- **`CharacterManager`**:
  - 싱글톤(Singleton)으로 설계되어 게임 내에서 하나만 존재하도록 설정하여, 모든 스크립트에서 접근이 가능합니다.
  - 이를 통해 플레이어와 관련된 정보 및 상태를 통합적으로 관리하고 다른 스크립트에 전달하는 역할을 수행합니다.

- **`Player`**:
  - `CharacterManager`의 인스턴스에 플레이어 데이터를 넣어, `CharacterManager`에서 플레이어 관련 정보를 가져올 수 있도록 합니다.
  - `Player.cs`는 플레이어의 세부 정보를 직접 관리하며, `CharacterManager`는 이를 불러와 사용합니다.

## 3. 핵심 로직 분석 (`Move`, `CameraLook`, `IsGrounded`)
- **Move**
  - `Vector3 dir`에 이동 방향을 설정하여 전후좌우 이동이 가능하도록 합니다. `transform.forward`와 `transform.right`를 사용하여 이동 방향을 설정하고, `moveSpeed`로 이동 속도를 조정합니다.
  - `dir.y`는 리지드바디의 `velocity.y` 값을 유지하여 점프 시 위아래 움직임을 제어합니다.
  - 최종적으로 `rigidbody.velocity`에 `dir` 값을 설정하여 이동이 구현됩니다.

- **CameraLook**
  - `mouseDelta.y`를 기반으로 Y축 회전을 계산하여 카메라의 좌우 회전이 가능하도록 합니다.
  - `clamp`를 통해 회전 각도를 제한하고, 마우스 이동과 반대로 카메라가 회전하도록 `-camCurXRot` 값을 사용합니다.
  - 로컬 좌표를 기준으로 회전시켜 자연스러운 시야 전환을 구현합니다.

- **IsGrounded**
  - 플레이어가 땅에 닿았는지 확인하기 위해 4방향(앞, 뒤, 좌, 우)으로 `Ray`를 발사합니다.
  - 레이어 마스크를 설정하여 땅과의 충돌만 감지하도록 제한하며, `Ray`는 일정한 길이만큼 발사됩니다.
  - `Physics.RayCast` 결과에 따라 `true` 또는 `false`를 반환하여, 연속 점프를 방지하는 로직에 사용합니다.

## 4. `Move`와 `CameraLook` 함수 호출 방식 (`FixedUpdate`, `LateUpdate`)
- **Move (FixedUpdate)**
  - 리지드바디와 같은 물리 연산이 필요한 경우, `FixedUpdate`를 통해 호출하는 것이 권장됩니다. 이는 불규칙하게 호출되는 `Update` 대신, 물리 엔진이 일정한 간격으로 계산할 수 있도록 보장해 주기 때문입니다.
  
- **CameraLook (LateUpdate)**
  - `LateUpdate`는 모든 `Update` 함수가 호출된 후에 실행되므로, 카메라가 따라가야 할 대상이 움직인 후에 카메라의 위치나 각도를 조정하는 데 적합합니다. 이를 통해 좀 더 부드러운 카메라 움직임을 구현할 수 있습니다.
