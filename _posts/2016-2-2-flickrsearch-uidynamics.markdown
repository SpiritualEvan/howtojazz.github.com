---
title:  "Flickr Search + UIAttachmentsBehavior"
description: UICollectionView 에 UIAttachmentsBehavior 를 사용한 예제
---

<h1>예제의 실행</h1>

이 예제는 [Github] 를 통해서 프로젝트 다운이 가능하다.

<img src="/assets/images/mytutorial.gif" alt="Logo">

UICollectionView 를 사용하는 부분은 [raywenderlich.com] 에서<br>
UIAttachmentsBehavior 를 사용하는 부분은 [objc.io] 의 글을 참조하였다.


프로젝트를 Build & Run 하고 아래와 같은 에러가 뜨면 플리커 API 키가 만료된 것이므로 새로운 것으로 교체해 주어야 한다.

	Error searching : Optional(Error Domain=FlickrSearch Code=0 "Invalid API Key (Key not found)" UserInfo={NSLocalizedFailureReason=Invalid API Key (Key not found)})
	Message from debugger: Terminated due to signal 15

아래의 과정을 통해 API 키를 교체할 수 있다.

1. [flickr api test]페이지로 접속 
2. 최하단의 Call Method 버튼을 누른다
3. 이후 뜨는 화면의 최하단을 보면 URL 란에서 api_key 파라메터의 값을 긁어서 복사한다.
4. Xcode 프로젝트의 FlickrSearcher.swift 파일안에서 apiKey 상수부분에 이 값을 붙여 넣는다.
<br>
<br>

<h1>더욱 부드러운 애니메이션</h1>

UIAttachmentsBehavior 를 적용하는데는 문제가 없었지만, 애니메이션이 만족스럽지 못했다.

아이폰의 메세지앱과 비교해보면, 이 예제에서는 애니메이션 끝자락에 셀이 상하로 1~2픽셀씩 왔다갔다하는 부분이 부자연스러워 보인다.

<img src="/assets/images/mytutorial.gif" alt="Logo">

메세지앱은 이런 부분없이 깔끔하게 셀들이 서로를 끌어당기듯이 움직인다.

<img src="/assets/images/messages.gif" alt="Logo">

UIAttachmentBehavior 프로퍼티인 damping, frequency 값을 조정하여 여러번 테스트하여도 메세지앱처럼 깔끔한 애니메이션은 보여주지 못했다.

이를 해결하기 위해, 애니메이션의 막바지에 탄성운동하는것을 없애기 위한 저항을 추가하는 방법을 선택했다.
[UIDynamicItemBehavior]클래스가 저항속성으로 resistance 프로퍼티를 제공한다.

<h1>Subclassing UIDynamicBehavior</h1>
저항속성을 UIAttachmentBehavior과 조합하기 위해 CellBehavior 클래스를 만들었다.

{% highlight swift %}
class CellBehavior: UIDynamicBehavior {
    private var itemBehavior:UIDynamicItemBehavior!
    private var attachmentBehavior:UIAttachmentBehavior!
}
{% endhighlight %}

CellBehavior 클래스는 멤버변수로 UIDynamicItemBehavior, UIAttachmentBehavior 를 갖는다.
UIDynamicAnimator 에 Behavior 처럼 동작하도록 추가하기 위해 CellBehavior 는 UIDynamicBehavior 를 상속받는다.

기존 UIAttachmentBehavior 와 사용하던 부분과 최대한 일치시키기 위해 초기화, anchorPoint, item 부분도 추가했다.

{% highlight swift %}
var item:UIDynamicItem 
{
	get 
	{
		return self.itemBehavior.items.first!
	}
}
var anchorPoint:CGPoint 
{
	get 
	{
		return self.attachmentBehavior.anchorPoint
	}
	set 
	{
		self.attachmentBehavior.anchorPoint = newValue
	}
}
init(item:UIDynamicItem!, attachedToAnchor anchorPoint:CGPoint) 
{
        super.init()
        self.attachmentBehavior = UIAttachmentBehavior(item: item, attachedToAnchor: item.center)
        self.attachmentBehavior.frequency = 2.5
        self.attachmentBehavior.damping = 0.0
        
        self.itemBehavior = UIDynamicItemBehavior(items: [item])
        self.itemBehavior.resistance = 30.0
        self.addChildBehavior(self.attachmentBehavior)
        self.addChildBehavior(self.itemBehavior)
        
}
{% endhighlight %}

이 클래스를 만든 후, UICollectionView 에서 UIAttachmentsBehavior 를 사용하던부분을 CellBehavior로 교체하였다.

<img src="/assets/images/advanced_pan.gif" alt="Logo">

[raywenderlich.com]: http://www.raywenderlich.com/78550/beginning-ios-collection-views-swift-part-1
[objc.io]: https://www.objc.io/issues/5-ios7/collection-views-and-uidynamics/#the-dynamic-animator
[Github]: https://github.com/howtojazz/flickrsearchdynamics.git
[flickr api test]: https://www.flickr.com/services/api/explore/?method=flickr.photos.search
[UIDynamicItemBehavior]: https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIDynamicItemBehavior_Class/index.html