^title Subclass Sandbox
^section Behavioral Patterns
^title Subclass Sandbox
^section 행동 패턴


## Intent
## 목적

*Define behavior in a subclass using a set of operations provided by its
base class.*
*base class에서 제공되어지는 일련의 작업을 사용하는 subclass에서의 행동을 정의하라.*

## Motivation
## 동기부여

Every kid has dreamed of being a superhero, but unfortunately, cosmic rays are
in short supply here on Earth. Games that let you pretend to be a superhero are
the closest approximation. Because our game designers have never learned to say,
"no", *our* superhero game aims to feature dozens, if not hundreds, of
different superpowers that heroes may choose from.
모든 아이들은 슈퍼히어로가 되려는 꿈을 갖지만, 불행하게도, 지구상에 우주선들은 아직 공급이 딸린다. 당신이 슈퍼히어로인 체 하게 만드는 게임들은 가장 가까운 근사값이다. 왜냐하면 우리들의 게임 디자이너들은 "아니", 라고 말하는 것을 전혀 배우지 못했고, *우리들의* 슈퍼히어로 게임은 수십개를 특징으로 삼는 것을 목표로 가진다. 만약 수백개가 아니더라도, 히어로들이 선택할 수도 있는 각양 각색의 슈퍼 파워들을. 

Our plan is that we'll have a `Superpower` base class. Then, we'll have a <span
name="lots">derived</span> class that implements each superpower. We'll divvy up
the design doc among our team of programmers and get coding. When we're done,
we'll have a hundred superpower classes.
우리들의 계획은 우리가 '슈퍼파워' base class를 갖는 것이다. 그리고나서, 우리는 각각의 슈퍼파워 요소들인 <span
name="lots">파생된</span> class들을 가질 것이다.

<aside name="lots">
<aside name="lots">

When you find yourself with a *lot* of subclasses, like in this example, that
often means a data-driven approach is better. Instead of lots of *code* for
defining different powers, try finding a way to define that behavior in *data*
instead.
이 예시와 같이 당신이 *수많은* subclass들을 스스로 찾을 때, 그것은 흔히 데이터 기반 접근이라고 의미하는게 나을 것이다. 각각 다른파웓르을 정의하는 많은 *코드* 대신에, 그 행동을 *데이터*로 정의할 방법을 찾는 것을 시도해라.

Patterns like <a class="pattern" href="type-object.html">Type Object</a>, <a
class="pattern" href="bytecode.html">Bytecode</a>, and <a class="gof-pattern"
href="http://en.wikipedia.org/wiki/Interpreter_pattern">Interpreter</a> can all
help.
<a class="pattern" href="type-object.html">Type Object</a>, <a
class="pattern" href="bytecode.html">Bytecode</a>와 같은 패턴들, 그리고 <a class="gof-pattern"
href="http://en.wikipedia.org/wiki/Interpreter_pattern">Interpreter</a>는 도움이 될 것이다.

</aside>
</aside>

We want to immerse our players in a world teeming with variety. Whatever power
they dreamed up when they were a kid, we want in our game. That means these
superpower subclasses will be able to do just about everything: play sounds,
spawn visual effects, interact with AI, create and destroy other game entities,
and mess with physics. There's no corner of the codebase that they won't touch.
우리는 우리의 플레이어들이 다양한 세계에 몰두하기를 원한다. 그들이 아이였을 때 꿈꿔온 파워가 무엇이든지, 우리는 그것들이 우리의 게임에 있기를 원한다. 이것은 이러한 슈퍼파워 subclass들이 모든 것들을 보여주는 것을 가능케 한다는 의미이다: 플레이 사운드, 시각 효과, AI와의 상호작용, 다른 게임 개체들은 창조 혹은 파괴, 그리고 물리학을 파괴하는 것. 여기에는 그들이 건드리고 싶지 않아 하는 코드 베이스의 모서리가 없다.

Let's say we unleash our team and get them writing superpower classes. What's
going to happen?
우리가 우리들의 팀을 촉발시키고 그들이 슈퍼파워 class들을 쓰게 한다고 말해 보자. 무슨 일이 생길까?

 *  *There will be lots of redundant code.* While the different powers will be
    wildly varied, we can still expect plenty of overlap. Many of them will
    spawn visual effects and play sounds in the same way. A freeze ray, heat
    ray, and Dijon mustard ray are all pretty similar when you get down to it.
    If the people implementing those don't coordinate, there's going to be a lot
    of duplicate code and effort.
 *  *여기에는 쓸모 없는 코드가 많을 것이다.* 각각의 파워들이 걷잡을 수 없이 다양해지는 동안, 우리는 많은 겸침이 있을 것이라는 걸 계속해서 예상 가능하다. 많은 슈퍼파워들이 시각 효과와 플레이 사운드를 같은 방법으로 낳을 것이다. 당신이 한파 광선, 열 광선, 그리고 디종 겨자 광선을 시작할 때, 그것들은 모두 서로간에 매우 유사할 것이다. 만약 그것들을 실행하려는 사람들이 그것들은 조직화하지 않으면, 거기엔 매우 많은 복제 코드와 노력이 필요할 것이다.
 
 *  *Every part of the game engine will get coupled to these classes.* Withoutㅁ
    knowing better, people will write code that calls into subsystems that were
    never meant to be tied directly to the superpower classes. If our renderer
    is organized into several nice neat layers, only one of which is intended to
    be used by code outside of the graphics engine, we can bet that we'll end up
    with superpower code that pokes into every one of them.
 *  *게임 엔진의 모든 부분들은 이러한 class들에 연결될 것이다.* 더 잘 알지 않고서는, 사람들은 슈퍼파워 class들과 직접적으로 연결되는 것을 전혀 의미하지 않는 하부시스템들을 소환하는 코드들을 쓸 것이다. 만약 우리의 renderer가 멋지게 정돈된 몇몇의 layer들을 조직화되게 한다면, 오직 그것들 중 하나만이 그래픽 엔진의 밖에 있는 코드에 의해 사용되게끔 의도될 것이고, 우리는 결국 모든 슈퍼파워에 쓸데 없이 참견하는 슈퍼파워 코드들을 끝내는 데에 내기를 걸 수 있을 것이다.

 *  *When these outside systems need to change, odds are good some random
    superpower code will get broken.* Once we have different superpower classes
    coupling themselves to various and sundry parts of the game engine, it's
    inevitable that changes to those systems will impact the power classes.
    That's no fun because your graphics, audio, and UI programmers probably
    don't want to also have to be gameplay programmers *too*.
 *  *이러한 외부 시스템들의 변화가 필요할 때, odd들은 망가지기 좋은 몇몇의 무작위한 슈퍼파워 코드이다. 우리가 게임 엔진의 다양하고 잡다한 부분들을 위해 각양각색의 슈퍼파워 class들을 연결할 때, 그 시스템들의 변화가 파워 class들에게 영향을 주는 것은 피할 수 없다. 이는 재미 없다. 왜냐하면 당신의 ㄱ래픽, 오디오, 그리고 UI 프로그래머들이 아마도 게임플레이 프로그래머들*도* 원하지 않을 것이다.

 *  *It's hard to define invariants that all superpowers obey.* Let's say we
    want to make sure that all audio played by our powers gets properly queued
    and prioritized. There's no easy way to do that if our hundred classes are
    all directly calling into the sound engine on their own.
 *  *모든 슈퍼파워가 복종하는 불변요소를 정의하는 것은 어렵다.* 우리가 우리들의 파워가 적절하게 줄을 서서 기다리고 우선순위를 매기도록 플레이되는 모든 오디오를 확신하고 싶어한다고 해보자. 만약 우리의 몇백개의 class들이 모두 직접적으로 그들 자신의 소리 엔진을 소환하려면 이는 쉬운 방법이 아닐 것이다.

What we want is to give each of the gameplay programmers who is implementing a
superpower a set of primitives they can play with. You want your power to play a
sound? Here's your `playSound()` function. You want particles? Here's
`spawnParticles()`. We'll make sure these operations cover everything you need
to do so that you don't need to `#include` random headers and nose your way into
the rest of the codebase.
우리가 원하는 것은 게임에서의 일련의 원시적인 슈퍼파워들을 실행하는 각각의 게임플레이 프로그래머들에게 제공하는 것이다. 당신은 당신의 파워가 소리가 나는 것을 원하는가? 여기에 당신의 `playSound()` 기능이 있다. 당신은 입자들을 원하는가? 여기에 `spawnParticles()`가 있다. 우리는 이러한 작업들이 당신이 원하는 모든 것을 덮는 걸 확신할 수 있도록 당신이 `#include` 무작위의 헤더를 필요로 하지 않고 나머지의 코드베이스들이 당신의 길을 간섭하지 않도록 할 것이다.

We do this by making these operations *protected methods of the* `Superpower`
*base class*. Putting them in the base class gives every power subclass direct,
easy access to the methods. Making them protected (and likely non-virtual) communicates
that they exist specifically to be *called* by subclasses.
우리는 *'슈퍼파워' *기본 class*의 보호되어진 방법*인 이러한 작업들을 만듦으로써 이를 해결한다. 그것들을 기본 class에 넣는 것은 모든 파워 subclass가 직접적으로, 방법에 쉽게 접근하게 만든다. 보호된(그리고 비가상적인 것과 다름없는) 그것들을 만드는 것은 그들이 subclass라고 불리우며 분명히 존재하는 것들과 소통한다.

Once we have these toys to play with, we need a place to use them. For that,
we'll define a *sandbox method*, an abstract protected method that subclasses
must implement. Given those, to implement a new kind of power, you:
우리가 이러한 장난감들을 함께 갖고 놀 때, 우리는 그것들을 사용할 장소가 필요하다. 그 때문에, 우리는 subclass들이 반드시 실행해야 하는 추상적인 보호 방법인 *sandbox method*를 정의할 것이다. 

1.  Create a new class that inherits from `Superpower`.
1. '슈퍼파워'에서 물려받은 새로운 class를 만들어라.

2.  Override `activate()`, the sandbox method.
2.  sandbox 방법인 `activate()`를 우선시해라.

3.  Implement the body of that by calling the protected methods that
    `Superpower` provides.
3.  '슈퍼파워'가 제공하는 보호 방법이라고 불리우는 그것의 몸체를 실행해라.

We can fix our redundant code problem now by making those provided operations as
high-level as possible. When we see code that's duplicated between lots of the
subclasses, we can always roll it up into `Superpower` as a new operation that
they can all use.
우리는 지금 가능한 한 고도의 레벨에서 제공되어지는 작업을 만듦으로써 우리의 불필요한 코드 문제를 개선할 수 있다. 우리가 수많은 subclass들 사이에서 복제되는 코드를 볼 때, 우리는 항상 그것을 우리가 사용할 수 있는 새로운 작업으로써의 '슈퍼파워'로 둥글게 만다. 

We've addressed our coupling problem by constraining the coupling to one place.
`Superpower` itself will end up coupled to the different game systems, but our
hundred derived classes will not. Instead, they are *only* coupled to their base
class. When one of those game systems changes, modification to `Superpower` may
be necessary, but dozens of subclasses shouldn't have to be touched.
우리는 하나의 장소에 연결시키도록 강요하는 우리들의 연결 문제를 다뤘다. '슈퍼파워' 그 자체는 다른 게임 시스템에 연결되는 것으로 끝날 것이지만, 우리들의 수백개의 파생된 class들은 그렇지 않을 것이다. 대신에, 그것들은 *오직* 그들의 기본 class에 연결된다. 그러한 게임 시스템 중 하나가 바뀔 때, '슈퍼파워'의 변경은 아마도 불가피할 것이지만, 수십개의 subclass들은 건드릴 필요가 없다.

This pattern leads to an architecture where you have a shallow but wide class
hierarchy. Your <span name="wide">inheritance</span> chains aren't *deep*, but
there are a *lot* of classes that hang off `Superpower`. By having a single
class with a lot of direct subclasses, we have a point of leverage in our
codebase. Time and love that we put into `Superpower` can benefit a wide set of
classes in the game.
이 패턴은 당신이 얕지만 광범위한 class 계층을 가지는 아키텍쳐를 이끈다. 당신의 <span name="wide">상속</span> 사슬을 *깊지* 않지만, '슈퍼파워'를 놓아주는 *많은* class들이 있다. 많은 직통의 subclass들과 함께하는 단일한 class들을 가짐으로써, 우리는 우리들의 코드베이스에서 영향력을 가지고 있다. 우리가 '슈퍼파워'에 넣어둔 시간과 사랑은 게임에서의 광범위한 일련의 class들에 유용하게 된다.

<aside name="wide">
<aside name="wide">

Lately, you find a lot of people criticizing inheritance in object-oriented
languages. Inheritance *is* problematic -- there's really no deeper coupling in
a codebase than the one between a base class and its subclass -- but I find
*wide* inheritance trees to be easier to work with than *deep* ones.
최근에, 당신은 객체 지향 언어의 상속을 비판하는 많은 사람들을 발견한다. 상속*은* 문제가 많다 -- 그것은 정말로 base class와 그것의 subclass간의 연결보다 코드베이스에 깊게 연결되어 있지 않다 -- 하지만 나는 *광범위한* 상속 나무들이 *깊은* 것들보다 쉽게 작업할 수 있다는 것을 발견했다. 

</aside>
</aside>

## The Pattern

A **base class** defines an abstract **sandbox method** and several **provided
operations**. Marking them protected makes it clear that they are for use by
derived classes. Each derived **sandboxed subclass** implements the sandbox
method using the provided operations.

## When to Use It

The Subclass Sandbox pattern is a very simple, common pattern lurking in lots of codebases, even outside
of games. If you have a non-virtual protected method laying around, you're
probably already using something like this. Subclass Sandbox is a good fit
when:

 *  You have a base class with a number of derived classes.

 *  The base class is able to provide all of the operations that a derived class
    may need to perform.

 *  There is behavioral overlap in the subclasses and you want to make it easier
    to share code between them.

 *  You want to minimize coupling between those derived classes and the rest of
    the program.

## Keep in Mind

"Inheritance" is a bad word in many programming circles these days, and one
reason is that base classes tend to accrete more and more code. This pattern is
particularly susceptible to that.

Since subclasses go through their base class to reach the rest of the game, the
base class ends up coupled to every system *any* derived class needs to talk to.
Of course, the subclasses are also intimately tied to their base class. That
spiderweb of coupling makes it very hard to change the base class without
breaking something -- you've got the [brittle base class problem][].

The flip side of the coin is that since most of your coupling has been pushed up
to the base class, the derived classes are now much more cleanly separated from
the rest of the world. Ideally, most of your behavior will be in those
subclasses. That means much of your codebase is isolated and easier to maintain.

Still, if you find this pattern is turning your base class into a giant bowl of
code stew, consider pulling some of the provided operations out into separate
classes that the base class can dole out responsibility to. The <a
class="pattern" href="component.html">Component</a> pattern can help here.

[brittle base class problem]: http://en.wikipedia.org/wiki/Fragile_base_class

## Sample Code

Because this is such a simple pattern, there isn't much to the sample code. That
doesn't mean it isn't useful -- the pattern is about the *intent*, not the
complexity of its implementation.

We'll start with our `Superpower` base class:

^code 1

The `activate()` method is the sandbox method. Since it is virtual and abstract,
subclasses *must* override it. This makes it clear to someone creating a power
subclass where their work has to go.

The other protected methods, `move()`, `playSound()`, and `spawnParticles()`, are
the provided operations. These are what the subclasses will call in their
implementation of `activate()`.

We didn't implement the provided operations in this example, but an actual game
would have real code there. Those methods are where `Superpower` gets coupled to
other systems in the game -- `move()` may call into physics code, `playSound()`
will talk to the audio engine, etc. Since this is all in the *implementation* of
the base class, it keeps that coupling encapsulated within `Superpower` itself.

OK, now let's get our radioactive spiders out and create a power. Here's one:

<span name="jump"></span>

^code 2

<aside name="jump">

OK, maybe being able to *jump* isn't all that *super*, but I'm trying to keep
things basic here.

</aside>

This power springs the superhero into the air, playing an appropriate sound and
kicking up a little cloud of dust. If all of the superpowers were this simple --
just a combination of sound, particle effect, and motion -- then we wouldn't
need this pattern at all. Instead, `Superpower` could have a baked-in
implementation of `activate()` that accesses fields for the sound ID, particle
type, and movement. But that only works when every power essentially works the
same way with only some differences in data. Let's elaborate on it a bit:

^code 3

Here, we've added a couple of methods to get the hero's position. Our
`SkyLaunch` subclass can now use those:

^code 4

Since we have access to some state, now our sandbox method can do actual,
interesting control flow. Here, it's still just a couple of simple `if`
statements, but you can do <span name="data">anything</span> you want. By having
the sandbox method be an actual full-fledged method that contains arbitrary
code, the sky's the limit.

<aside name="data">

Earlier, I suggested a data-driven approach for powers. This is one reason why
you may decide *not* to do that. If your behavior is complex and imperative,
it is more difficult to define in data.

</aside>

## Design Decisions

As you can see, Subclass Sandbox is a fairly "soft" pattern. It describes a basic idea, but
it doesn't have a lot of detailed mechanics. That means you'll be making some
interesting choices each time you apply it. Here are some questions to consider.

### What operations should be provided?

This is the biggest question. It deeply affects how this pattern feels and how
well it works. At the minimal end of the spectrum, the base class doesn't
provide *any* operations. It just has a sandbox method. To implement it, you'll
have to call into systems outside of the base class. If you take that angle,
it's probably not even fair to say you're using this pattern.

On the other end of the spectrum, the base class provides <span
name="include">*every*</span> operation that a subclass may need. Subclasses are
*only* coupled to the base class and don't call into any outside systems
whatsoever.

<aside name="include">

Concretely, this means each source file for a subclass would only need a single
`#include` -- the one for its base class.

</aside>

Between these two points, there's a wide middle ground where some operations are
provided by the base class and others are accessed directly from the outside
system that defines it. The more operations you provide, the less coupled
subclasses are to outside systems, but the *more* coupled the base class is. It
removes coupling from the derived classes, but it does so by pushing that up to the
base class itself.

That's a win if you have a bunch of derived classes that were all coupled to
some outside system. By moving the coupling up into a provided operation, you've
centralized it into one place: the base class. But the more you do this, the
bigger and harder to maintain that one class becomes.

So where should you draw the line? Here are a few rules of thumb:

 *  If a provided operation is only used by one or a few subclasses, you don't
    get a lot of bang for your buck. You're adding complexity to the base class,
    which affects everyone, but only a couple of classes benefit.

    This may be worth it to make the operation consistent with other
    provided operations, or it may be simpler and cleaner to let those
    special case subclasses call out to the external systems directly.

 *  When you call a method in some other corner of the game, it's less intrusive
    if that method doesn't modify any state. It still creates a coupling, but
    it's a <span name="safe">"safe"</span> coupling because it can't break
    anything in the game.

    <aside name="safe">

    "Safe" is in quotes here because technically, even just accessing data can
    cause problems. If your game is multi-threaded, you could read something at
    the same time that it's being modified. If you aren't careful, you could end up
    with bogus data.

    Another nasty case is if your game state is strictly deterministic (which
    many online games are in order to keep players in sync). If you access
    something outside of the set of synchronized game state, you can cause
    incredibly painful non-determinism bugs.

    </aside>

    Calls that do modify state, on the other hand, more deeply tie you to those
    parts of the codebase, and you need to be much more cognizant of that. That
    makes them good candidates for being rolled up into provided operations in
    the more visible base class.

 *  If the implementation of a provided operation only forwards a call to some
    outside system, then it isn't adding much value. In that case, it may be
    simpler to call the outside method directly.

    However, even simple forwarding can still be useful -- those methods often
    access state that the base class doesn't want to directly expose to
    subclasses. For example, let's say `Superpower` provided this:

    ^code 5

    It's just forwarding the call to some `soundEngine_` field in `Superpower`.
    The advantage, though, is that it keeps that field encapsulated in
    `Superpower` so subclasses can't poke at it.

### Should methods be provided directly, or through objects that contain them?

The challenge with this pattern is that you can end up with a painfully large
number of methods crammed into your base class. You can mitigate that by moving
some of those methods over to other classes. The provided operations in the base
class then just return one of those objects.

For example, to let a power play sounds, we could add these directly to
`Superpower`:

^code 6

But if `Superpower` is already getting large and unwieldy, we might want to
avoid that. Instead, we create a `SoundPlayer` class that exposes that
functionality:

^code 7

Then `Superpower` provides access to it:

^code 8

Shunting provided operations into auxiliary classes like this can do a few
things for you:

 *  *It reduces the number of methods in the base class.* In the example here,
    we went from three methods to just a single getter.

 *  *Code in the helper class is usually easier to maintain.* Core base classes
    like `Superpower`, despite our best intentions, tend to be tricky to change
    since so much depends on them. By moving functionality over to a less
    coupled secondary class, we make that code easier to poke at without
    breaking things.

 *  *It lowers the coupling between the base class and other systems.* When
    `playSound()` was a method directly on `Superpower`, our base
    class was directly tied to `SoundId` and whatever audio code the
    implementation called into. Moving that over to `SoundPlayer` reduces
    `Superpower`'s coupling to the single `SoundPlayer` class, which then
    encapsulates all of its other dependencies.

### How does the base class get the state that it needs?

Your base class will often need some data that it wants to encapsulate and keep
hidden from its subclasses. In our first example, the `Superpower` class
provided a `spawnParticles()` method. If the implementation of that needs some
particle system object, how would it get one?

 *  **Pass it to the base class constructor:**

    The simplest solution is to have the base class take it as a constructor
    argument:

    ^code pass-to-ctor-base

    This safely ensures that every superpower does have a particle system by the
    time it's constructed. But let's look at a derived class:

    ^code pass-to-ctor-sub

    Here we see the problem. Every derived class will need to have a constructor
    that calls the base class one and passes along that argument. That exposes
    every derived class to a piece of state that we don't want them to know
    about.

    This is also a maintenance headache. If we later add another piece of state
    to the base class, every constructor in each of our derived classes will
    have to be modified to pass it along.

 *  **Do two-stage initialization:**

    To avoid passing everything through the constructor, we can split
    initialization into two steps. The constructor will take no parameters and
    just create the object. Then, we call a separate method defined directly on
    the base class to pass in the rest of the data that it needs:

    ^code 9

    Note here that since we aren't passing anything into the constructor for
    `SkyLaunch`, it isn't coupled to anything we want to keep private in
    `Superpower`. The trouble with this approach, though, is that you have to
    make sure you always remember to call `init()`. If you ever forget, you'll
    have a power that's in some twilight half-created state and won't work.

    You can fix that by encapsulating the entire process into a single function,
    like so:

    <span name="friend"></span>

    ^code 10

    <aside name="friend">

    With a little trickery like private constructors and friend classes, you can
    ensure this `createSkylaunch()` function is the *only* function that can
    actually create powers. That way, you can't forget any of the initialization
    stages.

    </aside>

 *  **Make the state static:**

    In the previous example, we were initializing each `Superpower` *instance*
    with a particle system. That makes sense when every power needs its own
    unique state. But let's say that the particle system is a <a class="pattern"
    href="singleton.html">Singleton</a>, and every power will be sharing the
    same state.

    In that case, we can make the state private to the base class and also make
    it <span name="singleton">*static*</span>. The game will still have to make
    sure that it initializes the state, but it only has to initialize the
    `Superpower` *class* once for the entire game, and not each instance.

    <aside name="singleton">

    Keep in mind that this still has many of the problems of a singleton. You've
    got some state shared between lots and lots of objects (all of the
    `Superpower` instances). The particle system is encapsulated, so it isn't
    globally *visible*, which is good, but it can still make reasoning about
    powers harder because they can all poke at the same object.

    </aside>

    ^code 11

    Note here that `init()` and `particles_` are both static. As long as the
    game calls `Superpower::init()` once early on, every power can access the
    particle system. At the same time, `Superpower` instances can be created
    freely by calling the right derived class's constructor.

    Even better, now that `particles_` is a *static* variable, we don't have to
    store it for each instance of `Superpower`, so we've made the class use less
    memory.

 *  **Use a service locator:**

    The previous option requires that outside code specifically remembers to push
    in the state that the base class needs before it needs it. That places the
    burden of initialization on the surrounding code. Another option is to let
    the base class handle it by pulling in the state it needs. One way to do
    that is by using the <a class="pattern" href="service-locator.html">Service
    Locator</a> pattern:

    ^code 12

    Here, `spawnParticles()` needs a particle system. Instead of being *given*
    one by outside code, it fetches one itself from the service locator.

## See Also

 *  When you apply the <a class="pattern" href="update-method.html">Update
    Method</a> pattern, your update method will often also be a sandbox method.

 *  This pattern is a role reversal of the <a class="gof-pattern"
    href="http://en.wikipedia.org/wiki/Template_method_pattern">Template
    Method</a> pattern. In both patterns, you implement a method using a set of
    primitive operations. With Subclass Sandbox, the method is in the derived
    class and the primitive operations are in the base class. With Template
    Method, the *base* class has the method and the primitive operations are
    implemented by the *derived* class.

 *  You can also consider this a variation on the <a class="gof-pattern"
    href="http://en.wikipedia.org/wiki/Facade_Pattern">Facade</a> pattern. That
    pattern hides a number of different systems behind a single simplified API.
    With Subclass Sandbox, the base class acts as a facade thats hides the
    entire game engine from the subclasses.
