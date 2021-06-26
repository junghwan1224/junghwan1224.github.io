---
layout: post
title: 스프링 스케줄러
date: 2021-06-26 19:20:23 +0900
category: Java

---

#### Scheduled Annotation

- `@Scheduled`

- 어노테이션을 붙일 메서드에는 리턴타입&매개변수를 줄 수 없다.

- **fixedDelay**은 이전에 실행된 Task의 종료시간으로 부터 정의된 시간만큼 지난 후 Task를 실행

- **fixedRate**은 이전에 실행된 Task의 시작시간으로 부터 정의된 시간만큼 지난 후 Task를 실행

- **fixedDelay**와 **fixedRate**의 시간 단위는 밀리세컨드(ms), 정수가 아닌 문자열 타입으로도 값 지정 가능

- **initialDelay**는 스케줄러에서 메서드가 등록되자마자 수행하는 것이 아닌 초기 지연시간을 설정, 역시 정수/스트링 타입으로 값 지정 가능

- **cron**

  - 사용할 time zone으로 따로 설정하지 않으면 기본적으로 서버의 time zone

  - ```
    -초 0-59 , - * / 
    -분 0-59 , - * / 
    -시 0-23 , - * / 
    -일 1-31 , - * ? / L W
    -월 1-12 or JAN-DEC , - * / 
    -요일 1-7 or SUN-SAT , - * ? / L # (1:일, 2:월, 3:화, 4:수, 5:목, 6:금, 7:토)
    -년(옵션) 1970-2099 , - * /
      
    * : 모든 값
    ? : 특정 값 없음
    - : 범위 지정에 사용
    , : 여러 값 지정 구분에 사용
    / : 초기값과 증가치 설정에 사용
    L : 지정할 수 있는 범위의 마지막 값
    W : 월~금요일 또는 가장 가까운 월/금요일
    # : 몇 번째 무슨 요일 2#1 => 첫 번째 월요일
    
    크론 표현식에서는 6~7 자리가 사용됨
    
    cron = "* * * * * *"
    
    왼쪽부터 작은 단위 [초 분 시 일 월 요일 년(생략가능)]
    왼쪽부터 작은 단위 [초 분 시 일 월 요일 년(생략가능)]
    왼쪽부터 작은 단위 [초 분 시 일 월 요일 년(생략가능)]
    ```

  - ```
    [매일 오후 1시에 실행할 cron 만들기]
    자 일단 0초 0분이죠
    @Scheduled(cron="0 0")
    
    오후 1시래요 24시 표기법이니까
    @Scheduled(cron="0 0 13")
    
    매일
    @Scheduled(cron="0 0 13 * * ?")
    
    [매 주 금요일 새벽5시 30분 실행할 cron 만들기]
    0초 30분 새벽 5시
    @Scheduled(cron="0 30 05 ")
    
    매주 금요일
    @Scheduled(cron="0 30 05 * FRI ?")
    ```

  - ```java
    //1초 마다 실행
    @Scheduled(fixedDelay=1000)
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    }
    
    //5초 마다 실행
    @Scheduled(fixedDelay=5000)
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    }
    
    //10초 마다 실행
    @Scheduled(fixedDelay=10000)
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    }
    
    //매일 5시에 실행
    @Scheduled(cron="0 0 05 * * ?")
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    }
    
    //매월 2일,20일 새벽2시에 실행
    @Scheduled(cron="0 0 02 2,20 * ?")
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    }
    
    //매월 마지막날 저녁 10시에 실행
    @Scheduled(cron = "0 0 10 L * ?")
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    }
    
    //1시간 마다 실행 ex) 01:00, 02:00, 03:00....
    @Scheduled(cron = "0 0 0/1 * * *")
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    }
    
    //매일 오후 18시마다 실행 ex) 18:00
    @Scheduled(cron = "0 0 18 * * *")
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    }
    
    //매일 오후 18시00분-18시55분 사이에 5분 간격으로 실행
    @Scheduled(cron = "0 0/5 18 * * *")
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    }
    
    //매일 오후 9시00분-9시55분, 18시00분-18시55분 사이에 5분 간격으로 실행
    @Scheduled(cron = "0 0/5 9,18 * * *")
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    }
    
    //매일 오후 9시00분-18시55분 사이에 5분 간격으로 실행
    @Scheduled(cron = "0 0/5 9-18 * * *")
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    }
    
    //매달 1일 00시에 실행
    @Scheduled(cron = "0 0 0 1 * *")
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    
    }
    
    //매년 3월내 월-금요일 10시 30분에만 실행
    @Scheduled(cron = "0 30 10 ? 3 MON-FRI")
    public void testScheduler(){
        System.out.println("스케줄링 테스트");
    }
    ```

  

