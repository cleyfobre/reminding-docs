#오토스케일링 #autoscaling #ecs #aws
AWS ECS 서비스에서 오토스케일링 정책(조정정책) 추가 하고 있는 중인데
아래와 같이 세팅을 했다.

ECSServiceAverageCPUUtilization (70%)
ECSServiceAverageMemoryUtilization (70%)
ALBRequestCountPerTarget (50)

근데 문제는 오토스케일링이 안먹히는 거.

### 원인은 조정정책 이름이 다른 서비스에서 사용 중인 조정정책 이름과 같아서!

그래서 아래처럼 서비스의 이름을 앞에 prefix로 붙여주는 식으로 변경했다.

my-api-cpu-tracking
my-api-mem-tracking
my-api-req-tracking

만약에 my-api1, my-api2 둘 다 같은 이름의 조정정책을 사용하면
두 서비스의 상태를 보고 판단을 하는 듯 하다.
예를 들어, cpu 70 이상이면 스케일업을 걸어놨는데, 
한 서비스에는 70 이상인데, 다른 서비스에는 70 이하니까 스케일업이 안되는 거!
