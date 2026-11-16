# Grafana 대시보드

Grafana sidecar가 `grafana_dashboard: "1"` 라벨이 붙은 ConfigMap을 감시합니다. 저장소 루트에서 아래 명령을 실행하면 SNS App RED와 JVM 대시보드가 Grafana에 등록됩니다.

```bash
kubectl apply -k k8s/monitoring/dashboards
```

## SNS App RED

[sns-app-red.json](https://github.com/apieceofcoding/sns-devops/blob/part-5-metrics/k8s/monitoring/dashboards/sns-app-red.json)은 요청량(req/s), 5xx 오류율(%), 평균 응답 시간(ms)을 보여줍니다. 최근 5분 기준이며 `/actuator/health` 요청만 제외해요.

파일을 직접 가져오려면 Grafana **Dashboards → New → Import**에서 JSON을 업로드하고 Prometheus 데이터소스를 선택하세요. 기본 설치에서는 위 Kustomize 명령으로 자동 등록합니다.

## JVM (Micrometer)

저장소의 `jvm-micrometer.json`은 힙 메모리, GC, CPU, 스레드와 컨테이너 메모리를 보여줍니다. Tomcat 스레드는 앱에서 MBean registry를 켜야 나타납니다.

대시보드에서 `application=sns-app`과 현재 앱 인스턴스를 선택하세요. 변수는 Prometheus 시계열에서 동적으로 채워집니다.

```yaml
management:
  metrics:
    tags:
      application: sns-app
```

## Loki 데이터소스

저장소 루트에서 `kubectl apply -f k8s/monitoring/grafana-loki-datasource.yaml`을 실행하면 Loki 데이터소스가 등록됩니다. 내부 URL은 브라우저 주소창이 아니라 Grafana 데이터소스의 **Connection → URL** 값으로 사용합니다. 로그는 Grafana **Explore**에서 데이터소스 **Loki**를 선택해 조회하세요.

```logql
{k8s_namespace_name="sns", service_name="sns-app"}
```
