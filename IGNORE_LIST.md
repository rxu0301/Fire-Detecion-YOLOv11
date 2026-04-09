# GitHub에 올리면 안 되는 파일/폴더 목록

## 🚨 필수 삭제 항목

### 1. `.git` 폴더 (로컬 Repository 메타데이터)
- **경로:** `.git/`
- **크기:** ~200-300MB
- **이유:** 로컬 git 저장소 메타데이터로, 각 로컬 환경마다 다름. GitHub에 올리면 저장소가 아주 커짐
- **삭제 명령:**
  ```powershell
  Remove-Item -Path ".git" -Recurse -Force
  ```

---

## 🔴 권장 삭제 항목

### 2. 모델 가중치 파일 (`.pt` 파일)
- **패턴:** `**/weights/*.pt`
- **총 크기:** 약 300-400MB (모든 best.pt, last.pt 파일)
- **파일 목록:**

#### run_test01 폴더
- `run_test01/Base/weights/best.pt` (19.2 MB)
- `run_test01/Base/weights/last.pt` (19.2 MB)
- `run_test01/augmentation/weights/best.pt` (19.2 MB)
- `run_test01/augmentation/weights/last.pt` (19.2 MB)
- `run_test01/regularization/weights/best.pt` (19.2 MB)
- `run_test01/regularization/weights/last.pt` (19.2 MB)

#### run_test02 폴더
- `run_test02/augmented_freeze/weights/best.pt` (40.5 MB)
- `run_test02/augmented_freeze/weights/last.pt` (40.5 MB)
- `run_test02/augmented_v2/weights/best.pt` (19.2 MB)
- `run_test02/augmented_v2/weights/last.pt` (19.2 MB)

#### run_test03 폴더
- `run_test03/reg_strong_mixup_m/weights/best.pt` (40.5 MB)
- `run_test03/reg_strong_mixup_m/weights/last.pt` (40.5 MB)
- `run_test03/reg_strong_mixup_n/weights/best.pt` (5.5 MB)
- `run_test03/reg_strong_mixup_n/weights/last.pt` (5.5 MB)
- `run_test03/reg_strong_mixup_s/weights/best.pt` (19.2 MB)
- `run_test03/reg_strong_mixup_s/weights/last.pt` (19.2 MB)

**이유:** 
- 모든 `.pt` 파일은 PyTorch 모델 가중치 (이진 파일)
- 각각 수십 MB 크기로 매우 큼
- GitHub의 무료 저장소는 일반적으로 100MB 이상의 파일 업로드 불가
- 모드 가중치는 학습 스크립트로 재생성 가능하므로 업로드할 필요 없음
- `best.pt` = 최고 성능 모델, `last.pt` = 마지막 에포크 모델

**권장 대체 방안:**
- 모델은 별도 클라우드 저장소(Google Drive, Hugging Face Model Hub, AWS S3 등)에 저장
- README에 모델 다운로드 링크 추가

**삭제 명령:**
```powershell
Get-ChildItem -Path "run_test*" -Recurse -Include "*.pt" | Remove-Item -Force
```

---

## ✅ 안전하게 업로드 가능한 항목

### 3. 유지해야 할 파일/폴더
- ✅ `README.md` - 프로젝트 문서
- ✅ `main.ipynb` - 실험 노트북 (1MB, 괜찮음)
- ✅ `args.yaml` - 설정 파일들
- ✅ `results.csv` - 실험 결과 CSV
- ✅ 이미지 파일들 (`.jpg`, `.png`) - 시각화/결과 이미지 (업로드 가능 크기)

---

## 📋 추천 .gitignore 설정

프로젝트에 `.gitignore` 파일을 생성하면 자동으로 이런 파일들이 무시됩니다:

```
# Model weights
*.pt

# Python
__pycache__/
*.pyc
.Python
env/
venv/
pip-log.txt

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# 대용량 데이터
*.zip
*.tar.gz
data/
datasets/
```

---

## 📊 정리 후 예상 크기

| 항목 | 삭제 전 | 삭제 후 | 감소량 |
|------|--------|--------|--------|
| 총 크기 | ~600MB | ~200MB | 67% ↓ |
| .git 폴더 | ~200MB | 0 | 100% |
| .pt 파일 | ~400MB | 0 | 100% |

---

## 🔧 정리 실행 순서

1. `.git` 폴더 삭제
   ```powershell
   Remove-Item -Path ".\.git" -Recurse -Force
   ```

2. 모든 `.pt` 파일 삭제
   ```powershell
   Get-ChildItem -Path ".\run_test*" -Recurse -Include "*.pt" | Remove-Item -Force
   ```

3. `.gitignore` 파일 생성 (선택사항)

4. 깔끔하게 GitHub에 재 업로드

---

**마지막 확인:** 업로드 전 용량 확인
```powershell
(Get-ChildItem -Path "." -Recurse | Measure-Object -Property Length -Sum).Sum / 1MB
```
