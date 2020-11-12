# OS
* * *
### Paging

 ![img](https://lh5.googleusercontent.com/PCKovkDHBKjR-f26p8W5580EgY2MlxvNdlNFdegkT5_h9JKFY5HMjHV8kfXhIjUFBDKCXqFYHbhP_oZJldJczeg_4rPpVPVIh0kzstq4NdZg3EUT1iwl22np7Ww11BcLsv-z-HOO)       

 논리적 메모리를 일정한 크기로 잘라서 물리적 메모리에 넣는것. 페이지 테이블에는 해당 페이지가 물리적 주소의 어디에 저장되어 있는지 저장해놓음. 0번 page는 1번 frame에 올라가있고, 1번은 4번에 저장되어있다. cpu가 논리적인 주소를 원하면 논리적인 주소로 바꿔야 하는데, 페이지 테이블로 찾는다. 주소에서 앞부분이 페이지 번호 뒤에 offset 논리적인 주소를 물리적인 주소로 찾아서 offset을 더해(위에서 몇번째인지) 물리적 주소를 반환한다. offset은 물리 논리 상관없이 같음. 

 페이지 테이블은 어디에 들어가는가. MMU는 레지스터로 처리하면 됐음. 프로그램 하나의 주소공간이 100만개의 page를 가지고 있는 pagetable이 있을것. 프로그램마다 page table이 필요하니까 비효율. 이게 어디에 들어가야 할까. page table을 메모리에 넣는다. 결국에는 메모리 접근을 위해서는 2번의 memory access 필요.  MMU는 page table base register와 page table length register로 사용된다. 주소변환을 위한 페이지 테이블의 크기와 테이블을 가리킴. 속도 향상을 위해 TLB를 추가. 별도의 하드웨어. 캐시라고 생각하면 편안. TLB에 저장되어 있는 정보 중에 내가 원하는 Page 정보가 있니 먼저 확인하고 있으면 메모리 접근(메모리 한번). 없으면 페이지 테이블 참고(메모리 2번) 

 TLB는 모든 페이지 정보가 있는 것이 아님. 그리고 논리적 페이지, 물리적 페이지 쌍을 모두 가지고 있어야 함. 있는지 확인하려면 전체 서치해야함. 그렇게 하지 않기 위해서 병렬 처리가 가능하도록 함. 한방에 쫙 서치해서. 페이지 내용 없는 경우를 미스라고함. 미스나면 페이지 테이블 볼 수 밖에.. 프로세스마다 주소 정보가 다르기 때문에 context switching이 발생할 때마다 내용을 바꿔야함. 

### CPU Scheduling

 CPU Scheduler 운영체제 안에서 스케줄링을 하는 코드임. 소프트웨어이다. Dispatcher도 역시 소프트웨어. dispatcher는 cpu를 누구에게 줄지 정했으면 실제로 cpu를 주는 역할을 하는 소프트웨어임. 이 과정을 context switch라고 함. 문맥교환.

#### CPU 스케줄링이 필요한 경우 

1. cpu를 어떤 프로세스가 가지고 있다가 io 작업처럼 오래 걸리는 작업을 하는 경우 자진해서 cpu를 내어주는 경우( Running -> Blocked)
2. 나는 더 쓰고 싶은데 운영체제가 강제로 빼앗는 경우 ( Running -> Ready )
3. IO를 요청하는 프로세스의 io 작업이 끝날경우. 인터럽트를 걸어 원래 실행되고 있는 프로세스가 잠시 중단된다. 인터럽트가 끝난 후 원래 실행되는 코드는 다시 실행되고 blocked됐던 프로세스는 ready로 변함. (Blocked -> Ready ) 
4. 프로세스가 종료되서 새로운 프로세스에 cpu를 넘기는 경우(Terminate)

 1,4의 경우 더이상 쓸 필요가 없어서 cpu 자진 반납. (nonpreemptive : 강제로 빼앗지 않고 자진 반납)
 2,3의 경우는 Preemptive(강제로 빼앗음)

#### CPU 스케줄러을 통한 성능 측정(성능 척도)

##### 시스템 입장에서의 성능 척도 : 최대한 많이 처리하는 것이 좋은것

- cpu utilization : 전체 시간 중에서 cpu를 이용한 비율. cpu를 놀리지 말고 최대한 일을 시켜라.
- Throughput(처리량) : 일정 시간 이내에 몇개의 일을 처리했느냐. 

##### 프로그램 입장에서의 성능 척도  : 내가 cpu빨리 얻어서 빨리 끝나는 것.

- Turnaround time(소요시간) : cpu를 쓰러 들어와서 다 쓸 때 까지 걸린 시간. 쓴 시간 + 기다린 시간 다 합친거.
- waiting time : cpu를 순수하게 기다린 시간 cpu를 얻었다가 뺐겼을 때, 기다리는 시간이 있을 것이다. 여러번 그럴것인데 기다린 모든 시간의 합.
- response time(응답시간) : ready queue에 들어와서 처음으로 cpu를 얻기까지 걸린 시간.

#### scheduling algorithm

##### FCFS

 먼저온 순서대로 처리. 사람의 세계에서는 대개 쓰는 방법. 효율적이지 않음.   convoy effect : 먼저온 프로세스가 너무 오랜시간 cpu를 사용해 뒤의 프로세스가 cpu를 빨리 사용하지 못하는 현상.

##### Shortest Job-First(SJF)

 빨리 끝나는 프로세스 먼저 cpu 쓰게 하는 알고리즘. Average waiting time을 최소화 할 수 있음. Preemptive한 상황에서는 (Shortest Remaining Time First)으로 최적의 Average waiting time을 얻을 수 있음.Non-Preemptive 한 경우, 긴 프로세스가 먼저 올 경우, 늦게 온 짧은 프로세스는 이를 계속 기다려야 함. 오래걸릴 수 있음. cpu를 다 쓰고 나가는 시점에 스케줄링이 일어난다.Preemptive한 경우 : 프로세스가 올 때마다 더 짧은 프로세스 먼저 쓰게 됨. 

 문제점 1. : Starvation. 극단적으로 cpu 사용이 짧은 것을 선호함. cpu 사용시간이 긴 프로세스는 영원히 사용하지 못할수도 있음.   
 문제점 2. cpu를 얼마나 쓰고 나갈지 예측할 수 없다. cpu 사용시간을 미리 알 수 없지만 추정할 순 있음. io bound의 경우 cpu 사용시간이 짧고, cpu bound의 경우 cpu 사용시간이 기니까 어느정도 추정할 수 있음. exponential averaging

##### Priority Scheduling

 우선순위가 높은 것에 cpu를 주겠다.  

- preemptive : 우선순위가 높은 프로세스가 들어오면 뺏어서 주겠다.  
- non-preemptive : 일단 프로세스 먼저 종료하고 우선순위가 높은 프로세스를 실행하겠다.  

 sjp의 경우 cpu 사용시간이 우선순위가 될 것이다. 그러나 문제점이 있음. 컴퓨터 시스템에서는 효율성이 가장 중요한 것은 맞지만 한쪽에 차별을 너무 주면 안된다. 그래서 Aging 기법을 사용함.

##### Round Robin Scheduling

 선점형 스케줄링. cpu를 줄 때 할당시간을 세팅해서 주게된다. 이 시간이 끝나면 ready queue에 프로세스가 놓임. 응답시간이 빨라지는 것이 장점. 현재 queue에 n개의 프로세스가 있다면 cpu 사용시간 / n 의 시간동안에 적어도 cpu를 할당받을 수 있다. q(cpu 사용시간)이 너무 클경우, FCFS가 되고, 너무 작을 경우 문맥 교환 비용이 너무 들어서 비효율적이 된다.

##### Multilevel Queue

 계급이 있다. 높은 계급이 먼저 cpu 쓰고 그다음 계급의 큐가 쓰고 ~~- 우선순위가 높은 큐를 모두 처리해야 낮은 큐를 처리한다.- ready queue를 여러 개로 분할. interactive한 프로세스인지 batch(no human interaction) 프로세스인지. 전자는 RR, 후자는 FCFS.

##### Multilevel Feedback Queue

 프로세스가 다른 큐로 이동 가능. 큐의 기준. 승격, 강등의 기준 등이 필요함. - 보통 처음들어오는 프로세스는 우선순위 가장 높게. time quantum을 짧게 줌. 다음 큐에는 점점 길게줌. 그러다 FCFS. cpu 사용 시간이 긴 프로세스는 낮은 큐로 갔다가 쓰임. cpu 사용시간이 짧은 프로세스에 우선권을 줌. 빨리 끝나니까. 긴 프로세스는 점점 낮은 큐로 밀려남. 

##### Multiple-Processor Scheduling

 큐에 한줄로 세워서 각 프로세서가 알아서 꺼내가게 하는 방법.

 특정 cpu에 실행해야하는 프로세스가 있으면 잘 스케줄링해야함. 

 Load Sharing : 일부 프로세서에 job이 몰리지 않도록 적절히 부하를 공유. 

##### Real-Time Scheduling

 deadline이 있는 잡. 데드라인을 보장해줘야 함. cpu에서 데드라인 안에 처리 해야하는 것이 목표. 미리 스케줄링을 해서 데드라인 안에 제대로 동작하도록 하는. real time이 periodical 한 경우가 많음. 데드라인 보장이 중요.- hard real-time system, soft real-time computing

#### Thread Scheduling

 스레드를 구현하는 방식 : User level thread, Kernal Level Thread. 상황이 다르기 때문에 스케줄링 하는 방법도 다름. 

- Local scheduling : 사용자 프로세스가 직접 cpu 스케줄링함. 
- Global scheduling : 일반 프로세스와 마찬가지로 커널에서 스케줄링함.

#### Algorithm Evaluation

- Queueing model : 큐에 job들이 도착해서 쌓임. cpu 용량에 따라 처리하고 빠져나감. 단위시간당 처리량 등등
- Implementation & Measurement : 실제 실행해서 비교. 운영체제의 스케줄러와 내가 만든 스케줄러를 같이 돌려서 어느것이 좋은지 비교하는 것. 실측함.
- Simulation : 실제로 돌리는 것이 아니라 모의실험. 시뮬레이션 코드 짜서. 여러가지 예제를 돌림.