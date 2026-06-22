# 훈련 중 비디오 클립 녹화

Isaac Lab은
[gymnasium.wrappers.RecordVideo](https://gymnasium.farama.org/main/_modules/gymnasium/wrappers/record_video/) 클래스를 사용하여 훈련 중 비디오 클립을 녹화하는 기능을 지원합니다.

이 기능은 `ffmpeg`를 설치하고 훈련 스크립트에서 다음 명령줄 인수를 사용함으로써 활성화할 수 있습니다:

* `--video`: 훈련 중 비디오 녹화 활성화
* `--video_length`: 각 녹화된 비디오의 길이(단계 단위)
* `--video_interval`: 비디오 녹화 간격(단계 단위)

헤드리스 모드로 실행할 때는 `--enable_cameras` 인수도 반드시 추가해야 합니다.
녹화를 활성화하는 것은 훈련 중 렌더링을 활성화하는 것과 동일하므로, 시작 및 런타임 성능이 모두 저하될 수 있음을 유의하세요.

예제 사용법:

```shell
python scripts/reinforcement_learning/rl_games/train.py --task=Isaac-Cartpole-v0 --headless --video --video_length 100 --video_interval 500
```

녹화된 비디오는 훈련 체크포인트와 동일한 디렉터리에 저장되며,
`IsaacLab/logs/<rl_workflow>/<task>/<run>/videos/train` 경로 아래에 위치합니다.
