^title Decoupling Patterns

^title Decoupling 패턴

Once you get the hang of a programming language, writing code to do what you
want is actually pretty easy. What's hard is writing code that's easy to adapt
when your requirements *change*. Rarely do we have the luxury of a perfect
feature set before we've fired up our editor.

여러분이 프로그래밍 언어를 이해하기 시작하면 우리가 실제로 원하는 무언가를 수행하는 코드를 작성하는 것은 상당히 쉬운 일입니다. 여러분의 요구 사항이 *변경*될 때, 그것을 쉽게 적용할 수 있는 코드를 작성하는 일은 어렵습니다. 우리가 편집기를 작동시키기도 전에 완벽한 기능를 가지기는 어렵습니다.

A powerful tool we have for making change easier is *decoupling*. When we say
two pieces of code are "decoupled", we mean a change in one usually doesn't
require a change in the other. When you change some feature in your game, the
fewer places in code you have to touch, the easier it is.

변경을 좀 더 쉽게 만들어 주는 강력한 도구는 바로 *Decoupling* 입니다. 우리가 두 코드의 조각이 "decoupled" 되었다고 말할 때, 우리는 보통 하나를 변경하기 위해서 다른 것을 변경할 필요가 없다는 것을 의미합니다. 여러분이 게임에서 어떤 기능를 변경하거나 우리가 건드려야 하는 코드가 적을 때, 변경은 좀 더 쉬워집니다.

[Components](component.html) decouple different domains in your game from each
other within a single entity that has aspects of all of them. [Event
Queues](event-queue.html) decouple two objects communicating with each other,
both statically and *in time*. [Service Locators](service-locator.html) let
code access a facility without being bound to the code that provides it.

[Components](component.html)는 여러분의 게임 내에 있는 다른 도메인들을 각각의 도메인들들의 양상을 가지는 하나의 Entity 내에서 그 밖의 다른 것들로부터 decouple합니다. [Event Queue](event-queue.html)는 정적으로 제 시간 안에 두 오브젝트 사이의 통신을 decouple합니다. [Service Locators](service-locator.html)는 코드가 기능를 제공하는 코드에 바인드되지 않고도 기능에 접근하도록 할 것입니다. 

## The Patterns

## 관련 패턴

* [Component](component.html)
* [Event Queue](event-queue.html)
* [Service Locator](service-locator.html)
