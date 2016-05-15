^title Command
^section Design Patterns Revisited

^title Command 패턴
^section 디자인 패턴 다시보기

Command is one of my favorite patterns. Most large programs I write, games or
otherwise, end up using it somewhere. When I've used it in the right place, it's
neatly untangled some really gnarly code. For such a swell pattern, the Gang of
Four has a predictably abstruse description:

Command 패턴은 내가 좋아하는 패턴 중 하나입니다. 내가 만든 게임이나 다른 큰 프로그램 어딘가에
이 패턴을 사용하였습니다. 이 패턴을 적재적소에 사용하면, 좋지 못한 코드를 깔끔하게 풀어줍니다.
Gang of Four는 이 멋진 패턴에 대해서 다음과 같은 난해한 표현을 하였습니다.

> Encapsulate a request as an object, thereby letting users parameterize clients
> with different requests, queue or log requests, and support undoable
> operations.


> Request를 Object로 요약하고, 사용자에게
> Client의 다양한 Request, Queue 혹은 Log Request를
> 인자화함으로써, Request를 Object로 요약하고,
> 실행할 수 없는 Operation를 지원할 수 있다.

I think we can all agree that that's a terrible sentence. First of all, it
mangles whatever metaphor it's trying to establish. Outside of the weird world
of software where words can mean anything, a "client" is a *person* -- someone
you do business with. Last I checked, human beings can't be "parameterized".

나는 우리 모두가 이 흉측한 문장에 동의할 수 있다고 생각합니다. 무엇보다도 어떤 비유를 하려고
했는지는 모르지만 그것은 의미를 훼손시키고 있습니다. 이상한 소프트웨어 바깥 세계에서는 단어가
다양한 의미를 지닐 수 있습니다. 예를 들면 "client"는 우리가 함께 사업을 진행하는 *사람*이 됩니다.
마지막으로 내가 알아본바로는 인간은 "인자화" 될 수 없습니다.

Then, the rest of that sentence is just a list of stuff you could maybe possibly
use the pattern for. Not very illuminating unless your use case happens to be in
that list. *My* pithy tagline for the Command pattern is:

그리고 저 문장의 나머지 부분은 Command 패턴을 사용하면 활용할 수 있는 딱딱한 목록들입니다.
당신이 사용하고자 하는 상황이 저 목록들에 있더라도 너무 환상을 가지지는 말아주십시오.
Command 패턴이 무엇인지 나의 간결한 표현은 다음과 같습니다.

**A command is a *<span name="latin">reified</span> method call*.**

**Command 패턴은 <span name="latin">구체화</span>된 메소드 호출이다.**

<aside name="latin">

"Reify" comes from the Latin "res", for "thing", with the English suffix
"&ndash;fy". So it basically means "thingify", which, honestly, would be a more
fun word to use.

</aside>

Of course, "pithy" often means "impenetrably terse", so this may not be much of
an improvement. Let me unpack that a bit. "Reify", in case you've never heard
it, means "make real". Another term for reifying is making something "first-class".

물론, "간결한"은 종종 "눈 앞에 보이지 않을 정도로 간결함"을 의미합니다.
그래서 나의 간결한 표현이 큰 발전은 아니라고 생각합니다. 다시 본론으로 돌아가서,
"구체화된"은 "현실적으로 만든다"는 것을 의미합니다. "구체화된"의 또 다른은 의미는
무엇가를 "first-class"는 의미입니다.

<aside name="reflection">

*Reflection systems* in some languages let you work with the types in your
program imperatively at runtime. You can get an object that represents the class
of some other object, and you can play with that to see what the type can do. In
other words, reflection is a *reified type system*.

*Reflection systems*은 어떤 컴퓨터 언어에서는 부득이하게 당신의 프로그램 내의 자료형을
런타임으로만 처리할 수 있습니다. 여러분은 어떤 오브젝트의 클래스를 나타내는 클래스를 얻을 수
있습니다. 그리고 여러분은 어떤 기능을 할 수 있을지 알아볼 수 있습니다.
다시말해 reflection은 *구체화된 자료형 시스템*입니다.

</aside>

Both terms mean taking some <span name="reflection">*concept*</span> and turning
it into a piece of *data* -- an object -- that you can stick in a variable, pass
to a function, etc. So by saying the Command pattern is a "reified method call",
what I mean is that it's a method call wrapped in an object.

두 용어는 어떤 <span name="reflection">컨셉</span>을 가지고 그것을 오브젝트와 같은 변수와 같은 곳에 집어넣거나 함수를통해서 *데이터*로 바꿉니다. 그래서 Command 패턴을 "reified method call"이라고 할 때는 나는 method call이
오브젝트 안에 랩핑된 것을 의미합니다.

That sounds a lot like a "callback", "first-class function", "function pointer",
"closure", or "partially applied function" depending on which language you're
coming from, and indeed those are all in the same ballpark. The Gang of Four
later says:

> Commands are an object-oriented replacement for callbacks.

> Command들은 callback을 위한 오브젝트 지향의 교체입니다.

That would be a better slugline for the pattern than the one they chose.

But all of this is abstract and nebulous. I like to start chapters with
something concrete, and I blew that. To make up for it, from here on out it's
all examples where commands are a brilliant fit.

하지만 이런 표현들은 추상적이고 애매모호합니다. 나는 구체적인 사실에 기반하여 이 챕터를 시작하고자 합니다. 이를 위해서 모든 예제들은 Command들이 훌륭하게 적용되는 곳입니다.

## Configuring Input

## 입력 설정하기

Somewhere in every game is a chunk of code that reads in raw user input --
button presses, keyboard events, mouse clicks, whatever. It takes each input and
translates it to a meaningful action in the game:

버튼 눌려지거나, 키보드 이벤트, 마우스 클릭 등의 입력을 읽어들이는 코드 부분이 모든 게임에나
있기 마련입니다. 그 코드는 각각의 입력을 받고, 게임 내에서 의미있는 행동으로 번역합니다.

<img src="images/command-buttons-one.png" alt="A controller, with A mapped to swapWeapon(), B mapped to lurch(), X mapped to jump(), and Y mapped to fireGun()." />

A dead simple implementation looks like:

위 그림을 단순하게 완성하면 다음과 같습니다.

<span name="lurch"></span>

^code handle-input

<aside name="lurch">

Pro tip: Don't press B very often.
Pro tip: B 버튼을 너무 자주 누르지 말 것.

</aside>

This function typically gets called once per frame by the <a class="pattern"
href="game-loop.html">Game Loop</a>, and I'm sure you can figure out what it
does. This code works if we're willing to hard-wire user inputs to game actions,
but many games let the user *configure* how their buttons are mapped.

이 함수는 <a class="pattern" href="game-loop.html">게임 루프</a>에 의해 매 프레임마다 한번 호출되고, 이것을 어떤 기능을 하는지 어렵지 않게 알 수 있을 것입니다. 이 코드는 우리가 게임 액션을 위해 입력하면 당연히 실행될 것입니다. 하지만 많은 게임은 사용자들이 버튼에 따라 어떤 행동을 할지 *설정*하도록 해줍니다.

To support that, we need to turn those direct calls to `jump()` and `fireGun()`
into something that we can swap out. "Swapping out" sounds a lot like assigning
a variable, so we need an *object* that we can use to represent a game action.
Enter: the Command pattern.

We define a base class that represents a triggerable game command:

우리는 액션을 발생시키는 게임 Command를 나타내는 기본 클래스를 다음과 같이 정의합니다.

<span name="one-method"></span>

^code command

<aside name="one-method">

When you have an interface with a single method that doesn't return anything,
there's a good chance it's the Command pattern.

만약 아무것도 리턴하지 않는 하나의 메소드로 구성된 인터페이스가 있다면, 이는 Command 패턴을 사용할 수 있는 좋은 기회입니다. 바로 위와 같은 경우입니다.

</aside>

Then we create subclasses for each of the different game actions:

그리고 우리는 각각 다른 게임 액션을 위한 하위 클래스를 만듭니다.

^code command-classes

In our input handler, we store a pointer to a command for each button:

우리의 InputHandler에 각각의 버튼이 나타내는 Command를 가리키는 포인터 변수를 저장해야 합니다.

^code input-handler-class

Now the input handling just delegates to those:

이제 입력 핸들링은 단지 각각의 Command에 분배만 합니다.

<span name="null"></span>

^code handle-input-commands

<aside name="null">

Notice how we don't check for `NULL` here? This assumes each button will have
*some* command wired up to it.

우리는 여기서 `NULL`을 확인하지 않았다는 것을 확인했습니까? 이는 각각의 버튼이 *어떤* Command를 위해서 사용되고 있다는 것을 가정하였습니다.

If we want to support buttons that do nothing without having to explicitly check
for `NULL`, we can define a command class whose `execute()` method does nothing.
Then, instead of setting a button handler to `NULL`, we point it to that object.
This is a pattern called [Null
Object](http://en.wikipedia.org/wiki/Null_Object_pattern).

만약 우리가 명시적으로 `NULL`을 확인하지 않고 아무런 게임 액션도 하지 않는 버튼을 지원하고
싶다면, 아무것도 행하지 않는 `execute()` 함수를 가지는 command 클래스를 정의할 수 있습니다. 그래서
`NULL`을 위한 버튼 핸들러를 설정하는 대신에, 우리는 우리는 그런 오브젝트를 포인터를 가지고
가리기기만 하면 된다. 이런 패턴은 [Null Object](http://en.wikipedia.org/wiki/Null_Object_pattern)라고 부릅니다.

</aside>

Where each input used to directly call a function, now there's a layer of
indirection:

각각의 입력이 직접적으로 함수를 호출하는 방식에서, 이제 간접적인 계층이 생겼습니다.

<img src="images/command-buttons-two.png" alt="A controller, with each button mapped to a corresponding 'button_' variable which in turn is mapped to a function." />

This is the Command pattern in a nutshell. If you can see the merit of it
already, consider the rest of this chapter a bonus.

이것이 바로 Command 패턴을 간단하게 표현한 것 입니다. 만약에 이 패턴의 장점을 벌써 알았다면, 챕터의 남은 부분은 보너스로 생각해도 됩니다.

## Directions for Actors

## 배우들을 위한 명령

The command classes we just defined work for the previous example, but they're
pretty limited. The problem is that they assume there are these top-level
`jump()`, `fireGun()`, etc. functions that implicitly know how to find the
player's avatar and make him dance like the puppet he is.

방금의 예제에서 우리가 정의한 Command 클래스는 완전하지 않습니다. 문제는 Command 클래스는
플레이어의 아바타를 어떻게 찾고, 아바타를 꼭두각시처럼 춤을 추게 만드는 `jump()`, `fireGun()`과 같은
가장 높은 층위의 함수를 가정하고 있다는 점입니다.

That assumed coupling limits the usefulness of those commands. The *only* thing
the `JumpCommand` can make jump is the player. Let's loosen that restriction.
Instead of calling functions that find the commanded object themselves, we'll
*pass in* the object that we want to order around:

이는 커플링이 Command의 유용성을 제한하고 있다고 가정합니다. `JumpCommand`가 할 수 있는
*유일*한 것은 플레이어를 점프하도록 하는 것입니다. 이런 부족한 점을 보안해봅시다. 명령된
오브젝트를 호출하는 함수가 스스로 찾는 대신에, 우리는 명령하고자 하는 Command를 오브젝트에
전달하는 것입니다.

^code actor-command

Here, `GameActor` is our "game object" class that represents a character in the
game world. We pass it in to `execute()` so that the derived command can invoke
methods on an actor of our choice, like so:

`GameActor`는 게임 세계에서 케릭터를 나타내는 "game object" 클래스입니다. 우리는 `GameActor`를
`execute()` 함수에 전달합니다. 그러면 파생된 Command 클래스들은 GameActor에 대한 메소드를
호출할 수 있습니다. 다음과 같습니다.

^code jump-actor

Now, we can use this one class to make any character in the game hop around.
We're just missing a piece between the input handler and the command that takes
the command and invokes it on the right object. First, we change `handleInput()`
so that it *returns* commands:

이제 우리는 이 클래스를 게임 내 어떤 케릭터에도 사용할 수 있습니다. 우리는 잠시 input hadler와
Command 사이에서 command를 받아서 적합한 오브젝트를 호출 사이의 조각을 잃어버렸습니다.
먼저, 우리는 Command를 *리턴*할 수 있도록 `handleInput()`을 바꿔야 합니다.

^code handle-input-return

It can't execute the command immediately since it doesn't know what actor to
pass in. Here's where we take advantage of the fact that the command is a
reified call -- we can *delay* when the call is executed.

즉시 Command를 실행시킬 수는 없습니다. 왜냐하면 어떤 actor가 전달되었는지 모르기 때문입니다.
이것이 바로 Command가 구체화된 호출이라는 사실을 사용하는 부분입니다. 우리는 호출이 실행되는
것을 *지연*시킬 수 있습니다.

Then, we need some code that takes that command and runs it on the actor
representing the player. Something like:

그러면 우리는 command를 받아서 플레이어를 나타내는 actor를 실행시키는 코드를 추가해야 합니다.
다음과 같습니다.

^code call-actor-command

Assuming `actor` is a reference to the player's character, this correctly drives
him based on the user's input, so we're back to the same behavior we had in the
first example. But adding a layer of indirection between the command and the
actor that performs it has given us a neat little ability: *we can let the
player control any actor in the game now by changing the actor we execute
the commands on.*

In practice, that's not a common feature, but there is a similar use case that
*does* pop up frequently. So far, we've only considered the player-driven
character, but what about all of the other actors in the world? Those are driven
by the game's AI. We can use this same command pattern as the interface between
the AI engine and the actors; the AI code simply emits `Command` objects.

The decoupling here between the AI that selects commands and the actor code
that performs them gives us a lot of flexibility. We can use different AI
modules for different actors. Or we can mix and match AI for different kinds of
behavior. Want a more aggressive opponent? Just plug-in a more aggressive AI to
generate commands for it. In fact, we can even bolt AI onto the *player's*
character, which can be useful for things like demo mode where the game needs to
run on auto-pilot.

<span name="queue">By</span> making the commands that control an actor
first-class objects, we've removed the tight coupling of a direct method call.
Instead, think of it as a queue or stream of commands:

<aside name="queue">

For lots more on what queueing can do for you, see <a href="event-queue.html"
class="pattern">Event Queue</a>.

</aside>

<span name="stream"></span>

<img src="images/command-stream.png" alt="A pipe connecting AI to Actor." />

<aside name="stream">

Why did I feel the need to draw a picture of a "stream" for you? And why does it
look like a tube?

</aside>

Some code (the input handler or AI) <span name="network">produces</span>
commands and places them in the stream. Other code (the dispatcher or actor
itself) consumes commands and invokes them. By sticking that queue in the
middle, we've decoupled the producer on one end from the consumer on the other.

<aside name="network">

If we take those commands and make them *serializable*, we can send the stream
of them over the network. We can take the player's input, push it over the
network to another machine, and then replay it. That's one important piece of
making a networked multi-player game.

</aside>

## Undo and Redo

The final example is the most well-known use of this pattern. If a command object
can *do* things, it's a small step for it to be able to *undo* them. Undo is
used in some strategy games where you can roll back moves that you didn't like.
It's *de rigueur* in tools that people use to *create* games. The <span
name="hate">surest way</span> to make your game designers hate you is giving
them a level editor that can't undo their fat-fingered mistakes.

<aside name="hate">

I may be speaking from experience here.

</aside>

Without the Command pattern, implementing undo is surprisingly hard. With it,
it's a piece of cake. Let's say we're making a single-player, turn-based game and
we want to let users undo moves so they can focus more on strategy and less on
guesswork.

We're conveniently already using commands to abstract input handling, so every
move the player makes is already encapsulated in them. For example, moving a
unit may look like:

^code move-unit

Note this is a little different from our previous commands. In the last example,
we wanted to *abstract* the command from the actor that it modified. In this
case, we specifically want to *bind* it to the unit being moved. An instance of
this command isn't a general "move something" operation that you could use in a
bunch of contexts; it's a specific concrete move in the game's sequence of
turns.

This highlights a variation in how the Command pattern gets implemented. In some
cases, like our first couple of examples, a command is a reusable object that
represents a *thing that can be done*. Our earlier input handler held on to a
single command object and called its `execute()` method anytime the right button
was pressed.

Here, the commands are more specific. They represent a thing that can be done at
a specific point in time. This means that the input handling code will be <span
name="free">*creating*</span> an instance of this every time the player chooses
a move. Something like:

^code get-move

<aside name="free">

Of course, in a non-garbage-collected language like C++, this means the code
executing commands will also be responsible for freeing their memory.

</aside>

The fact that commands are one-use-only will come to our advantage in a second.
To make commands undoable, we define another operation each command class needs
to implement:

^code undo-command

An `undo()` method reverses the game state changed by the corresponding
`execute()` method. Here's our previous move command with undo support:

^code undo-move-unit

Note that we added some <span name="memento">more state</span> to the class.
When a unit moves, it forgets where it used to be. If we want to be able to undo
that move, we have to remember the unit's previous position ourselves, which is
what `xBefore_` and `yBefore_` do.

<aside name="memento">

This seems like a place for the <a
href="http://en.wikipedia.org/wiki/Memento_pattern"
class="gof-pattern">Memento</a> pattern, but I haven't found it to work well.
Since commands tend to modify only a small part of an object's state,
snapshotting the rest of its data is a waste of memory. It's cheaper to
manually store only the bits you change.

<a href="http://en.wikipedia.org/wiki/Persistent_data_structure">*Persistent
data structures*</a> are another option. With these, every modification to an
object returns a new one, leaving the original unchanged. Through clever
implementation, these new objects share data with the previous ones, so it's
much cheaper than cloning the entire object.

Using a persistent data structure, each command stores a reference to the
object before the command was performed, and undo just means switching back to
the old object.

</aside>

To let the player undo a move, we keep around the last command they executed.
When they bang on Control-Z, we call that command's `undo()` method. (If they've
already undone, then it becomes "redo" and we execute the command again.)

Supporting multiple levels of undo isn't much harder. Instead of remembering the
last command, we keep a list of commands and a reference to the "current" one.
When the player executes a command, we append it to the list and point "current"
at it.

<img src="images/command-undo.png" alt="A stack of commands from older to newer. A 'current' arrow points to one command, an 'undo' arrow points to the previous one, and 'redo' points to the next." />

When the player chooses "Undo", we undo the current command and move the current
pointer back. When they choose <span name="replay">"Redo"</span>, we advance the
pointer
and then execute that command. If they choose a new command after undoing some,
everything in the list after the current command is discarded.

The first time I implemented this in a level editor, I felt like a genius. I was
astonished at how straightforward it was and how well it worked. It takes
discipline to make sure every data modification goes through a command, but once
you do that, the rest is easy.

<aside name="replay">

Redo may not be common in games, but re-*play* is. A naïve implementation would
record the entire game state at each frame so it can be replayed, but that would
use too much memory.

Instead, many games record the set of commands every entity performed each
frame. To replay the game, the engine just runs the normal game simulation,
executing the pre-recorded commands.

</aside>

## Classy and Dysfunctional?

Earlier, I said commands are similar to first-class functions or closures, but
every example I showed here used class definitions. If you're familiar with
functional programming, you're probably wondering where the functions are.

I wrote the examples this way because C++ has pretty limited support for
first-class functions. Function pointers are stateless, functors are weird and
still
require defining a class, and the lambdas in C++11 are tricky to work with
because of manual memory management.

That's *not* to say you shouldn't use functions for the Command pattern in other
languages. If you have the luxury of a language with real closures, by all means,
use them! In <span name="some">some</span> ways, the Command pattern is a way of
emulating closures in languages that don't have them.

<aside name="some">

I say *some* ways here because building actual classes or structures for
commands is still useful even in languages that have closures. If your command
has multiple operations (like undoable commands), mapping that to a single
function is awkward.

Defining an actual class with fields also helps readers easily tell what data
the command contains. Closures are a wonderfully terse way of automatically
wrapping up some state, but they can be so automatic that it's hard to see what
state they're actually holding.

</aside>

For example, if we were building a game in JavaScript, we could create a move
unit command just like this:

    :::javascript
    function makeMoveUnitCommand(unit, x, y) {
      // This function here is the command object:
      return function() {
        unit.moveTo(x, y);
      }
    }

We could add support for undo as well using a pair of closures:

    :::javascript
    function makeMoveUnitCommand(unit, x, y) {
      var xBefore, yBefore;
      return {
        execute: function() {
          xBefore = unit.x();
          yBefore = unit.y();
          unit.moveTo(x, y);
        },
        undo: function() {
          unit.moveTo(xBefore, yBefore);
        }
      };
    }

If you're comfortable with a functional style, this way of doing things is
natural. If you aren't, I hope this chapter helped you along the way a bit. For
me, the usefulness of the Command pattern really shows how effective the
functional paradigm is for many problems.

## See Also

 *  You may end up with a lot of different command classes. In order to make it
    easier to implement those, it's often helpful to define a concrete base
    class with a bunch of convenient high-level methods that the derived
    commands can compose to define their behavior. That turns the command's main
    `execute()` method into a <a href="subclass-sandbox.html"
    class="pattern">Subclass Sandbox</a>.

 *  In our examples, we explicitly chose which actor would handle a command. In
    some cases, especially where your object model is hierarchical, it may not
    be so cut-and-dried. An object may respond to a command, or it may decide to
    pawn it off on some subordinate object. If you do that, you've got yourself
    a <a class="gof-pattern" href="
    http://en.wikipedia.org/wiki/Chain-of-responsibility_pattern">Chain of Responsibility</a>.

 *  Some commands are stateless chunks of pure behavior like the `JumpCommand`
    in the first example. In cases like that, having <span
    name="singleton">more</span> than one instance of that class wastes memory
    since all instances are equivalent. The <a class="gof-pattern"
    href="flyweight.html">Flyweight</a> pattern addresses that.

    <aside name="singleton">

    You could make it a <a href="singleton.html" class="gof-
    pattern">Singleton</a> too, but friends don't let friends create singletons.

    </aside>
