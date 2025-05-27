# AWS S3 & CloudFront 배포 프로젝트

Next.js 애플리케이션을 AWS S3와 CloudFront를 이용하여 배포한 프로젝트입니다.

## 배포 링크

[사이트 바로가기](https://d3tyirxyz3i1et.cloudfront.net/)

## 프로젝트 소개

이 프로젝트는 Next.js 15 애플리케이션을 AWS S3에 정적 호스팅으로 배포하고, CloudFront를 통해 글로벌 CDN 서비스를 제공합니다. GitHub Actions를 통한 CI/CD 파이프라인이 구성되어 있어 main 브랜치에 변경사항이 push될 때마다 자동으로 배포됩니다.

## 기술 스택

- **프론트엔드**

  - Next.js 15
  - React 19
  - Tailwind CSS 4
  - TypeScript
  - Recharts

- **배포 및 인프라**
  - AWS S3 (정적 웹 호스팅)
  - AWS CloudFront (CDN)
  - GitHub Actions (CI/CD)

## 배포 아키텍처

```
사용자 요청 → CloudFront CDN → S3 버킷(정적 파일)
```

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

## 수동 배포 방법

수동으로 배포하려면 다음 명령어를 사용합니다:

```bash
# 빌드
pnpm build

# AWS CLI가 설치되어 있고 자격 증명이 구성되어 있어야 합니다
aws s3 sync out/ s3://[버킷이름] --delete
aws cloudfront create-invalidation --distribution-id [배포ID] --paths "/*"
```

## AWS 리소스 설정

이 프로젝트를 위해 다음 AWS 리소스가 필요합니다:

1. **S3 버킷**: 정적 웹 호스팅이 활성화된 버킷
2. **CloudFront 배포**: S3 버킷을 오리진으로 설정
3. **IAM 사용자**: S3 업로드 및 CloudFront 무효화 권한이 있는 사용자

## 주의사항

- AWS 리소스 사용에 따른 비용이 발생할 수 있습니다.
- GitHub Actions 시크릿에 AWS 자격 증명을 안전하게 저장해야 합니다.
