^title State
^section Design Patterns Revisited

^title State 패턴
^section 디자인 패턴 다시보기

Confession time: I went a little overboard and packed way too much into this
chapter. It's ostensibly about the <a
href="http://en.wikipedia.org/wiki/State_pattern" class="gof-pattern">State</a>
design pattern, but I can't talk about that and games without going into the
more fundamental concept of *finite state machines* (or "FSMs"). But then once I
went there, I figured I might as well introduce *hierarchical state machines*
and *pushdown automata*.

자백의 시간입니다. 나는 이 챕터에 조금 흥분했고, 너무 많은 것을 이 챕터에 집어넣었습니다. 표면상 <a
href="http://en.wikipedia.org/wiki/State_pattern" class="gof-pattern">스테이트 디자인 패턴</a>이 그런 것이지만, 나는 *finite state machines (FSM)* (유한 상태 기계)에 대한 기본적인 개념을 설명하지 않고서 게임에 대해서 그 어떤 말도 할 수 없습니다. 나는 *hierarchical state machines*과 *pushdown automata*에 대해서도 소개하기로 결정했습니다.

That's a lot to cover, so to keep things as short as possible, the code samples
here leave out a few details that you'll have to fill in on your own. I hope
they're still clear enough for you to get the big picture.

그래서 다뤄야 할 부분이 많습니다. 가능하다면 줄일 것입니다. 그래서 여기의 샘플 코드는 조금 자세한 부분은 여러분이 직접 채워야 할 것입니다. 나는 여러분이 큰 그림은 충분히 이해할 것이라고 기대합니다.

Don't feel sad if you've never heard of a state machine. While well known to
<span name="two-camps">AI and compiler</span> hackers, they aren't that familiar
to other programming circles. I think they should be more widely known, so I'm
going to throw them at a different kind of problem here.

만약 여러분이 state machine에 대해서 들어본 적이 없다고 해도 슬퍼할 필요는 없습니다. <span name="two-camps">AI와 컴파일러</a> 해커에 대해서 잘 아는 반면에, AI와 컴파일러 헤커들은 다른 프로그래밍 분야와 친숙하지 않습니다. 나는 AI와 컴파일러 해커도 좀더 널리 알려져야한다고 생각합니다. 그래서 나는 다른 종류의 문제를 제기할 것입니다.

<aside name="two-camps">

This pairing echoes the early days of artificial intelligence. In the &rsquo;50s and
&rsquo;60s, much of AI research was focused on language processing. Many of the
techniques compilers now use for parsing programming languages were invented for
parsing human languages.

이런 한 쌍만들기는 인공 지능의 초기에 자주 반복되었습니다. 1950년대와 60년대에는 수많은 AI연구가 언어 처리에 집중되어 있었습니다. 현재 프로그램 언어를 파싱하기위한 컴파일러 기술은 인간 언어를 파싱하기 위해서 발명되었습니다.

</aside>

## We've All Been There

## 우리 모두 경험이 있어요

We're working on a little side-scrolling platformer. Our job is to implement the
heroine that is the player's avatar in the game world. That means making her
respond to user input. Push the B button and she should jump. Simple enough:

우리는 간단한 횡스크롤 게임을 만들고 있습니다. 우리의 일은 게임 세계에 있는 플레이어의 아바타인 여주인공 만들기입니다. 사용자의 입력에 여주인공을 반응하도록 해야합니다.  B 버튼을 누르면 여주인공은 점프해야 합니다. 단순합니다.

^code spaghetti-1

Spot the bug?

버그를 발견했나요?

There's nothing to prevent "air jumping" -- keep hammering B while she's in the
air, and she will float forever. The simple <span name="landing">fix</span> is to
add an `isJumping_` Boolean field to `Heroine` that tracks when she's jumping,
and then do:

"공중 점프"를 방지할 수 있는 것이 아무것도 없습니다. 여주인공이 공중에 떠 있는 동안에 B 버튼을 연타한다면 여주인공은 영원이 둥둥 떠다닐 것입니다. 여주인공의 점프 상태를 알 수 있는 Boolean 변수 `isJumping_`을 `여주인공`에 추가하면 <span name="landing">고칠 수</span> 있습니다.

^code spaghetti-2

<aside name="landing">

There should also be code that sets `isJumping_` back to `false` when the
heroine touches the ground. I've omitted that here for brevity's sake.

여주인공이 땅에 닿으면 `isJumping_`을 다시 `false` 상태로 변경하는 코드가 필요합니다. 여기선 코드를 간결하게 하기 위해서 생략하였습니다.

</aside>

Next, we want the heroine to duck if the player presses down while she's on the
ground and stand back up when the button is released:

다음은 플레이어가 땅에 닿아있는 동안에 DOWN 키를 누르면 여주인공을 웅크리게 만들고 싶습니다. DOWN 키에서 손을 떼면 여주인공은 다시 일어섭니다.

^code spaghetti-3

Spot the bug this time?

버그를 찾았습니까?

With this code, the player could:

이 코드로는 다음처럼 할 수 있습니다.

1. Press down to duck.
2. Press B to jump from a ducking position.
3. Release down while still in the air.

--

1. 웅크리기 위해서 DOWN키를 누릅니다.
2. 웅크린 상태에서 점프를 하기 위해서 B키를 누릅니다.
3. 공중에서 DOWN키에 손을 뗍니다.

The heroine will switch to her standing graphic in the middle of the jump. Time
for another flag...

여주인공은 점프하는 상태에서 일어서는 그래픽으로 바뀔 것입니다. 다른 flag가 필요해집니다...

^code spaghetti-4

Next, it would be cool if the heroine did a dive attack if the player presses
down in the middle of a jump:

다음, 여주인공이 점프한 상태에서 DOWN키를 누르면 다이브 공격을 할 수 있으면 멋있을 것 같습니다.

^code spaghetti-5

Bug hunting time again. Find it?

버그 사냥 시간입니다. 찾았습니까?

We check that you can't air jump while jumping, but not while diving. Yet
another field...

우리는 점프하는 중에는 공중 점프를 할 수 없습니다. 하지만 다이빙 중에는 할 수 있습니다. 다른 변수가 또 필요해집니다...

Something is clearly <span name="se">wrong</span> with our approach. Every time
we touch this handful of code, we break something. We need to add a bunch more
moves -- we haven't even added *walking* yet -- but at this rate, it will
collapse into a heap of bugs before we're done with it.

우리의 접근 방식이 확실하게 무언가 <span name="se">잘못되었습니다</span>. 매번 우리가 얼마 되지도 않는 코드를 건드리면, 우리는 무언가 망가뜨립니다. 우리는 추가할 움직임이 아직 더 있습니다. 우리는 아직 *걷기*를 추가하지 않았습니다. 하지만 이런 식으로 우리가 끝내기도 전에 버그를 만들 것입니다.

<aside name="se">

Those coders you idolize who always seem to create flawless code aren't simply
superhuman programmers. Instead, they have an intuition about which *kinds* of
code are error-prone, and they steer away from them.

여러분이 우상화하는, 흠이 전혀 없는 코드를 만드는 것처럼 보이는 코더들은 단순히 초인적인 프로그래머가 아닙니다. 대신에, 그들은 에러를 발생시키기 좋은 코드의 *종류*에 대한 직관력을 가지고 있으며, 그 코드를 피합니다.

Complex branching and mutable state -- fields that change over time -- are two
of those error-prone kinds of code, and the examples above have both.

복잡한 분기와 시간에 따라 변하는 가변적인 상태는 에러를 발생시키기 좋은 코드입니다. 위의 코드는 둘 다 가지고 있습니다.

</aside>

## Finite State Machines to the Rescue

## 유한 상태 기계를 통해 해결하다

In a fit of frustration, you sweep everything off your desk except a pen and
paper and start drawing a flowchart. You draw a box for each thing the heroine
can be doing: standing, jumping, ducking, and diving. When she can respond to a
button press in one of those states, you draw an arrow from that box, label it
with that button, and connect it to the state she changes to.

좌절 속에서 여러분은 펜과 종이를 제외한 모든 것을 치워버리고, 플로우 차트를 그리기 시작합니다. 여러분은 여주인공이 할 수 있는 행동들을 박스로 그립니다. 서기, 점프, 웅크리기, 다이브하기. 여주인공은 이런 상태들 중에서 키가 눌러지면 하나의 반응을 할 수 있습니다. 여러분은 박스에서 화살표를 그릴 수 있습니다. 그리고 화살표에 버튼이름을 붙입시다. 그리고 변할 여주인공의 상태로 연결합니다.

<img src="images/state-flowchart.png" alt="A flowchart containing boxes for Standing, Jumping, Diving, and Ducking. Arrows for button presses and releases connect some of the boxes." />

Congratulations, you've just created a *finite state machine*. These came out of
a branch of computer science called *automata theory* whose family of data
structures also includes the famous Turing machine. FSMs are the simplest member
of that family.

축하합니다. 여러분은 방금 *유한 상태 기계*를 만들었습니다. 유명한 튜링 기계를 포함하는 자료 구조 영역 중 하나인 *오토마타 이론*이라는 컴퓨터 과학의 한 분야에서 나온 것입니다. FSM은 이 영역 중에서 가장 단순한 것입니다.

The <span name="adventure">gist</span> is:

<span name="adventure">요지</span>는 다음과 같습니다.

 *  **You have a fixed *set of states* that the machine can be in.** For our
    example, that's standing, jumping, ducking, and diving.

 *  **기계가 될 수 있는 *상태*를 확정해야 합니다.** 예를들어 우리의 예시에서는 상태는 서기, 점프, 웅크리기, 다이브입니다.

 *  **The machine can only be in *one* state at a time.** Our heroine can't be
    jumping and standing simultaneously. In fact, preventing that is one reason
    we're going to use an FSM.

 *  **기계는 한 번에 *하나*의 상태를 가질 수 있습니다.** 우리의 여주인공은 점프하면서 동시에 서있을 수 없습니다. 사실, 이를 방지하기 위해서 우리는 FSM을 사용합니다.

 *  **A sequence of *inputs* or *events* is sent to the machine.** In our example,
    that's the raw button presses and releases.

 *  ***입력* 혹은 *이벤트*의 연속은 기계로 보내집니다.** 우리의 예시에서 버튼이 눌러졌는지, 떼어졌는지입니다.

 *  **Each state has a *set of transitions*, each associated with an input and
    pointing to a state.** When an input comes in, if it matches a transition for
    the current state, the machine changes to the state that transition points
    to.

    For example, pressing down while standing transitions to the ducking state. Pressing down while jumping transitions to diving. If no transition is defined for an input on the current state, the input is ignored.
    
 *  **각각의 상태는 각각의 입력에 관련되고 상태를 가리키는 *이행 세트*를 가집니다.** 입력이 들어오고, 그 입력이 현재 상태의 이행에 잘 맞다면, 기계는 이행이 가리키는 상태로 변화합니다.
 
 예를 들어, 서있는 동안에 DOWN키를 누르면, 이행은 웅크리는 상태입니다. 점프 중에서 DOWN 키를 누르면, 이행은 다이브입니다. 만약에 현재 상태에서 입력에 대해 정의된 이행이 없다면, 입력은 무시됩니다.

In their pure form, that's the whole banana: states, inputs, and transitions.
You can draw it out like a little flowchart. Unfortunately, the compiler doesn't
recognize our scribbles, so how do we go about *implementing* one? The Gang of
Four's State pattern is one method -- which we'll get to -- but let's start simpler.

여러분은 플로우 차트로 그릴 수 있습니다. 운 나쁘게도 컴파일러는 우리가 끄적거린 것을 이해할 수 없습니다. 그래서 우리는 어떻게 *구현*해야 할까요? GoF의 State 패턴은 하나의 메소드이지만 좀 더 단순하게 시작해봅시다.

<aside name="adventure">

My favorite analogy for FSMs is the old text adventure games like Zork. You have
a world of rooms that are connected to each other by exits. You explore them by
entering commands like "go north".

FSM에 대한 내가 가장 좋아하는 비유는 Zork와 같은 오래된 텍스트 모험 게임입니다. 여러분은 출구들로 연결된 방의 세계를 가지고 있습니다. 여러분은 "북쪽으로 가자"와 같은 커맨드를 입력하면 모험할 수 있습니다.

This maps directly to a state machine: Each room is a state. The room you're in
is the current state. Each room's exits are its transitions. The navigation
commands are the inputs.

이는 상태 기계와 바로 연결할 수 있습니다. 각각의 방은 상태입니다. 여러분이 있는 방은 현재의 상태입니다. 각각의 방의 출구는 이행입니다. 조종 커맨드는 입력입니다.

</aside>

## Enums and Switches

One problem our `Heroine` class has is some combinations of those Boolean fields
aren't valid: `isJumping_` and `isDucking_` should never both be true, for
example. When you have a handful of flags where only one is `true` at a time,
that's a hint that what you really want is an `enum`.

한 가지 문제가 있는데 우리의 여주인공 클래스가 가진 일부 Boolean 필드의 조합들이 유효하지 않다는 것입니다. 예를들어, `isJumping_`과 `isDucking_`는 동시에 `true`일 수 없습니다. 당신이 같은 시간에 오직 하나만 `true`이어야 하는 몇개의 플래그를 가지고 있을 때, `enum`은 니가 정말로 원하는 것에 대한 힌트일 수 있습니다.

In this case, that `enum` is exactly the set of states for our FSM, so let's
define that:

이 케이스에서, `enum`은 정확히 우리의 FSM을 위한 state들의 집합입니다. 이제 정의해봅시다.

^code enum

Instead of a bunch of flags, `Heroine` will just have one `state_` field. We
also flip the order of our branching. In the previous code, we switched on
input, *then* on state. This kept the code for handling one button press
together, but it smeared around the code for one state. We want to keep that
together, so we switch on state first. That gives us:

^code state-switch

This seems trivial, but it's a real improvement over the previous code. We still
have some conditional branching, but we simplified the <span
name="invalid">mutable</span> state to a single field. All of the code for
handling a single state is now nicely lumped together. This is the simplest way
to implement a state machine and is fine for some uses.

<aside name="invalid">

In particular, the heroine can no longer be in an *invalid* state. With the
Boolean flags, some sets of values were possible but meaningless. With the `enum`,
each value is valid.

</aside>

Your problem may outgrow this solution, though. Say we want to add a move where
our heroine can duck for a while to charge up and unleash a special attack.
While she's ducking, we need to track the charge time.

We add a `chargeTime_` field to `Heroine` to store how long the attack has
charged. Assume we already have an <span name="update">`update()`</span> that
gets called each frame. In there, we add:

^code switch-update

<aside name="update">

If you guessed that this is the <a href="update-method.html"
class="pattern">Update Method</a> pattern, you win a prize!

</aside>

We need to reset the timer when she starts ducking, so we modify
`handleInput()`:

^code state-switch-reset

All in all, to add this charge attack, we had to modify two methods and add a
`chargeTime_` field onto `Heroine` even though it's only meaningful while in
the ducking state. What we'd prefer is to have all of that code and data nicely
wrapped up in one place. The Gang of Four has us covered.

## The State Pattern

For people deeply into the object-oriented mindset, every conditional <span
name="branch">branch</span> is an opportunity to use dynamic dispatch (in other
words a virtual method call in C++). I think you can go too far down that
rabbit hole. Sometimes an `if` is all you need.

<aside name="branch">

There's a historical basis for this. Many of the original object-oriented
apostles like *Design Patterns*' Gang of Four, and *Refactoring*'s Martin Fowler
came from Smalltalk. There, `ifThen:` is just a method you invoke on the
condition, which is implemented differently by the `true` and `false` objects.

</aside>

But in our example, we've reached a tipping point where something
object-oriented is a better fit. That gets us to the State pattern. In the words
of the Gang of Four:

> Allow an object to alter its behavior when its internal state changes. The object will appear to change its class.

That doesn't tell us much. Heck, our `switch` does that. The concrete pattern
they describe looks like this when applied to our heroine:

### A state interface

First, we define an interface for the state. Every bit of behavior that is
state-dependent -- every place we had a `switch` before -- becomes a virtual
method in that interface. For us, that's `handleInput()` and `update()`:

^code heroine-state

### Classes for each state

For each state, we define a class that implements the interface. Its methods
define the heroine's behavior when in that state. In other words, take each `case`
from the earlier `switch` statements and move them into their state's class. For example:

^code ducking-state

Note that we also moved `chargeTime_` out of `Heroine` and into the `DuckingState`
class. This is great -- that piece of data is only meaningful while in that state,
and now our object model reflects that explicitly.

### Delegate to the state

Next, we give the `Heroine` a pointer to her current state, lose each big `switch`, and delegate to the state instead:

<span name="delegate"></span>

^code gof-heroine

In order to "change state", we just need to assign `state_` to point to a
different `HeroineState` object. That's the State pattern in its entirety.

<aside name="delegate">

This looks like the <a href="http://en.wikipedia.org/wiki/Strategy_pattern"
class="gof-pattern">Strategy</a> and <a href="type-object.html"
class="pattern">Type Object</a> patterns. In all three, you have a main object
that delegates to another subordinate one. The difference is *intent*.

 *  With Strategy, the goal is to *decouple* the main class from some portion of
    its behavior.

 *  With Type Object, the goal is to make a *number* of objects behave similarly
    by *sharing* a reference to the same type object.

 *  With State, the goal is for the main object to *change* its behavior by
    *changing* the object it delegates to.

</aside>

## Where Are the State Objects?

I did gloss over one bit here. To change states, we need to assign `state_` to
point to the new one, but where does that object come from? With our `enum`
implementation, that was a no-brainer -- `enum` values are primitives like
numbers. But now our states are classes, which means we need an actual instance
to point to. There are two common answers to this:

### Static states

If the state object doesn't have any other <span name="fn">fields</span>, then
the only data it stores is a pointer to the internal virtual method table so
that its methods can be called. In that case, there's no reason to ever have
more than one instance of it. Every instance would be identical anyway.

<aside name="fn">

If your state has no fields and only *one* virtual method in it, you can
simplify this pattern even more. Replace each state *class* with a state
*function* -- just a plain vanilla top-level function. Then, the `state_` field
in your main class becomes a simple function pointer.

</aside>

In that case, you can make a single *static* instance. Even if you have a
bunch of FSMs all going at the same time in that same state, they can all point
to the <span name="flyweight">same instance</span> since it has nothing
machine-specific about it.

<aside name="flyweight">

This is the <a href="flyweight.html" class="gof-pattern">Flyweight</a> pattern.

</aside>

*Where* you put that static instance is up to you. Find a place that makes
sense. For no particular reason, let's put ours inside the base state class:

^code heroine-static-states

Each of those static fields is the one instance of that state that the game
uses. To make the heroine jump, the standing state would do something like:

^code jump

### Instantiated states

Sometimes, though, this doesn't fly. A static state won't work for the ducking
state. It has a `chargeTime_` field, and that's specific to the heroine that
happens to be ducking. This may coincidentally work in our game if there's only
one heroine, but if we try to add two-player co-op and have two heroines on
screen at the same time, we'll have problems.

In that case, we have to <span name="fragment">create</span> a state
object when we transition to it. This lets each FSM have its own instance of the state. Of course, if we're allocating a *new* state, that means we need to free the *current* one. We have to be careful here, since the code that's triggering the change is in a method in the current state. We don't want to delete `this` out from under ourselves.

Instead, we'll allow `handleInput()` in `HeroineState` to optionally return a new state. When it does, `Heroine` will delete the old one and swap in the new one, like so:

^code swap-instance

That way, we don't delete the previous state until we've returned from its method. Now, the standing state can transition to ducking by creating a new instance:

^code duck

When I can, I prefer to use static states since they don't burn memory and CPU cycles allocating objects
each state change. For states that are more, uh, *stateful*, though, this is the
way to go.

<aside name="fragment">

When you dynamically allocate states, you may have to worry about fragmentation.
The <a href="object-pool.html" class="pattern">Object Pool</a> pattern can help.

</aside>

## Enter and Exit Actions

The goal of the State pattern is to encapsulate all of the behavior and data for
one state in a single class. We're partway there, but we still have
some loose ends.

When the heroine changes state, we also switch her sprite. Right now, that code is owned by the state she's switching *from*. When she goes from ducking to standing, the ducking state sets her image:

^code enter-standing-before

What we really want is each state to control its own graphics. We can
handle that by giving the state an *entry action*:

^code standing-with-enter

Back in `Heroine`, we modify the code for handling state changes to call that
on the new state:

^code change-state

This lets us simplify the ducking code to:

^code enter-standing

All it does is switch to standing and the standing state takes care of
the graphics. Now our states really are encapsulated. One particularly nice thing
about entry actions is that they run when you enter the state regardless of
which state you're coming *from*.

Most real-world state graphs have multiple transitions into the same state. For
example, our heroine will also end up standing after she lands a jump or dive. That means we would end up duplicating some code everywhere that transition
occurs. Entry actions give us a place to consolidate that.

We can, of course, also extend this to support an *exit action*. This is just a
method we call on the state we're *leaving* right before we switch to the new
state.

## What's the Catch?

I've spent all this time selling you on FSMs, and now I'm going to pull the rug
out from under you. Everything I've said so far is true, and FSMs are a good fit
for some problems. But their greatest virtue is also their greatest flaw.

State machines help you untangle hairy code by enforcing a very <span
name="turing">constrained</span> structure on it. All you've got is a fixed set
of states, a single current state, and some hardcoded transitions.

<aside name="turing">

A finite state machine isn't even *Turing complete*. Automata theory describes
computation using a series of abstract models, each more complex than the
previous. A *Turing machine* is one of the most expressive models.

"Turing complete" means a system (usually a programming language) is powerful
enough to implement a Turing machine in it, which means all Turing complete
languages are, in some ways, equally expressive. FSMs are not flexible enough
to be in that club.

</aside>

If you try using a state machine for something more complex like game AI, you
will slam face-first into the limitations of that model. Thankfully, our
forebears have found ways to dodge some of those barriers. I'll close this
chapter out by walking you through a couple of them.

## Concurrent State Machines

We've decided to give our heroine the ability to carry a gun. When she's packing
heat, she can still do everything she could before: run, jump, duck, etc. But
she also needs to be able to fire her weapon while doing it.

If we want to stick to the confines of an FSM, we have to *double* the number of
states we have. For each existing state, we'll need another one for doing the
same thing while she's armed: standing, standing with gun, jumping, jumping with
gun, you get the idea.

Add a couple of more weapons and the number of states explodes combinatorially.
Not only is it a huge number of states, it's a huge amount of redundancy: the
unarmed and armed states are almost identical except for the little bit of code
to handle firing.

The problem is that we've <span name="combination">jammed</span> two pieces of
state -- what she's *doing* and what she's *carrying* -- into a single machine.
To model all possible combinations, we would need a state for each *pair*. The
fix is obvious: have two separate state machines.

<aside name="combination">

If we want to cram *n* states for what she's doing and *m* states for what she's
carrying into a single machine, we need *n &times; m* states. With two machines,
it's just *n + m*.

</aside>

We keep our original state machine for what she's doing and leave it alone. Then
we define a separate state machine for what she's carrying. `Heroine` will have
*two* "state" references, one for each, like:

<span name="equip-state"></span>

^code two-states

<aside name="equip-state">

For illustrative purposes, we're using the full State pattern for her equipment.
In practice, since it only has two states, a Boolean flag would work too.

</aside>

When the heroine delegates inputs to the states, she hands it to both of them:

<span name="consume"></span>

^code handle-two-inputs

<aside name="consume">

A more full-featured system would probably have a way for one state machine to
*consume* an input so that the other doesn't receive it. That would prevent both
machines from erroneously trying to respond to the same input.

</aside>

Each state machine can then respond to inputs, spawn behavior, and change its
state independently of the other machine. When the two sets of states are mostly
unrelated, this works well.

In practice, you'll find a few cases where the states do interact. For example,
maybe she can't fire while jumping, or maybe she can't do a dive attack if she's
armed. To handle that, in the code for one state, you'll probably just do some
crude `if` tests on the *other* machine's state to coordinate them. It's not the most
elegant solution, but it gets the job done.

## Hierarchical State Machines

After fleshing out our heroine's behavior some more, she'll likely have a bunch
of similar states. For example, she may have standing, walking, running, and
sliding states. In any of those, pressing B jumps and pressing down ducks.

With a simple state machine implementation, we have to duplicate that code in
each of those states. It would be better if we could implement that once and
reuse it across all of the states.

If this was just object-oriented code instead of a state machine, one way to
share code across those states would be using <span
name="inheritance">inheritance</span>. We could define a class for an "on
ground" state that handles jumping and ducking. Standing, walking, running, and
sliding would then inherit from that and add their own additional behavior.

<aside name="inheritance">

This has both good and bad implications. Inheritance is a powerful means of code
reuse, but it's also a very strong coupling between two chunks of code. It's a big
hammer, so swing it carefully.

</aside>

It turns out, this is a common structure called a *hierarchical state machine*.
A state can have a *superstate* (making itself a *substate*). When an event
comes in, if the substate doesn't handle it, it rolls up the chain of
superstates. In other words, it works just like overriding inherited methods.

In fact, if we're using the State pattern to implement our FSM, we can
use class inheritance to implement the hierarchy. Define a base class for the
superstate:

^code on-ground

And then each substate inherits it:

^code duck-on-ground

This isn't the only way to implement the hierarchy, of course. If you aren't
using the Gang of Four's State pattern, this won't work. Instead, you can model
the current state's chain of superstates explicitly using a *stack* of states
instead of a single state in the main class.

The current state is the one on the top of the stack, under that is its
immediate superstate, and then *that* state's superstate and so on. When you
dish out some state-specific behavior, you start at the top of the stack and
walk down until one of the states handles it. (If none do, you ignore it.)

## Pushdown Automata

There's another common extension to finite state machines that also uses a stack
of states. Confusingly, the stack represents something entirely different, and
is used to solve a different problem.

The problem is that finite state machines have no concept of *history*. You know
what state you *are* in, but have no memory of what state you *were* in. There's
no easy way to go back to a previous state.

Here's an example: Earlier, we let our fearless heroine arm herself to the
teeth. When she fires her gun, we need a new state that plays the firing
animation and spawns the bullet and any visual effects. So we slap together a
`FiringState` and make <span name="shared">all of the states</span> that she can
fire from transition into that when the fire button is pressed.

<aside name="shared">

Since this behavior is duplicated across several states, it may also be a good
place to use a hierarchical state machine to reuse that code.

</aside>

The tricky part is what state she transitions to *after* firing. She can pop off
a round while standing, running, jumping, and ducking. When the firing sequence
is complete, she should transition back to what she was doing before.

If we're sticking with a vanilla FSM, we've already forgotten what state she was
in. To keep track of it, we'd have to define a slew of nearly identical states
-- firing while standing, firing while running, firing while jumping, and so on
-- just so that each one can have a hardcoded transition that goes back to the
right state when it's done.

What we'd really like is a way to *store* the state she was in before firing and
then *recall* it later. Again, automata theory is here to help. The relevant
data structure is called a [*pushdown
automaton*](http://en.wikipedia.org/wiki/Pushdown_automaton).

Where a finite state machine has a *single* pointer to a state, a pushdown
automaton has a *stack* of them. In an FSM, transitioning to a new state
*replaces* the previous one. A pushdown automaton lets you do that, but it also
gives you two additional operations:

 1. You can *push* a new state onto the stack. The "current" state is always the
    one on top of the stack, so this transitions to the new state. But it leaves
    the previous state directly under it on the stack instead of discarding it.

 2. You can *pop* the topmost state off the stack. That state is discarded, and
    the state under it becomes the new current state.

<img src="images/state-pushdown.png" alt="The stack for a pushdown automaton. First it just contains a Standing state. A Firing state is pushed on top, then popped back off when done." />

This is just what we need for firing. We create a *single* firing state. When
the fire button is pressed while in any other state, we *push* the firing state
onto the stack. When the firing animation is done, we *pop* that state off, and
the pushdown automaton automatically transitions us right back to the state we
were in before.

## So How Useful Are They?

Even with those common extensions to state machines, they are still pretty
limited. The trend these days in game AI is more toward exciting things like
*[behavior trees][]* and *[planning systems][]*. If complex AI is what you're interested in,
all this chapter has done is whet your appetite. You'll want to read other books
to satisfy it.

[behavior trees]: http://web.archive.org/web/20140402204854/http://www.altdevblogaday.com/2011/02/24/introduction-to-behavior-trees/
[planning systems]: http://web.media.mit.edu/~jorkin/goap.html

This doesn't mean finite state machines, pushdown automata, and other simple
systems aren't useful. They're a good modeling tool for certain kinds of
problems. Finite state machines are useful when:

 *  You have an entity whose behavior changes based on some internal state.

 *  That state can be rigidly divided into one of a relatively small number of
    distinct options.

 *  The entity responds to a series of inputs or events over time.

In games, they are most known for being used in AI, but they are also common in
implementations of user input handling, navigating menu screens, parsing text,
network protocols, and other asynchronous behavior.
