---
_id: 85fdb7c1-7ab1-480e-9d62-8bb8aca767cf
date: '2024-10-04'
last_modified: '2025-01-24'
title: '[프로그래머스] PCCP 기출문제 3번 / 충돌위험 찾기 Python'
url: https://velog.io/@hso07202/프로그래머스-PCCP-기출문제-3번-충돌위험-찾기
---

요즘 서류에..공부에... 정신이 없어서 블로그는 못 올렸는데 푸는 데 너무 오래 걸린 문제라 써야겠어서 오랜만에 블로그를 켰음🤯 오픽 3일 컷 했던 것도 기록해 놓아야 하는데 바쁘다 바빠
 
[[프로그래머스] [PCCP 기출문제] 3번 / 충돌위험 찾기](https://school.programmers.co.kr/learn/courses/30/lessons/340211)

![](https://velog.velcdn.com/images/hso07202/post/8105c31b-1fbf-42dd-9e9b-e8e90d6c27ff/image.png)

너무 모르겠어서 코드 찾아보려다가 파이썬 코드를 못 찾아서 그냥 일단 냅다 풀어봄..

#### 📕 헤맸던 부분
1. routes[i][j]에 있는 게 points[routes[i][j]] 라는 걸 제대로 생각 못한 점..
2. routes[i]가 (시작 포인트, 끝 포인트)로만 있는 줄 알았는데 여러 곳을 들리기도 한다는 것..
3. 최소 경로..굳이 따로 안 구해도 됨. 장애물이고 뭐고 없어서 그냥 목표를 향해 r -> c 순서로 이동하면 최소 경로임

그래서 일단
```
1. 로봇마다의 이동 경로 구하기
   - 시작점 세팅
   - 이후 좌표들 차례로 목표 좌표로 세팅
     -> r값부터 목표값 도달, 이후 c값 목표값 도달하도록 while문 돌림
     -> 끝나면 다음 목표 좌표 세팅 반복
   - 최종 이동 경로 append
2. 구해놓은 이동 경로로 충돌 확인하기
   -> 서로 이동 길이가 맞지 않아 어떻게 해야할 지 찾다가 다른 분 코드를 참고함..!
   - 끝난 리스트 체크할 empty, 충돌값 체크할 cnt
   - 모든 리스트가 값이 없을 때까지 while문 돌리기
   - 이동하는 로봇 수 만큼 for문 돌리고
     -> 리스트에 값이 더이상 없으면 empty 증가
     -> 있을 경우, 좌표 꺼내서 해당하는 배열값 증가
   - 배열에 값이 2이상이면 같은 곳에서 만나는 것이므로 cnt 증가
📕 이때 mapList = [[0]*101 for _ in range(101)]를 [[0]*101]*101으로 했더니 리스트가 초기화 되지 않아 이상한 값이 나왔었다!.!
      
```

### 💡 내 코드
```python
from collections import deque

def solution(points, routes):
    answer = 0
    routeList = []

    def checkRoute(points, routes):
        for i in range(len(routes)):
            r = deque() # 로봇마다 이동 경로 저장

            # routes[i] => (시작 포인트 (== points[routes[i][0]]), 방문 포인트, ... , 끝 포인트)
            start = points[routes[i][0]-1]
            x, y = start[0], start[1]
            r.append((x, y))
            
            for j in range(len(routes[0])):
                end = points[routes[i][j]-1]
                dx, dy = end[0], end[1]

                nx, ny = dx-x, dy-y
                while(nx != 0):
                    if(nx > 0):
                        x += 1
                        nx -= 1
                    else:
                        x -= 1
                        nx += 1
                    r.append((x, y))

                while(ny != 0):
                    if(ny > 0):
                        y += 1
                        ny -= 1
                    else:
                        y -= 1
                        ny += 1
                    r.append((x, y))
            routeList.append(r)
    
    def checkCollide(routeList):
        empty = 0
        cnt = 0

        while(empty < len(routes)):
            empty = 0
            mapList = [[0]*101 for _ in range(101)]
            
            for i in range(len(routes)):
                if not routeList[i]:
                    empty += 1
                    continue
                
                nx, ny = routeList[i].popleft()
                mapList[nx][ny] += 1
            
            for i in range(1, 101):
                for j in range(1, 101):
                    if(mapList[i][j] > 1):
                        cnt += 1
            
        return cnt

    checkRoute(points, routes)
    answer = checkCollide(routeList)

    return answer
```