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
- Helm 배포
- DB statefulSet으로 이중화 (시간 남으면 할 것)
```

## 사용설명서
```
kubectl apply -k ./project
```
위 명령어를 사용하여 즉시 Ingress - web(flask) - DB 세팅을 구성할 수 있다.
이 경우 DB가 hostPath를 사용하는 볼륨을 사용한다.

```yaml
# db-deploy.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deploy
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
        ports:
        - containerPort: 3306
        envFrom:
        - secretRef:
            name: mysql-secret
        - configMapRef:
            name: mysql-configmap
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-storage
        persistentVolumeClaim:
          claimName: mysql-pvc-hp # hostPath 사용 설정
          #claimName: mysql-pvc-nfs # nfs 사용 설정
```
위 코드는 **db-deploy.yml** 파일 내용이다.
맨 아래 주석처리 된 부분을 설명대로 mysql-pvc-hp를 mysql-pvc-nfs 로 교체하여 nfs 볼륨을 사용할 수 있다. 
nfs 볼륨을 사용하기 전에 서버에 nfs-server를 설치하고, /srv/nfs-volume 경로를 생성해 주어야 한다.

> 본 프로젝트는 minikube 환경에서 작업하였기 때문에 DB를 hostPath방식으로 연결할 수 있었다.
> 하지만 멀티노드 환경에서 본 프로젝트를 구동하고싶다면, nfs 방식으로 바꿔야 제대로 실행될 확률이 높다.
