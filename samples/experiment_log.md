## 실험 기록

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

## 프롬프트

**output_01** (seed 42)

```
The person from image 1 strikes exactly the same full-body pose as the stick-figure skeleton in image 2. Keep the face, hairstyle and outfit of the person in image 1. Full-body shot, the whole figure visible from head to feet, plain light grey studio background, soft even lighting. Photorealistic fashion photography.
```

**output_02** (seed 42)

```
The person from image 1 strikes exactly the same full-body pose as the stick-figure skeleton in image 2. Keep the face, hairstyle and outfit of the person in image 1. Full-body shot, the whole figure visible from head to feet, plain light grey studio background, soft even lighting. Photorealistic fashion photography.
```

**exp1_stage** (seed 42)

```
The person from image 1 strikes exactly the same full-body pose as the stick-figure skeleton in image 2. Keep the face, hairstyle and outfit of the person in image 1. Full-body shot on a theatre stage, deep red velvet curtain behind, warm spotlight from above. Photorealistic stage photography.
```

**exp1_costume** (seed 42)

```
The person from image 1 strikes exactly the same full-body pose as the stick-figure skeleton in image 2. Keep the face and hairstyle of the person in image 1, but dress them in a fitted white ballet costume with white tights and ballet slippers. Full-body shot, plain light grey studio background, soft even lighting. Photorealistic fashion photography.
```

**exp1_anime** (seed 42)

```
The person from image 1 strikes exactly the same full-body pose as the stick-figure skeleton in image 2. Keep the face, hairstyle and outfit of the person in image 1. Full-body shot, plain cream background. Clean flat 2D anime illustration with bold outlines and soft cel shading.
```

**exp2_female_ballet** (seed 42)

```
The person from image 1 strikes exactly the same full-body pose as the stick-figure skeleton in image 2. Keep the face, hairstyle and outfit of the person in image 1. Full-body shot, the whole figure visible from head to feet, plain light grey studio background, soft even lighting. Photorealistic fashion photography.
```

**exp2_male_dance** (seed 42)

```
The person from image 1 strikes exactly the same full-body pose as the stick-figure skeleton in image 2. Keep the face, hairstyle and outfit of the person in image 1. Full-body shot, the whole figure visible from head to feet, plain light grey studio background, soft even lighting. Photorealistic fashion photography.
```

**exp3_photo_ref** (seed 42)

```
The person from image 1 strikes exactly the same full-body pose as the stick-figure skeleton in image 2. Keep the face, hairstyle and outfit of the person in image 1. Full-body shot, the whole figure visible from head to feet, plain light grey studio background, soft even lighting. Photorealistic fashion photography.
```

**exp4_cfg4** (seed 42)

```
The person from image 1 strikes exactly the same full-body pose as the stick-figure skeleton in image 2. Keep the face, hairstyle and outfit of the person in image 1. Full-body shot, the whole figure visible from head to feet, plain light grey studio background, soft even lighting. Photorealistic fashion photography.
```
