# Burrito Monads, Arrow Kitchens, and Freyd Category Recipes

## Introduction
From Lawvere's [Hegelian taco](http://ncatlab.org/nlab/show/Hegelian+taco) to Baez's [layer cake analogy](https://math.ucr.edu/home/baez/cohomology.pdf) to Eugenia Cheng's _How to Bake Pi_, categorists have cultivated a rich tradition of culinary metaphors and similes. A well-known example in the world of computation is Mark Dominus's ["monads are like burritos"](https://blog.plover.com/prog/burritos.html) — where a tortilla (computational context) wraps diverse ingredients (values) to create a cohesive entity (effectful value) whose burrito structure is maintained as the meal moves down the assembly line (undergoes computations).


Monads, like burritos, come in many different varieties. In computer science monads serve to streamline computational patterns such as exception handling and context management.  We illustrate these two examples by analogy.

<!-- when workin at a burrito truck, one might face two scenarios:-->
Imagine you work at a burrito truck.

If a customer orders a burrito sans <!--without--> rice but rice is accidentally added, it can't be served. The **Maybe monad** handles exceptions such as this — when something goes wrong, it returns a special "Nothing" value rather than a flawed result, and once a failure occurs, all subsequent steps automatically preserve this state avoiding the need for repetitive error-checking.

<p align="center">
  <img src="https://cdn.discordapp.com/attachments/1018203420143910923/1373225204939096094/Screen_Shot_2025-05-17_at_2.36.29_PM.png?ex=6829a322&is=682851a2&hm=dc1546d4c75ad0e8e6c58a335a691ecc392de2f40772849f46082bbe94c287d7&" alt="Maybe Monad Burrito" />
  <br>
  <em>Figure 1: The Maybe Monad illustrated with the burrito-making process</em>
</p>

In Haskell, the parameterized type "Maybe a" has two constructors, "Just a" and "Nothing."  The former is an alias for values of type "a" whereas the latter is indicative of an error.  The following Haskell code exhibits the maybe type as an instance of the monad class:
```
instance Maybe Monad where
return = Just
Nothing >>= f = Nothing
(Just x) >>= f = f x
```

the return function has type a -> Maybe a, which is suggestive of its role as the monad unit.  The so-called bind operation >>= has type Maybe a -> (a -> Maybe b) -> Maybe b, and corresponds to a bare-bones Kleisli composition (see [Monads: Programmer's Definition](https://bartoszmilewski.com/2016/11/21/monads-programmers-definition/) for details).

A slight generalization allows for descriptive error messages.

**Definition.** Given a collection of exceptions $E$, there is an associated **Either monad** $((-)+E, \eta, \mu)$.


* $\eta_X:X \to X + E$ is the coproduct insertion 
* $\mu_X:X + E + E \to X + E$ collapses two copies of $E$ into one 
* Kleisli morphisms are computations that may fail $X \to Y + E$
* Kleisli composition automatically propagates exceptions

Of course, either monads are simply maybe monads with a set in place of the constant/singleton "Nothing" and they allow us not only to say *that* an error has occured, but also to indicate *what* that error was.

Now suppose one of your regular customers walks up to the window and orders "the usual."  Luckily you've recorded their preferences in a recipe book.  The act of following the appropriate recipe is akin to executing computations that depend on a global read-only state. The **Reader monad** is the functional programmer's way of incorporating this impure concept in pure functional terms. <!-- incorporates this context like pages of a recipe book; it's like a special burrito wrapper that carries customer preferences and makes them available at every preparation step. whether we're adding protein, vegetables, or salsa, we can avoid. The same burrito-making process adapts automatically to each customer's "environment". **Reader monads** model such computations that depend on some global state. -->

<p align="center">
  <img src="https://cdn.discordapp.com/attachments/1018203420143910923/1373226702603288636/Screen_Shot_2025-05-17_at_2.42.27_PM.png?ex=6829a487&is=68285307&hm=a029a7ba7ce5ca0bf86d9a10b051a8d396e2c032dc0f71d39fbca5de3302d51c&" alt="Reader Monad Burrito" />
  <br>
  <em>Figure 2: The Reader Monad illustrated with the burrito-making process</em>
</p>

**Definition.** Given a collection of environments $E$, there is an associated **Reader monad** $((-)^E, \eta, \mu)$.

* $\eta_X : X \to X^E$ turns elements into constant functions $x \mapsto \lambda e. x$
* $\mu_X : (X^E)^E \to X^E$ turns function-valued functions into functions via diagonal evaluation $f \mapsto \lambda e. f(e)(e)$
* Kleisli morphisms convert inputs into executable functions from environments to outputs $X \to Y^E$
* Composition in the Kleisli category keeps track of the (immutable) environment as computations are chained together

Here is the same definition given as an instance of the Haskell monad class:
```
instance Monad ((->) r) where
return x = \_ -> x
g >>= f = \e -> f (g e) e
```
<!--In the context of this post, an additional use of monads is that they structure effects in classical computing, as stated by --> 
[The seminal paper of Moggi](https://www.lfcs.inf.ed.ac.uk/reports/88/ECS-LFCS-88-66/ECS-LFCS-88-66.pdf) has several other interesting examples illustrating the power of monads.  Nevertheless, monads may not always suffice for all of our needs.  For example, what would happen if our burrito truck suddenly exploded in popularity requiring automation of repetative processes and parallel work stations? <!--While monads are powerful for handling individual contexts like exceptions or environments, they have limitations. What would happen if the burrito truck expanded into a full-service restaurant with various kinds of orders, multiple prep stations, and interdependent dishes? -->


This is where "arrows" enter the picture. [Introduced by John Hughes in 2000](https://www.sciencedirect.com/science/article/pii/S0167642399000234), arrows generalize strong monads.  Because of this, arrows handle more complicated computational patterns in a natural way. While monads wrap values in computational contexts (like burritos in tortillas), arrows can represent entire preparation processes capable of coordinating multiple inputs while maintaining awareness of the broader kitchen environment.

Arrows come with three core operations that determine their behaviour; looking at their types, we see that arrows are evocative of a lax internal hom that interacts with binary products. 

```
class Arrow a where
arr :: (x -> y) -> a x y
(>>>) :: a x y -> a y z -> a x z
first :: a x y -> a (x,z) (y,z)
```

1. `arr` turns functions into "arrows."  This is like incorporating a standard burrito recipe or preparation step into the food truck's workflow — taking a simple instruction like "add beans, then cheese" and automating it within our kitchen's setup.
2. `>>>` composes composable arrows. This allows for separately automated processes to be seamlessly strung together. <!--This is sequential composition, it's how you sequence preperation steps, like ensuring that after the protein choice is added, the burrito moves to the salsa station, and then the wrapping station.-->
3. `first` enacts an automated process on one burrito while simultaneously passing a second burrito through the station. <!--This operation allows an arrow to transform one component while leaving the other untouched. This is like modifying the burrito filling while leaving the side of beans untouched. The capability to process just one part of a complex inputs is a powerful property that arrows have. -->

These data are subject to 9 axioms, which we eventually discuss below. 

<p align="center">
  <img src="https://cdn.discordapp.com/attachments/1018203420143910923/1373250942056530030/Screen_Shot_2025-05-17_at_4.18.45_PM.png?ex=6831a41a&is=6830529a&hm=1648a217922a574bf4ced491ae2ccfee6eae8420907b41d8c78d1116be0fe252&" alt="Arrow Operations" />
  <br>
  <em>Figure 3: Arrow Operations. The three fundamental operations of arrows enable complex workflows beyond monadic structures.</em>
</p>


Shortly before arrows were introduced, Power, Robinson, and Thielecke were working on Freyd categories — a categorical structure designed to model "effectful" computation. Using our simile, a Freyd category formalizes the relationship between an ideal burrito recipe (pure theory) and the real-world process of making that burrito in a particular kitchen<!--with all its constraints and variables-->. 

A Freyd category consists of three main components:

1. A category $C$ with finite products which can be thought of as the syntax of our kitchen.  In other words, $C$ is like a recipe book containing the abstract information one needs to interpret and implement in the context of a real world kitchen. <!--, like a standard handwritten recipe for a burrito, without considering specific cooking constraints.-->
3. A symmetric premonoidal category $K$ which plays the more semantic role of our real world kitchen.<!--kitchen operations, where timing matters, and operations may interact in some specific cases (like how using the oven can delay something else from being baked)-->
4. An identity-on-objects functor $J:C \to K$ which faithfully translates pure recipes into physical processes that work within the specific setup of the kitchen $K$.<!--, ensuring that basic operations preserve the structure of the recipe.-->

<p align="center">
  <img src="https://cdn.discordapp.com/attachments/1018203420143910923/1373252580548808704/Screen_Shot_2025-05-17_at_4.25.15_PM.png?ex=6831a5a1&is=68305421&hm=b2d9c9c64e17b7af5b9747267f7c174d06ad85d46f687565f973e7c81074e10d&" alt="Freyd Category Structure" />
  <br>
  <em>Figure 4: Freyd Category Structure. The relationship between pure recipes (category C) and real-world kitchen operations (category K), connected by the identity-on-objects functor J that preserves structure while accommodating practical constraints.</em>
</p>

Although arrows originated in Haskell, a highly abstract functional programming language, researchers began noticing apparent correspondences between the components of arrows and those of Freyd categories. These two structures, developed from different starting points, seemed to address the same fundamental challenge: how to systematically manage computations that involve effects, multiple inputs and outputs, and context-awareness. Therefore, it was hypothesized that arrows are equivalent to Freyd categories.

As a part of the Adjoint School, our group has been focusing on [R. Atkey's](https://www.sciencedirect.com/science/article/pii/S157106611100051X) work, which dispells this folklore and precisely formulates the relationship between arrows and Freyd categories. Just as Atkey asks in the title of his paper this blog post will investigate the question of "what is a categorical model of arrows?" Ulimately, we will explain that there are subtle differences  between arrows and Freyd categories.<!--Are arrows the same as Freyd categories? Is the folklore about their equivalence mathematically sound, or is there more subtlety to their relationship?-->


The answer not only clarifies the theoretical aspects but also reveal practical insights for programming language design and quantum computation models. As we'll see, the relationship between arrows and Freyd categories is more nuanced than initially thought, with important implications for how we model complex computational systems.

Of course, the burrito comparisons only go so far, we will use this post to explain our journey to understand arrows and Freyd categories.

---

**Key Insights:**
- Monads encapsulate computational effects by wrapping values in contexts, much like burritos wrap ingredients in tortillas
- Different monads (Maybe, Reader) handle different patterns like exception handling and context management
- Arrows generalize beyond monads to handle multiple inputs and coordinate complex processes, like managing an entire kitchen rather than just making individual burritos

---


## Beyond the Kitchen: Arrows and Freyd Categories


Formally, a monad on a category is a monoid in its category of endofunctors. So far, we've explored monads through concrete examples like the maybe monad and the reader monad, and mentioned that arrows are a further generalization of the computational structure that monads offer. Freyd categories offer a similar concept, as we briefly discussed earlier — we will now describe what these structures offer before pitting them against each other.


### Arrows

To understand arrows from a categorical perspective, a good place to start would be by defining them as monoids in a category of strong profunctors. There are two definitions of arrows that we will explore in this blog post, and the profunctor definition is one of them.

Formally, a profunctor $P$ is a functor $P: C^{\text{op}}\times C \to \text{Set}.$ Unlike regular functors which are maps between categories, profunctors map from a pair of categories (with the first one considered in its opposite form) to the category of sets. Intuitively, a profunctor associates to each pair of objects a set of "generalized morphisms" between them.

Composition of profunctors requires a more sophisticated construction using coends. Given profunctors $P$ and $Q$, their composition is defined as:

$$(P * Q)(x, z) = \int^y P(x, y) \times Q(y, z)$$

This formula has an intuitive interpretation similar to a dot product. The coend instructs us to take a colimit (essentially a sum) over all intermediate objects $y,$ considering all ways to go from $x$ to $z$ via some intermediate $y$.

The identity profunctor is simply $\text{id}(x, y) = C(x, y)$, which uses the hom-sets of the category $C$.

The definition of arrows in terms of the category of profunctors shows how they are capable of modelling complicated relationships between inputs and outputs.

Having examined arrows from a profunctor perspective, we now turn to their concrete definition in terms of operations. This operational view aligns with how arrows are implemented in programming languages like Haskell.

**Definition.** An **Arrow** in a Cartesian closed category $C$ consists of a mapping on objects and three families of morphisms:

* A mapping on objects $\text{Ar} : \text{ob}(C) \times \text{ob}(C) \to \text{ob}(C)$
  
This defines the arrow type constructor, which takes input and output types and produces an arrow type between them.

* A family of morphisms $\text{arr} : Y^X \to \text{Ar}(X, Y)$

This operation lifts a pure function into the arrow context, allowing regular functions to be treated as arrows.

* A family of morphisms $\ggg : \text{Ar}(X, Y) \times \text{Ar}(Y, Z) \to \text{Ar}(X, Z)$
  
 This enables sequential composition of arrows, similar to function composition but preserving the arrow structure.

* A family of morphisms $\text{first} : Y^X \to \text{Ar}(X \times W, Y \times W)$
  
 This is perhaps the most distinctive operation, allowing an arrow to process the first component of a pair while leaving the second component unchanged.

This data is subject to nine axioms that ensure arrows behave consistently. To make these abstract operations more concrete, consider the following example, where $\text{Ar}(x, y) := Y^X,$ $\text{arr} := \text{id}_{(Y^X)},$ $\ggg := \text{composition},$ and $\text{first}(f) := f \times \text{id}.$ We will now list the arrow laws and draw commutative diagrams based on this example.

The arrow laws $\text{arr}(\text{id})\ggg a=a$ and $a\ggg \text{arr}(\text{id})=a$ express left and right unitality of identities under composition.

<p align="center">
  <img src="https://cdn.discordapp.com/attachments/1018203420143910923/1373254007719465050/Screen_Shot_2025-05-17_at_4.29.44_PM.png?ex=6829bdf5&is=68286c75&hm=ea21369bc1c67f12e0a4409e1a437581b92f4a024bf1917ef92865eacd2857fa&" alt="Arrow Identity Laws" />
  <br>
  <em>Figure 5: Arrow Laws </em>
</p>


The arrow law $(a \ggg b)\ggg c=a \ggg (b \ggg c),$ represents associativity of composition.

<p align="center">
  <img src="https://cdn.discordapp.com/attachments/1018203420143910923/1373254008030105661/Screen_Shot_2025-05-17_at_4.29.52_PM.png?ex=6829bdf5&is=68286c75&hm=8ddafc62d9d6a5493f8d590dede6566f6e19b2a2d6a8ad9bf21a449604932a24&" alt="Associativity of >>>" />
  <br>
  <em>Figure 6: Arrow Laws </em>
</p>


The arrow law $\text{first}(a\ggg b)=\text{first}(a)\ggg \text{first}(b)$ encodes functoriality of $- \times W: C \to C$.

<p align="center">
  <img src="https://cdn.discordapp.com/attachments/1018203420143910923/1373254008428433509/Screen_Shot_2025-05-17_at_4.30.01_PM.png?ex=6829bdf5&is=68286c75&hm=1a764e53398da099aaa6cbe5c3a720819143bb567c8652096f1499286b93f54d&" alt="Arrow Laws" />
  <br>
  <em>Figure 7: Arrow Laws</em>
</p>


The arrow law $\text{first}(a)\ggg \text{arr}(\pi_{1})=\text{arr}(\pi_{1})\ggg a$ express naturality of the counit $- \times W \to \text{id}_{C}$, i.e., the first projection maps.

<p align="center">
  <img src="https://cdn.discordapp.com/attachments/1018203420143910923/1373254008755720313/Screen_Shot_2025-05-17_at_4.30.09_PM.png?ex=6829bdf5&is=68286c75&hm=88d42e69a67a7e392d08d9a583ea8e4d8d3558e3519984cae1f565b0356cf04d&" alt="Arrow Laws" />
  <br>
  <em>Figure 8: Arrow Laws</em>
</p>


The arrow law $\text{first}(a)\ggg \text{arr}(\alpha)=\text{arr}(\alpha)\ggg \text{first}(\text{first}(a))$ asks that $\text{first}$ play nicely with associators.

<p align="center">
  <img src="https://cdn.discordapp.com/attachments/1018203420143910923/1373254009036607570/Screen_Shot_2025-05-17_at_4.30.16_PM.png?ex=6829bdf5&is=68286c75&hm=3272bf3e9fab68bf162524667d8845ba0cd36ca38351530e368596ae94dfad1f&" alt="Arrow Laws" />
  <br>
  <em>Figure 9: Arrow Laws</em>
</p>


The arrow law $\text{first}(a)\ggg \text{arr}(\text{id} \times f)=\text{arr}(\text{id} \times f)\ggg \text{first}(a)$ is an interchange law which says $\text{id} \times g:(- \times W) \to (- \times W')$ is a natural transformation for every $g:W \to W'$ in $C$.

<p align="center">
  <img src="https://cdn.discordapp.com/attachments/1018203420143910923/1373254009309368351/Screen_Shot_2025-05-17_at_4.30.26_PM.png?ex=6829bdf5&is=68286c75&hm=69c4d7ab28f7555f15d61a2bc9b66747e76de190f8710018c783b1f05eec631b&" alt="Arrow Laws" />
  <br>
  <em>Figure 10: Arrow Laws</em>
</p>


Two arrow laws trivialise as a result of our example, so diagrams aren't produced. The first such law is $\text{arr}(f;g)=\text{arr}(f)\ggg \text{arr}(g).$ For our example, this law trivialises, as $\ggg : = \text{composition}$ and $\text{arr} := \text{id}_{(Y^X)}.$ The second law that trivialises would be $\text{first}(\text{arr}(f))=\text{arr}(f \times \text{id})$ as a result of us setting $\text{first}(f) := f \times \text{id}.$

### Freyd Categories

To understand Freyd categories, we must first define what a symmetric monoidal category is.

**Definition.** A **symmetric premonoidal category** includes:

- An object $I$ (unit).
- Natural transformations that define how objects interact:
  - Associativity: $\alpha : (x \otimes y) \otimes z \to x \otimes (y \otimes z)$
  - Left unitor: $\lambda : x \otimes I \to x$
  - Right unitor: $\rho : I \otimes x \to x$
  - Symmetry: $\sigma : x \otimes y \to y \otimes x$
- All components are **central**.

A morphism $f : x \to x'$ is **central** if $\forall g:y \to y', \quad f \otimes y ; x' \otimes g = x \otimes g ; f \otimes y'$

Now, we can define a Freyd category, recalling the definition from the introduction.

**Definition.** A **Freyd category** consists of:

- A category $C$ with finite products.
- A symmetric premonoidal category $K$.
- An identity-on-objects functor $J : C \to K$ that:
  - Preserves symmetric premonoidal structure.
  - Ensures $J(f)$ is always central.
  
### Arrows vs Freyd Categories: Similarities and Differences

At first glance, the definition of a Freyd category appears strikingly similar to that of an Arrow. This apparent similarity led to the folklore belief that they were equivalent structures.

A Freyd category consists of two categories $C$ and $K$ with an identity-on-objects functor $J: C \to K$, where:
- $C$ has finite products
- $K$ is symmetric premonoidal (with a functor $− \otimes z$)
- $J$ maps finite products in $C$ to the premonoidal structure in $K$

In our culinary metaphor, we could map this to:
- $C$: The idealized recipes (Haskell types and functions)
- $K$: The real-world kitchen operations (computations represented by $\text{Ar}(x,y)$)
- $J$: The translation process (via `arr`, embedding pure functions)
- Composition in $K$: The sequencing of operations (via `>>>`)
- Premonoidal structure in $K$: The ability to process pairs (via `first`)

This correspondence seemed so natural that for many years, the programming languages community considered Arrows and Freyd categories to be essentially the same concept expressed in different languages.

However, Atkey's work revealed a crucial distinction: **Arrows are more general than Freyd categories**. The key difference lies in how they handle inputs:

- Freyd categories allow only a single input to computations
- Arrows support two separate inputs:
  - One may be structured (modeled using comonads)
  - This additional flexibility allows Arrows to represent computations that Freyd categories cannot

To bridge this gap, Atkey introduced the concept of **indexed Freyd categories**, which can model two structured inputs. The relationship can be summarized as:

**Arrows are equivalent to Closed Indexed Freyd Categories**

In our culinary metaphor, we can understand this relationship as follows: a Freyd category is like a restaurant that can only take one order at a time (a single input), while arrows are like a more sophisticated establishment that can handle both individual orders and special requests that come with their own context (two inputs, one potentially structured). The closed indexed Freyd categories that Atkey identifies represent the perfect middle ground — restaurants that can efficiently manage multiple orders with specialized instructions while maintaining the core operational principles that make kitchens function. This is particularly valuable when preparing complex "quantum dishes" where ingredients might be entangled and interact with each other in non-local ways.

<p align="center">
  <img src="https://cdn.discordapp.com/attachments/1018203420143910923/1373257168463073371/Screen_Shot_2025-05-17_at_4.43.28_PM.png?ex=6831a9e6&is=68305866&hm=85e0e0b92ea28138fa3aeb243d425ba7b8032d42d86da79ecb7c13c617fb63e7&" alt="Arrows vs. Freyd Categories" />
  <br>
  <em>Figure 11: Arrows vs. Freyd Categories. Arrows support two inputs (one potentially structured) and are equivalent to Closed Indexed Freyd Categories, which generalize standard Freyd Categories that handle only single inputs.</em>
</p>


This distinction helps explain why Arrows have proven particularly useful in domains like quantum computing, where managing multiple inputs with complex relationships is essential.

R. Atkey's paper finds the relationship between arrows and different constraints on Freyd categories as follows: 

<p align="center">
  <img src="https://cdn.discordapp.com/attachments/1018203420143910923/1373257727874306189/Screen_Shot_2025-05-17_at_4.45.36_PM.png?ex=6831aa6c&is=683058ec&hm=0287206f6a726eb4b84f718168a3721307043a9f4e7e813587a75b682c43435b&" alt="Relationship between structures" />
  <br>
  <em>Figure 12: Relationship between structures</em>
</p>


---

**Key Insights:**
- Arrows can be defined both as monoids in categories of strong profunctors and operationally through concrete morphisms ($\text{arr}$, $\ggg$, $\text{first}$)
- Freyd categories formalize the relationship between pure functions and effectful computations using symmetric premonoidal structure
- Despite the folklore belief, arrows are strictly more general than Freyd categories because they can handle two separate inputs (one potentially structured)
- Arrows are equivalent to closed indexed Freyd categories, bridging the conceptual gap

---

## Thoughts + Further Questions After Reading R. Atkey's Work 

Atkey's work helped us realize the subtle differences between arrows and Freyd categories.  By carefully examining the mathematical structure, he demonstrated that arrows are more general than Freyd categories.

The critical observation — that arrows can process two distinct inputs whereas Freyd categories can only allow one — has far-reaching consequences. Arrows' capacity to handle multiple inputs with a single potentially structured offers tractability that is particularly useful in quantum computing. Particles in quantum systems can be in entangled states in which manipulation of one particle influences others in real time irrespective of distance. This non-local interaction can be modelled through arrows' ability to bring several inputs together while keeping track of their interrelationship.

This relationship of arrows and quantum computing leads us to a number of interesting questions:

* Can relative monads model quantum effects, and how do they compare to arrow-based approaches? 

* Given that symmetric fusion categories lack cartesian products, how can we generalize the notion of Freyd categories — originally built over cartesian categories — to this rigid, monoidal setting? 

* Can indexed Freyd categories explain quantum contextuality? 

The ride from burrito monads to arrow kitchens has carried us farther than we anticipated, illustrating that even established mathematical folklore sometimes requires precise re-evaluation. As we continue to learn about these structures, we hope this post will motivate others to participate in the exploration of these tools and their use in quantum computing and beyond.
