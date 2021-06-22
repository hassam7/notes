## Inheritance versus Composition

The two most common techniques for reusing functionality in object-oriented
systems are class inheritance and object composition. As we've explained, class
inheritance lets you define the implementation of one class in terms of another's.
Reuse by subclassing is often referred to as white-box reuse. The term "white-box"
refers to visibility: With inheritance, the internals of parent classes are often
visible to subclasses.


Object composition is an alternative to class inheritance. Here, new functionality
is obtained by assembling or composing objects to get more complex functionality.
Object composition requires that the objects being composed have well-defined
interfaces. This style of reuse is called black-box reuse, because no internal
details of objects are visible. Objects appear only as "black boxes."

Inheritance and composition each have their advantages and disadvantages. Class
inheritance is defined statically at compile-time and is straightforward to use,
since it's supported directly by the programming language. Class inheritance also
makes it easier to modify the implementation being reused. When a subclass
overrides some but not all operations, it can affect the operations it inherits
as well, assuming they call the overridden operations.


But class inheritance has some disadvantages, too. First, you can't change the
implementations inherited from parent classes at run-time, because inheritance
is defined at compile-time. Second, and generally worse, parent classes often
define at least part of their subclasses' physical representation. Because
inheritance exposes a subclass to details of its parent's implementation, it's
often said that "inheritance breaks encapsulation" [Sny86]. The implementation
of a subclass becomes so bound up with the implementation of its parent class
that any change in the parent's implementation will force the subclass to change.


Implementation dependencies can cause problems when you're trying to reuse a
subclass. Should any aspect of the inherited implementation not be appropriate
for new problem domains, the parent class must be rewritten or replaced by something
more appropriate. This dependency limits flexibility and ultimately reusability.
One cure for this is to inherit only from abstract classes, since they usually
provide little or no implementation.

Object composition is defined dynamically at run-time through objects acquiring
references to other objects. Composition requires objects to respect each others'
interfaces, which in turn requires carefully designed interfaces that don't stop
you from using one object with many others. But there is a payoff. Because objects
are accessed solely through their interfaces, we don't break encapsulation. Any
object can be replaced at run-time by another as long as it has the same type.
Moreover, because an object's implementation will be written in terms of object
interfaces, there are substantially fewer implementation dependencies.

Object composition has another effect on system design. Favoring object
composition over class inheritance helps you keep each class encapsulated and
focused on one task. Your classes and class hierarchies will remain small and
will be less likely to grow into unmanageable monsters. On the other hand, a design
based on object composition will have more objects (if fewer classes), and the
system's behavior will depend on their interrelationships instead of being defined
in one class.
That leads us to our second principle of object-oriented design:


Favor object composition over class inheritance.

Ideally, you shouldn't have to create new components to achieve reuse. You should
be able to get all the functionality you need just by assembling existing components
through object composition. But this is rarely the case, because the set of
available components is never quite rich enough in practice. Reuse by inheritance
makes it easier to make new components that can be composed with old ones.
Inheritance and object composition thus work together.
Nevertheless, our experience is that designers overuse inheritance as a reuse
technique, and designs are often made more reusable (and simpler) by depending
more on object composition. You'll see object composition applied again and again
in the design patterns.

## Delegation

Delegation is a way of making composition as powerful for reuse as inheritance
[Lie86, JZ91]. In delegation, two objects are involved in handling a request:

a receiving object delegates operations to its delegate. This is analogous to
subclasses deferring requests to parent classes. But with inheritance, an
inherited operation can always refer to the receiving object through the this
member variable in C++ and self in Smalltalk. To achieve the same effect with
delegation, the receiver passes itself to the delegate to let the delegated
operation refer to the receiver.
For example, instead of making class Window a subclass of Rectangle (because
windows happen to be rectangular), the Window class might reuse the behavior of
Rectangle by keeping a Rectangle instance variable and delegating
Rectangle-specific behavior to it. In other words, instead of a Window being a
Rectangle, it would have a Rectangle. Window must now forward requests to its
Rectangle instance explicitly, whereas before it would have inherited those
operations.

The following diagram depicts the Window class delegating its Area operation to
a Rectangle instance.

<image>


A plain arrowhead line indicates that a class keeps a reference to an instance
of another class. The reference has an optional name, "rectangle" in this case.
The main advantage of delegation is that it makes it easy to compose behaviors
at run-time and to change the way they're composed. Our window can become circular
at run-time simply by replacing its Rectangle instance with a Circle instance,
assuming Rectangle and Circle have the same type.

Delegation has a disadvantage it shares with other techniques that make software
more flexible through object composition: Dynamic, highly parameterized software
is harder to understand than more static software. There are also run-time
inefficiencies, but the human inefficiencies are more important in the long run.
Delegation is a good design choice only when it simplifies more than it complicates.
It isn't easy to give rules that tell you exactly when to use delegation, because
how effective it will be depends on the context and on how much experience you have with it. Delegation works best when it's used in highly stylized ways—that
is, in standard patterns.

Several design patterns use delegation. The State (338), Strategy (349), and
Visitor (366) patterns depend on it. In the State pattern, an object delegates
requests to a State object that represents its current state. In the Strategy
pattern, an object delegates a specific request to an object that represents a
strategy for carrying out the request. An object will only have one state, but
it can have many strategies for different requests. The purpose of both patterns
is to change the behavior of an object by changing the objects to which it delegates
requests. In Visitor, the operation that gets performed on each element of an
object structure is always delegated to the Visitor object.

Other patterns use delegation less heavily. Mediator (305) introduces an object
to mediate communication between other objects. Sometimes the Mediator object
implements operations simply by forwarding them to the other objects; other times
it passes along a reference to itself and thus uses true delegation. Chain of
Responsibility (251) handles requests by forwarding them from one object to another
along a chain of objects. Sometimes this request carries with it a reference to
the original object receiving the request, in which case the pattern is using
delegation. Bridge (171) decouples an abstraction from its implementation. If
the abstraction and a particular implementation are closely matched, then the
abstraction may simply delegate operations to that implementation.


Delegation is an extreme example of object composition. It shows that you can
always replace inheritance with object composition as a mechanism for code reuse.