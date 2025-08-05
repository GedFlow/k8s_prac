# 쿠버네티스 미니 프로젝트
### Skills
- Docker version 28.3.0, build 38b7060
  - 도커 이미지 베이스 : python version = 3.9.21-slim
- kubernetes 1.31
    - minikube 사용함
    - kustomization 사용

## ToDo
```
[완]- web자체 이미지 제작
[완]- ingress 리소스 생성
[완]- 자체 제작한 이미지로 deploment 생성 (flask와 DB)
[완]- ingress - flask(deploy) - DB(deploy) 구성\
[완]- ingress TLS 터미네이션 설정
[완]- flask 디플로이먼트 HPA 구성 추가
[완]- flask <-> DB 연동
[완]- flask configmap으로 구성 설정
[완]- DB configmap으로 구성 설정
[완]- DB PV/PVC 구성 추가
[완]- Helm 배포
- DB statefulSet으로 이중화 (시간 남으면 할 것)
```

## 사용설명서
```bash
# helm chart 실행
$ helm install <별칭> ptj-miniproject/flaskDiary

# helm chart 제거
$ helm uninstall <별칭>

# helm 커스터마이징
$ helm install <별칭> ptj-miniproject/flaskDiary -f <커스텀.yaml>

# kustomize 사용시
kubectl apply -k ./project
```
위 명령어를 사용하여 즉시 Ingress - web(flask) - DB 세팅을 구성할 수 있다.
이 경우 DB가 hostPath를 사용하는 볼륨을 사용한다.

> DEFAULT VALUES 를 살펴보고 커스텀.yaml을 생성하여  볼륨종류(hostPath, nfs), nfs서버 주소, 도메인네임 등을 지정할 수 있다.

> nfs 볼륨을 사용하려면 먼저 control plane에 nfs-server를 설치 및 실행하고, nfs 경로를 생성해 주어야 한다.

