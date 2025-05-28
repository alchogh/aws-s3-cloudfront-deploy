# AWS S3 & CloudFront 배포 프로젝트

Next.js 애플리케이션을 AWS S3와 CloudFront를 이용하여 배포한 프로젝트입니다.

## 배포 링크

[S3 + CloudFront사이트 바로가기](https://d3tyirxyz3i1et.cloudfront.net/)
[S3사이트 바로가기](http://hanghae-front-5th.s3-website-ap-southeast-2.amazonaws.com/)

## 프로젝트 소개

이 프로젝트는 Next.js 15 애플리케이션을 AWS S3에 정적 호스팅으로 배포하고, CloudFront를 통해 글로벌 CDN 서비스를 제공합니다. GitHub Actions를 통한 CI/CD 파이프라인이 구성되어 있어 main 브랜치에 변경사항이 push될 때마다 자동으로 배포됩니다.

## 기술 스택

- **프론트엔드**

  - Next.js 15

- **배포 및 인프라**
  - AWS S3 (정적 웹 호스팅)
  - AWS CloudFront (CDN)
  - GitHub Actions (CI/CD)

## 배포 아키텍처

![image](https://github.com/user-attachments/assets/50a3f0f5-83f5-4194-a1f4-a2abbf4b3d25)



## 왜 CloudFront를 함께 사용해야 할까?

<img width="1682" height="50" alt="스크린샷 2025-05-27 오후 2 55 44" src="https://github.com/user-attachments/assets/468f3822-21da-4c66-b2f6-31712c918f61" />

위 이미지는 동일한 리소스를 각각 S3 단독 배포와 CloudFront + S3를 통해 요청한 스크린 샷이다.
CloudFront를 사용하는 경우 응답 시간이 약 28ms, S3 단독 요청은 약 313ms, 약 7배 차이가 난 것을 알 수 있다.

이러한 차이는 CloudFront가 **글로벌 CDN(Content Delivery Network)** 역할을 하기 때문이다.
CloudFront는 전 세계 여러 지역에 위치한 **엣지 로케이션(Edge Location)** 을 통해 사용자와 가까운 서버에서 콘텐츠를 제공하므로,  
매번 AWS S3의 원본 버킷까지 네트워크 요청을 전달할 필요가 없다.

> CloudFront를 통해 정적 리소스를 로딩할 경우,
> 브라우저는 이전에 수신한 리소스에 대해 `ETAG` 또는 `Last-Modified`값을 포함한 조건부 요청을 전송한다.
> 이 요청을 기반으로 캐시된 리소스와 비교한 후, 변경 사항이 없으면 304응답을 반환한다.

### 왜 CloudFront로 배포된 네트워크를 보면 응답헤더에 `Content-Encoding`가 보이지 않을까?

<img width="720" alt="스크린샷 2025-05-27 오후 3 18 31" src="https://github.com/user-attachments/assets/64ce6fc8-5987-4249-9eee-059b3d1d3f4c" />

앞서 말한대로 304는 캐시된 리소스가 여전히 유효하다는 뜻이다. 그래서 status code가 304가 뜨면 실제 바디가 전달되지 않고, 헤더도 일부 생략되기 때문에 `Content-Encoding` 도 생략될 수 있다.

그래서 캐시 사용중지를 누르고 확인을 하면 Content-Encoding이 정상 출력되면서 압축이 된 것을 알 수 있다.

<img width="824" alt="스크린샷 2025-05-27 오후 3 54 14" src="https://github.com/user-attachments/assets/adb11fc5-4ec4-4d2c-ba02-aa5abecebadd" />

## 로컬 개발 환경 설정

```bash
# 의존성 설치
pnpm install

# 개발 서버 실행
pnpm dev

# 빌드
pnpm build
```

## 배포 프로세스

이 프로젝트는 GitHub Actions를 사용하여 자동 배포됩니다:

1. `main` 브랜치에 코드가 push되면 GitHub Actions 워크플로우가 트리거됩니다.
2. 워크플로우는 다음 단계를 실행합니다:
   - 코드 체크아웃
   - 의존성 설치
   - Next.js 프로젝트 빌드
   - AWS 자격 증명 구성
   - 빌드된 파일을 S3 버킷에 업로드
   - CloudFront 캐시 무효화

## AWS 리소스 설정

이 프로젝트를 위해 다음 AWS 리소스가 필요합니다:

1. **S3 버킷**: 정적 웹 호스팅이 활성화된 버킷
2. **CloudFront 배포**: S3 버킷을 오리진으로 설정
3. **IAM 사용자**: S3 업로드 및 CloudFront 무효화 권한이 있는 사용자
