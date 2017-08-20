---
title:  "블록구문을 array 에 담기"
description: 블록구문을 array 에 담기
---

배열에 블록을 포함할때는 copy 를 이용하여 스택의 블록을 힙으로 복사하여
힙 객체를 배열에 포함시켜야 한다.
그렇지 않으면 지역변수 규칙에 따라 block이 사라져버려
나중에 배열이 메모리 해제된 block 을 참조하는 문제가 발생하게 된다.

[Executing Blocks From NSArray?]




[Executing Blocks From NSArray?]: http://stackoverflow.com/questions/3257940/executing-blocks-from-nsarray
