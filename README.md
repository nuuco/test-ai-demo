# WebGPU Adapter Test

Android Chrome 환경에서 WebGPU 지원 상태와 GPU Adapter 정보를 확인하기 위한 간단한 테스트 페이지입니다.

이 저장소는 LASTLY 프로젝트의 로컬 AI 적용 가능성을 검토하기 위해 만들었습니다.  
특히 LiteRT Gemma 3 270M Web Demo 실행 전, Android 기기에서 WebGPU Core Adapter 또는 Compatibility Adapter를 사용할 수 있는지 확인하는 용도입니다.

## 테스트 목적

다음 항목을 확인합니다.

- `navigator.gpu` 지원 여부
- WebGPU Core Adapter 획득 가능 여부
- WebGPU Compatibility Adapter 획득 가능 여부
- `maxBufferSize`
- `maxStorageBufferBindingSize`

Google LiteRT Gemma 3 270M Web Demo는 WebGPU Adapter와 비교적 큰 GPU buffer limit을 요구하므로,
단순히 `chrome://gpu`에서 `WebGPU: Hardware accelerated`가 표시되는 것만으로 실제 모델 실행 가능 여부를 판단하기 어렵습니다.

## 실행 방법

GitHub Pages로 배포한 뒤 Android Chrome에서 접속합니다.

배포 주소:

```text
https://nuuco.github.io/test-ai-demo/
```

GitHub Pages 설정:

```text
Settings
→ Pages
→ Source: Deploy from a branch
→ Branch: main
→ Folder: / (root)
```

루트 디렉터리에 `index.html`이 있어야 합니다.

## 예상 결과

예시:

```text
navigator.gpu: YES

Core adapter: NO
Compatibility adapter: YES

[Compatibility limits]
maxBufferSize: 268435456
maxStorageBufferBindingSize: 134217728
```

### 결과 해석

- `navigator.gpu: NO`
  - 현재 브라우저에서 WebGPU API를 사용할 수 없음

- `Core adapter: YES`
  - 기본 WebGPU Core Adapter 사용 가능

- `Core adapter: NO`
  - 기본 Core Adapter를 얻지 못함

- `Compatibility adapter: YES`
  - WebGPU Compatibility Mode를 통한 Adapter 사용 가능

- `Compatibility adapter: NO`
  - Compatibility Adapter도 얻지 못함

## Gemma 3 270M 테스트와의 관계

LiteRT Gemma 3 270M 공식 Web Demo는 WebGPU를 사용하며,
현재 데모에서는 약 640 MiB 수준의 GPU buffer limit을 요구합니다.

따라서 이 페이지에서 Adapter를 얻을 수 있더라도 다음 조건을 별도로 확인해야 합니다.

```text
maxBufferSize >= 671088640
maxStorageBufferBindingSize >= 671088640
```

Adapter 획득에는 성공하지만 위 한도보다 작은 경우,
Gemma 3 270M 공식 데모의 `requestDevice()` 단계에서 실패할 수 있습니다.

## 테스트 환경 기록 예시

```text
Device:
Galaxy ...

Android:
16

Chrome:
153.x

chrome://gpu:
WebGPU Hardware accelerated

navigator.gpu:
YES

Core adapter:
YES / NO

Compatibility adapter:
YES / NO

maxBufferSize:
...

maxStorageBufferBindingSize:
...
```

## 관련 링크

- LiteRT Gemma 3 270M Web Demo  
  https://google-ai-edge.github.io/litert-samples/samples/tensor_api_playground/demo/gemma3/gemma3_demo.html

- LiteRT 공식 저장소  
  https://github.com/google-ai-edge/litert

- Chrome WebGPU 문서  
  https://developer.chrome.com/docs/web-platform/webgpu/
