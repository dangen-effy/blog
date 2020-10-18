---
title: "유니티 for문 없이 가장 가까운 오브젝트 구하기"
description: "LINQ를 활용한 오브젝트 거리 정렬하기"
date: 2020-10-18T17:13:52+09:00
draft: false
tags: ["유니티", "LINQ"]
categories: ["유니티", "LINQ"]
---

![foreach vs linq](/images/forvslinq.PNG)

유니티에서 가장 가까운 오브젝트를 구하는 일은 비일비재합니다. "unity find nearest gameobject" 라는 키워드로 구글링 해보면 정말 많은 예시 코드들이 있는데요, 대부분 for loop 를 이용한 장황한 코드들입니다. 🤦‍♂️

## LINQ

하지만 C# 은 [LINQ](https://docs.microsoft.com/en-us/dotnet/csharp/tutorials/working-with-linq) 로 강력하고 풍부한 열거형 메소드를 지원합니다. 이 LINQ 의 편리한 메소드들을 조합하면 for loop 을 이용한 장황한 코드 없이 간결하게 문제를 해결할 수 있습니다. 😊

## 가장 가까운 오브젝트 찾기

```
private GameObject FindNearestObjectByTag(string tag)
{
    // 탐색할 오브젝트 목록을 List 로 저장합니다.
    var enemies = GameObject.FindGameObjectsWithTag(tag).ToList();

    // LINQ 메소드를 이용해 가장 가까운 적을 찾습니다.
    var neareastEnemy = enemies
        .OrderBy(enemy =>
        {
            return Vector3.Distance(transform.position, enemy.transform.position);
        })
        .ToList()
        .FirstOrDefault(); 

    return neareastEnemy;
}
```

- OrderBy 메소드는 넘겨받은 람다식으로 List를 정렬합니다. 정렬 기준은 Vector3.Distance 메소드로 내 위치와 적 위치의 거리를 계산한 결과입니다. 덕분에 가장 가까운 적이 첫 번째 요소로 정렬됩니다.

- FirstOrDefault 메소드는 List 의 첫 번째 요소를 반환합니다. 만약 List 가 비어있다면 null 을 반환합니다.

- 최종적으로 neareastEnemy 변수에 가장 가까운 오브젝트가 저장됩니다.