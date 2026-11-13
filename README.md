# ROS 2 Manipulator Learning

ROS 2 Jazzy · MoveIt · Gazebo 로봇팔 실습 및 문제 해결 기록

Windows의 WSL Ubuntu에서 로봇팔 시뮬레이션 환경을 구성하고, MoveIt을 통한 동작 계획과 제어를 학습합니다. 강의 기반 실습에서 직접 수행한 환경 구성, 오류 진단, 검증 결과를 기록합니다.

## 실행 결과

**2026-11-14: Gazebo Sim에서 `omy_f3m` 로봇팔 모델 표시 성공.**

![Gazebo 로봇팔 실행 결과](images/gazebo-manipulator.png)

현재 확인한 범위는 Gazebo 창 실행과 로봇 모델 표시입니다. MoveIt 경로 계획 및 관절 제어는 다음 실습에서 확인할 예정입니다.

## 진행 상황

- [x] WSL Ubuntu에서 워크스페이스 생성
- [x] ROBOTIS 원본 저장소 다운로드
- [x] ROS 2 Jazzy 환경 설정
- [x] 기본 Gazebo 예제 실행
- [x] Gazebo에서 로봇팔 모델 표시
- [ ] 원본 커밋, 정확한 버전과 launch 명령 기록
- [ ] MoveIt 경로 계획 및 관절 제어
- [ ] 동작 영상과 직접 수정한 코드 추가

## 환경

| 항목 | 사용 환경 |
| --- | --- |
| 호스트 | Windows |
| Linux | WSL Ubuntu (정확한 버전 추후 기록) |
| ROS | ROS 2 Jazzy |
| 시뮬레이터 | Gazebo Sim (정확한 버전 추후 기록) |
| 원본 패키지 | ROBOTIS open_manipulator |
| 표시 모델 | omy_f3m |
| 워크스페이스 | `~/moveit_ws` |
| 화면 표시 설정 | `LIBGL_ALWAYS_SOFTWARE=1` |

## 주요 명령

실습 중 사용한 명령을 정리한 것으로, 전체 설치를 보장하는 스크립트는 아닙니다. Ubuntu 터미널에서 실행하며 프롬프트의 `~$`는 입력하지 않습니다.

### 소스 준비

```bash
mkdir -p ~/moveit_ws/src
cd ~/moveit_ws/src
git clone https://github.com/ROBOTIS-GIT/open_manipulator.git
```

### ROS 환경과 의존성

```bash
source /opt/ros/jazzy/setup.bash
echo $ROS_DISTRO
cd ~/moveit_ws
rosdep install --from-paths src --ignore-src -r -y
```

`echo $ROS_DISTRO` 결과가 `jazzy`인지 확인합니다. rosdep의 마지막 성공 문구만 보지 않고 위쪽 오류와 설치 실패 목록도 확인합니다.

### 기본 Gazebo 실행

```bash
source /opt/ros/jazzy/setup.bash
LIBGL_ALWAYS_SOFTWARE=1 gz sim -v 4 shapes.sdf
```

### 기존 빌드 결과 사용

워크스페이스에 `install/local_setup.bash`가 생성되어 있는 경우:

```bash
cd ~/moveit_ws
source /opt/ros/jazzy/setup.bash
source install/local_setup.bash
export LIBGL_ALWAYS_SOFTWARE=1
```

이후 실습에 사용한 로봇 launch 명령을 실행합니다. 정확한 launch 명령과 빌드 옵션은 확인 후 추가합니다.

## 문제 해결

| 증상 | 확인 및 조치 | 결과 |
| --- | --- | --- |
| Anaconda Prompt에서 `mkdir -p` 오류 | Ubuntu 터미널로 전환하고 프롬프트 문자 제외 | 워크스페이스 생성 |
| Git clone에서 사용자 이름 요청 | 주소의 `ROBOTICS-GIT`를 `ROBOTIS-GIT`로 수정 | 다운로드 완료 |
| `ROS distro is not set` | `source /opt/ros/jazzy/setup.bash` 실행 | Jazzy 인식 |
| 여러 apt 의존성 설치 실패 | apt 목록 갱신 및 개별 설치 오류 확인을 통해 진단 | 전체 의존성 설치 결과는 별도 재확인 필요 |
| `source_install/local_setup.bash` 파일 없음 | `source install/local_setup.bash`로 공백 수정 | 문법 수정 |
| `WARN:COPY MODE`, 창이 보이지 않음 | WSL 재시작 후 ROS 환경 재적용 및 소프트웨어 렌더링 실행 | 기본 창과 로봇팔 표시 성공 |
| 새 터미널에서 `gz: command not found` | ROS Jazzy 환경 다시 불러오기 | 실행 성공 |
| `realsense2_description/meshes/d405.stl` 경로 오류 | 카메라 패키지 및 자원 경로 확인 대상 | 로봇팔 표시 성공. 해당 로그 소멸 여부는 미확인 |

여러 조치를 순차 적용한 뒤 창이 열렸으므로 GUI 문제의 단일 원인을 확정하지 않았습니다. 소프트웨어 렌더링은 성능이 낮아질 수 있습니다.

## 배운 개념

- **description 패키지**: 로봇의 구조와 형상을 위한 URDF/Xacro, 메시 등의 자원.
- **URDF**: 링크와 관절, 형상 및 물리 정보로 로봇 구조를 설명하는 XML 형식.
- **source**: 현재 터미널에 환경 설정을 적용하는 명령. ROS 설치 환경과 작업 공간의 빌드 환경을 구분합니다.
- **rosdep**: 소스 패키지가 요구하는 외부 의존성을 확인하고 설치하는 도구.
- **Gazebo / MoveIt**: 시뮬레이션 환경과 동작 계획 도구의 역할을 구분해 학습합니다.

## 출처와 기록 범위

개인 학습 기록이며 원본 로봇 코드를 직접 개발한 것으로 주장하지 않습니다. 직접 수행한 환경 구성과 문제 해결 과정을 기록하며, 강의 자료와 원본 소스는 복제하지 않았습니다. 향후 직접 수정한 코드를 올릴 때는 원본 커밋과 변경 내용, 라이선스를 함께 기록합니다.

- [ROBOTIS 원본 저장소](https://github.com/ROBOTIS-GIT/open_manipulator)
- [ROS 2 Jazzy 문서](https://docs.ros.org/en/jazzy/)
- [Gazebo 문서](https://gazebosim.org/docs/)
