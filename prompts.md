# 테스트에 쓴 프롬프트 모음

모든 생성의 공통 설정: FLUX.2-klein 4B GGUF Q4_K_M, 텍스트 인코더 Qwen3-4B Q4_K_M, 4스텝, euler, **seed 42**, 출력 약 0.4MP(포즈 사진 비율), 참조 이미지 0.25MP.
참조 이미지 순서: **이미지 1 = 모델 사진**, **이미지 2 = 포즈 뼈대**(실험 3만 원본 포즈 사진).

## 기본 프롬프트 (BASE)

```
The person from image 1 strikes exactly the same full-body pose as the stick-figure skeleton in image 2. Keep the face, hairstyle and outfit of the person in image 1. Full-body shot, the whole figure visible from head to feet, plain light grey studio background, soft even lighting. Photorealistic fashion photography.
```

## 도구 동작 — 포즈와 결과 짝 (samples/)

| 참조 포즈 | 결과 | 모델 사진 (이미지 1) | 프롬프트 |
|---|---|---|---|
| `pose_01.png` (발레, `docs/pose/fmail-ballet.png`) | `output_01.png` | 남자 `docs/model/mail-model1.png` | BASE |
| `pose_02.png` (댄스, `docs/pose/fmail-dance.png`) | `output_02.png` | 여자 `docs/model/fmail-model1.png` | BASE |

포즈 사진과 성별이 다른 모델을 골라, 옷·얼굴이 아니라 **자세만** 옮겨졌는지 보이게 했습니다.

## 실험 1. 같은 포즈 · 다른 프롬프트 (남자 모델 × pose_01, 기준 output_01)

`HEAD` = `The person from image 1 strikes exactly the same full-body pose as the stick-figure skeleton in image 2.`

| 결과 | 바꾼 칸 | 프롬프트 |
|---|---|---|
| `exp1_stage.png` | 환경·조명 | HEAD + `Keep the face, hairstyle and outfit of the person in image 1. Full-body shot on a theatre stage, deep red velvet curtain behind, warm spotlight from above. Photorealistic stage photography.` |
| `exp1_costume.png` | 옷 | HEAD + `Keep the face and hairstyle of the person in image 1, but dress them in a fitted white ballet costume with white tights and ballet slippers. Full-body shot, plain light grey studio background, soft even lighting. Photorealistic fashion photography.` |
| `exp1_anime.png` | 스타일 | HEAD + `Keep the face, hairstyle and outfit of the person in image 1. Full-body shot, plain cream background. Clean flat 2D anime illustration with bold outlines and soft cel shading.` |

## 실험 2. 같은 프롬프트 · 다른 포즈 (BASE, 2×2)

| | pose_01 발레 | pose_02 댄스 |
|---|---|---|
| 남자 모델 | `output_01.png` | `exp2_male_dance.png` |
| 여자 모델 | `exp2_female_ballet.png` | `output_02.png` |

## 실험 3. 뼈대 vs 원본 포즈 사진 (남자 모델 × pose_01, BASE)

| 결과 | 참조 2 |
|---|---|
| `output_01.png` | DWPose 뼈대 |
| `exp3_photo_ref.png` | 원본 포즈 사진 (`pose_mode="photo"`) |

## 실험 4. cfg 1.0 vs 4.0 (남자 모델 × pose_01, BASE)

| 결과 | cfg |
|---|---|
| `output_01.png` | 1.0 (FLUX.2-klein 4B 증류판 권장값) |
| `exp4_cfg4.png` | 4.0 (교안 노트 값) |

## 실행 기록

2026-11-11, 무료 Colab Tesla T4. 모든 생성은 seed 42, 4스텝, UNet `flux-2-klein-4b-Q4_K_M.gguf`, 텍스트 인코더 `Qwen3-4B-Q4_K_M.gguf` 입니다.
노트 [10] 셀이 출력한 표 그대로입니다(원본: `samples/experiment_log.md`, 각 결과의 설정은 `samples/*.json`).

| 파일 | 모델 | 포즈 | 참조2 | cfg | 크기 | 시간(초) | 메모 |
|---|---|---|---|---|---|---|---|
| output_01 | mail-model1.png | fmail-ballet.png | skeleton | 1.0 | 592x688 | 50.9 | 도구 동작: mail-model1.png × fmail-ballet.png |
| output_02 | fmail-model1.png | fmail-dance.png | skeleton | 1.0 | 656x608 | 10.5 | 도구 동작: fmail-model1.png × fmail-dance.png |
| exp1_stage | mail-model1.png | fmail-ballet.png | skeleton | 1.0 | 592x688 | 12.1 | 실험1 환경·조명: 무대 |
| exp1_costume | mail-model1.png | fmail-ballet.png | skeleton | 1.0 | 592x688 | 12.2 | 실험1 옷: 발레복 |
| exp1_anime | mail-model1.png | fmail-ballet.png | skeleton | 1.0 | 592x688 | 13.5 | 실험1 스타일: 일러스트 |
| exp2_female_ballet | fmail-model1.png | fmail-ballet.png | skeleton | 1.0 | 592x688 | 13.5 | 실험2: 여자 모델 × 발레 |
| exp2_male_dance | mail-model1.png | fmail-dance.png | skeleton | 1.0 | 656x608 | 12.0 | 실험2: 남자 모델 × 댄스 |
| exp3_photo_ref | mail-model1.png | fmail-ballet.png | photo | 1.0 | 592x688 | 12.1 | 실험3: 참조 2 = 원본 포즈 사진 |
| exp4_cfg4 | mail-model1.png | fmail-ballet.png | skeleton | 4.0 | 592x688 | 21.1 | 실험4: cfg 4.0 (교안 노트 값) |

- output_01 이 50.9초인 것은 첫 생성이라 모델을 GPU 에 올리는 시간이 포함됐기 때문입니다.
- exp4 가 약 2배 걸린 것은 cfg 가 1이 아니면 스텝마다 조건 있는 예측과 빈 조건 예측을 둘 다 계산하기 때문으로 보입니다(추측).
- 그 밖에 뼈대 추출은 발레 12.0초(DWPose 모델 첫 다운로드 포함), 댄스 3.0초가 걸렸습니다.
