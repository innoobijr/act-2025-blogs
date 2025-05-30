Graded Monads for Numerical Analysis

Jessica Richards and J&oacute;n H&aacute;kon Gar&eth;arsson


Monads have been used to model computational effects, starting with Moggi.
They have successfully modeled various kinds of effects such as partiality, non-determinism, stateful computation, exceptions, reading input, writing output, and more. They do this in a fairly coarse manner, we just know whether something is effectful or not. So, although pretty useful, it can be a bit limiting for some effects.

For example, when computers do numerical calculations, they induce small rounding errors at each step due to limitations of floating point arithmetic. This can be seen as an effect, but just knowing whether a calculation is approximate or not is unhelpful. It would be much better to have a bound on the accumulated rounding error. Luckily, there's a generalisation of monads that model this kind of behaviour, called graded monads. Our group has been reading [towards a formal theory of graded monads](https://www.irif.fr/~mellies/papers/fossacs2016-final-paper.pdf) which covers a lot of interesting theory about these.

## Graded monads

Before tackling graded monads we should remind ourselves of the definition of a monad.

#### Definition 1:
A *monad* consists of an endofunctor \(T : \mathbf C \to \mathbf C\) along with natural transformations \(\mu : T \circ T \Rightarrow T\) and \(\eta : \Id_{\mathbf C} \Rightarrow T\) such that the following diagrams commute:


<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Monad-laws.png";alt=";Commutative diagrams for a monad.";/>;

For those who know about string diagrams, we can draw the multiplication \(\mu\) and unit \(\eta\) of a monad as:

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Monad-laws-string-diagram.png";alt=";mu and eta for a monad, as a string diagram.";/>;

in the monoidal category of endofunctors on \(\mathbf C\) with composition as the monoidal product.
The associativity condition becomes:

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Monad-laws-assoc.png";alt=";Associativity law for a monad, written as a string diagram.";/>;

#### Example 1:
The list monad has the endofunctor \(X \mapsto \List X\) on \(\mathbf{Set}\),
which takes a set \(X\) to the set of finite lists in \(X\).
The multiplication flattens lists of lists and the unit wraps elements into single-element lists.


#### Example 2:
Let \((M, \cdot, 1)\) be a monoid which we think of as the monoid of side effects.
The \(M\)-writer monad sends a set \(X\) to \(X \times M\).
The multiplications is \(X \times M \times M \to X \times M, (x, m, n) \mapsto (x, m n)\) and the unit is \(X \to X \times M, x \mapsto (x, 1)\).
For example if $M = (\String, +, \text{""})$, the monoid of strings, then multiplications is \((x, s, t) \mapsto (x, s + t)\) and the unit is \(x \mapsto (x, \text{""})\).


#### Example 3:
Let \(S\) be a set which we think of as the set of states.
We want to model computations that also modify the state, so function of the form \(X \times S \to Y \times S\).
Currying gives us \(X \to (Y \times S)^S\).
The \(S\)-state monad \(X \mapsto (X \times S)^S\).
The multiplication is:

$((X \times S)^S \times S)^S \to (X \times S)^S$ 

$f \mapsto (s \mapsto \text{let } (g, t) = f(s) $ in $ g(t))$
    
and the unit is

$X \to (X \times S)^S$
$x \mapsto (s \mapsto (x, s))$


#### Example 4:
The powerset monad \(X \to PX\), has multiplication that takes the union of collections of subsets \(PPX \to PX, \mathcal{U} \mapsto \bigcup_{U \in \mathcal{U}} U\) and the unit wraps elements into single-element subsets \(x \mapsto \{x\}\).


#### Definition 2:
A *preordered monoid* is both a preorder \((M, \leq)\) and a monoid \((M, \cdot, 1)\) such that the multiplication is monotone.
That is, if \(m \leq n\) and \(p \leq q\), then \(m p \leq n q\) for all \(m, n, p, q \in M\).

Examples include \((â„, \leq, +, 0)\) and \((â„•, \leq, \cdot, 1)\).
These can be written as a monoidal category with morphisms corresponding to the preorder.

#### Definition 3:
Given a monoidal category $(\mathbf{M}, \otimes, I)$, an $\mathbf{M}$-*graded monad* is comprised of:

- A functor $T: \mathbf{M} \rightarrow [\mathbf{C}, \mathbf{C}]$ giving for each $m \in \mathbf{M}$ an endofunctor $T_m$. Equivalently, a functor $T: \mathbf{M} \times \mathbf{C} \rightarrow \mathbf{C}$.
- A unit $\eta: I \Rightarrow T_1$
- A family of multiplications $\mu_{m, n}: T_m T_n \Rightarrow T_{m \cdot n}$

Which satisfy:

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Graded-monad.png";alt=";Commutative diagrams for graded monads.";/>;


To see that this really is a generalisation of a monad, firstly note that if we look at the above diagrams and ignore everything in red, these are just the monad laws. Everything in red is trivial if our monoidal category is 1. So in fact, a monad can be seen as a 1-graded monad.

The definition of a graded monad works for any monoidal category, but it is simpler if it is discrete or pre ordered. Pre ordered is also very useful for effects, as then we can bound effects. So we will only consider the pre ordered case for this blog post.

We can make a graded version of the monads from before.
The list monad can be graded over \((N, \leq, \cdot, 1)\) were we consider lists of length less than a particular length.
The writer monad can be graded if we consider a graded monoid.
The powerset monad can be graded over cardinalities by considering subsets of most some cardinality.

## Constructions

### Adjunctions
We start with a brief background on why adjunctions are important for regular monads, then give an overview of the generalisations for graded monads. Given any adjunction $F \dashv G$ with $\mathbf{C}$ as the domain of $F$, we can construct a monad $GF: \mathbf{C} \rightarrow \mathbf{C}$ with the unit as that of the adjunction and multiplication as $G \circ \epsilon \circ F$ where $\epsilon$ is the counit of the adjunction. 


<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Adjunction.png";alt=";An adjunction.";/>;


Now its all very interesting that we can make new monads appear from adjunctions but we really want to be able to make the monads we already cared about appear from adjunctions. For a monad $T$, if an adjunction $\langle F, G, \eta, \epsilon \rangle$ corresponds to $T$ via the above construction, it is called a *resolution* of $T$. And, for any monad $T$ there turns out to be two canonical ways to construct a resolution, via adjunctions to categories called the Eilenberg-Moore category $\mathbf{C}^T$ and the Kleisli category $\C_T$. These are canonical in the sense that they are the terminal and initial objects in the category of resolutions, respectively.

These results turn out to generalise to graded monads. Instead of considering monads and adjunctions, we consider $\mathbf{M}$-graded monads and adjunctions $F \dashv G$ with an action $\triangleright$ of $\mathbf{M}$ on the codomain of $F$. Given such an adjunction and action, $m \mapsto G(m \triangleright F - )$ is a graded monad. We illustrate this in the following diagram, where $T$ is the composition of the relevant functors.

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Graded-monad-decomp.png";alt=";The composition of adjunction and monoidal action.";/>;

Conversely, we can construct Eilenberg-Moore and Kleisli categories for any graded monad to give canonical resolutions of it. The rest of this section will cover the Eilenberg-Moore and Kleisli categories in detail, both for a regular monad and the graded versions.

### Eilenberg-Moore category

#### Definition 4:


Let \(T\) be a monad on a category \(\mathbf C\).
A \(T\)-algebra consists of an object \(A\) and morphism \(h : TA \to A\) such that

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/T-algebra.png";alt=";T-algebra comm diagram.";/>;

commutes.
A \(T\)-algebra homomorphism from \((A, h)\) to \((B, k)\) is a morphism \(f : A \to B\) such that the following diagram commutes

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/T-algebra-hom.png";alt=";T-algebra hom comm diagram.";/>;

The \(T\)-algebras and \(T\)-algebra homomorphisms form the Eilenberg-Moore category $\mathbf{C}^T$. We have a resolution of $T$ given by $F \dashv U$ where:

- $F$ sends an object $X$ to the pair $(TX, \mu_X)$, the free algebra on $X$
- $U$ takes a $T$-algebra $(A, h)$ to the object $A$
-The composition takes an object $X$ to $(T X, \mu_X)$ to $TX$, as required

This resolution is terminal.

The algebras of the list monad are monoids.
The algebras of the writer monad are sets with monoid action.

#### Definition 5:


Let \(T\) be a \(M\)-graded monad on a category \(\mathbf C\) for a preordered monoid \(M\).
A \(T\)-algebra consists of a functor \(A : M \to \mathbf C\) and a familiy of morphisms \(h_{m, n} : T_m A_n \to A_{m n}\) such that

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Graded-T-algebra.png";alt=";Graded T-algebra comm diagram.";/>;


A \(T\)-algebra homomorphism from \((A_i, h_{i,j})\) to \((B_i, k_{i,j})\) is a family of morphisms \(f_m : A_m \to B_m\) such that the following diagram commutes

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Graded-T-algebra-hom.png";alt=";Graded T-algebra-hom comm diagram.";/>;

for all \(m, n \in M\).

The graded Eilenberg-Moore category consists of the graded algebras and their homomorphisms. For an $\mathbf{M}$-graded monad $T$ this gives a resolution $F \dashv U$, $\triangleright$, where:

- $F$ sends an object $X$ to the graded algebra $\{T_m X\}_{m \in \mathbf{M}}, \{h_{m, n}\}_{m, n \in \mathbf{M}}$
- $m \triangleright (A_i, h_{i,j})$ sends the graded algebra to $(A_{i \cdot m}, h_{i, j \cdot m})$. So for instance, the unit algebra of the image is $A_m$.
- $U$ selects the carrier of the unit algebra $A_1$

Since this is a bit more confusing, its nice to how we can use this on the graded list monad.

- For a set $X$, the free graded algebra $F X$ is $((L_i)_{i \in â„•}, (\mathbf{concat}_{i,j})_{i,j \in â„•})$ where $L_i$ is the set of lists of length $i$. Since concat doesn't really change much whatever $i, j$ it is parametrised by, we'll just ignore it.
- Given a number $n$. $n \triangleright ((L_i)_{i \in â„•}, (\mathbf{concat}_{i,j})_{i,j \in â„•})$ will be the lists of length $i * n$, so, lists of length 0, $n$, $2n, \ldots$.
- Now $U$, given an algebra $n \triangleright F X$ selects $L_{1 \cdot n}$. So this is exactly $T_n X$, the set of lists of length $n$.
  
This is terminal in the category of resolutions.

Consider a graded ring \((R_n)_{n \in N}\).
We can make a graded monad on the category of abelian groups with the endofunctors \(A \mapsto R_n \otimes A\).
Multiplication and unit are like in the case of the writer monad.
The algebras of this monad are graded modules over \((R_n)_n\) and the algebra homomorphisms are graded homomorphisms.

### Kleisli construction


#### Definition 6:
The Kleisli construction of a monad \(T\) on a category \(\mathbf C\) is a category with the same objects as \(\mathbf C\) but morphisms are now of the form \(X \to TY\).
The composition of \(f : X \to TY\) and \(g : Y \to TZ\) is the morphism

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Kleisli-comp.png";alt=";Diagram.";/>;

and the identities are \(\eta_X : X \to TX\).

We can again use string diagrams.
Remember that an object \(X\) in a category \(\mathbf C\) can equivalently be seen as a functor \(X : \mathbf 1 \to \mathbf C\) from the terminal category.
A morphism \(f : X \to Y\) can in a similar way be seen as a natural transformation between such functors.
So we can draw a morphism in string diagrams as


<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/String-diagram-morphism.png";alt=";Diagram.";/>;

Let \(T : \mathbf C \to \mathbf C\) be an endofunctor.
Then the object \(TX\) can be seen as a composition of functors \(\mathbf 1 \xrightarrow{X} \mathbf C \xrightarrow{T} \mathbf C\).
We can draw this as follows

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/String-functor-comp.png";alt=";Diagram.";/>;

and a morphism \(Tf : TX \to TY\) as


<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/String-TX-Tf-TY.png";alt=";Diagram.";/>;


We can draw a Kleisli morphism \(f : X \to TY\) with a string diagram as

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/String-Kleisli-morphism.png";alt=";Diagram.";/>;


and a composition \(X \xrightarrow{f} TY \xrightarrow{Tg} TTZ \xrightarrow{\mu_Z} TZ \) as

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/String-Kleisli-comp.png";alt=";Diagram.";/>;

or just

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/String-Kleisli-comp-alt.png";alt=";Diagram.";/>;

A good way to get intuition for these string diagrams is to consider the case of the writer monad, then the diagrams become exactly the same as the string diagrams of a monoidal category.

The Kleisli construction of a graded monad \(T\) on a category \(\mathbf C\) is a bit more complicated.
The objects are pairs \((X, m)\) of objects and grades.
A morphism from \((X, m)\) to \((Y, n)\) is an equivalence class
\[
	[p, f : X \to T_p Y, m p \leq n].
\]
under the equivalence relation
\[
	(p, f : X \to T_p Y, m p \leq n) \sim
	(p', f' : X \to T_q Y, m p' \leq n)
\]
if \(p \leq p'\) and

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Graded-Kleisli-equiv.png";alt=";Diagram.";/>;

or \(p' \leq p\) and vice versa.

The composition of \([q, f : X \to T_q Y, m q \leq n] : (X, m) \to (Y, n)\) and \([r, g : Y \to T_r Z, n r \leq p] : (Y, n) \to (Z, p)\) is
\[
	[q r, X \xrightarrow{f} T_q Y \xrightarrow{T_q g} T_q T_r Z \xrightarrow{\mu_{q, r, Z}} T_{q r} Z, m q r \leq p].
\]
The identites are of the form \([1, \eta_X : X \to T_1 X, m 1 \leq m ]\).

We can draw a (representative of a) Kleisli morphism with a string diagram as

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Graded-Kleisli-morphism.png";alt=";Diagram.";/>;

Composition becomes

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Graded-Kleisli-comp.png";alt=";Diagram.";/>;

### Locally graded categories

Let \((M, \leq, \cdot, 1)\) be a preordered monoid.
A \(M\)-*locally graded* category is a category enriched over \([M^{\op}, \mathbf{Set}]\).
That is, the hom-sets are graded \(\Hom_m(X, Y)\), composition is of the form
\[
	\Hom_m(X, Y) \times \Hom_n(Y, Z) \to \Hom_{m n}(X, Z)
\]
and identities are unit-graded \(\id_X \in \Hom_1(X, X)\).
We also have upgrade maps \(\Hom_m(X, Y) \to \Hom_n(X, Y)\) for \(m \leq n\).

A graded monad \(T\) gives rise to a locally graded category with \(\Hom_m(X, Y) = \Hom(X, T_m Y)\).
This category is in some way similar to the Kleisli category which can help us understand it better.

Assume that \(M\) is commutative and closed monoidal, that is, for every \(m, n \in M\) there exists \([m, n]\) such that \(p m \leq n\) if and only if \(p \leq [m, n]\).
Examples of closed preordered monoids include \((â„•, \leq, +, 0)\) and \(([0, \infty], \leq, + , 0)\) with \([a, b] = \max(0, b - a)\).

We can now make a functor from the Kleisli category to the graded one.
Objects \((X, m)\) in the Kleisli category forget their grade and get sent to \(X\).
A morphism \([p, f : X \to T_p Y, m p \leq n]\) from \((X, m)\) to \((Y, n)\) get sent to the \([m, n]\)-morphism

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Graded-Kleisli-functor.png";alt=";Diagram.";/>;

where the second map is upgraded with \(p \leq [m, n]\).
This is well-defined.

We can draw the morphisms in this category with string diagrams in a similar way to the non-graded Kleisli morphisms.
A morphism \(f : X \to T_mY\) is drawn as

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Graded-Kleisli-X-TmY.png";alt=";Diagram.";/>;

and the composition of \(f : X \to T_m Y\) and \(g : Y \to T_n Y\) as

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
    main/4b-jessica-jon/blog_post_diagrams/Graded-Kleisli-graded-comp.png";alt=";Diagram.";/>;


## Applications


Our motivation for studying graded monads was to give better bounds on numerical error in a compositional way.
The grading is supposed to give the degree of error and it gets propagated along compositions.
For this we define on graded monad, which we call the neighbourhood monad, and  one graded comonad, which we call the Lipschitz comonad.

### Neighbourhood monad

We define a \(([0, \infty], \geq, +, 0)\)-graded monad that we call the graded neighbourhood monad.
Let \(\mathbf{Met}\) be the category of generalised metric spaces.
For each \(\varepsilon \in [0, \infty]\) we have a functor \(T_\varepsilon : \mathbf{Met} \to \mathbf{Met}\) that associates each metric space \((X, d)\) with the metric space
\[
    T_\varepsilon X = \{(x, y) \in X^2 \mid d(x, y) \leq \varepsilon\}
\]
with the metric \(d_r((x, y), (x', y')) = d(x, x')\).
On morphisms (short maps), this just applies the map to both points \(T_r f(x, y) = (f(x), f(y))\).
The unit map is \(\eta_X : X \mapsto T_0 X; x \mapsto (x, x)\) and the multiplication map \(\mu_{\varepsilon, \delta, X} : T_\varepsilon T_\delta X \to T_{\varepsilon + \delta} X\) is given by
\[
    ((x, y), (x', y')) \mapsto (x, y').
\]
The triangle inequality guarantees that \(d(x, y') \leq \varepsilon + \delta\).

Consider the locally graded category associated to this graded monad.
We can think of a morphism \(f : X \to T_\varepsilon Y\) as two maps: a perfect map \(f : X \to Y\) and an approximate map \(\tilde f : X \to Y\) such that \(d(f(x), \tilde f(x)) \leq \varepsilon\).
The composition of two such morphisms \(f : X \to T_\varepsilon Y\) and \(g : Y \to T_\delta Z\) could be interpreted as

<;img
src=";https://raw.githubusercontent.com/innoobijr/act-2025-blogs/refs/heads/
main/4b-jessica-jon/blog_post_diagrams/Neighbourhood-monad.png";alt=";Diagram.";/>;

### Lipschitz comonad

For each \(r \in [0, \infty]\) we have a functor \(D_r : \mathbf{Met} \to \mathbf{Met}\) with \(D_r (X, d) = (X, r \cdot d)\).
On morphisms \(D_r f = f\) and this is a short map \(D_r X \to D_r Y\) because \(r d(x, y) \geq r d(f(x), f(y))\) follows from \(d(x, y) \geq d(f(x), f(y))\).
We will show that this forms a \(([0, \infty], \geq, \cdot, 1)\)-graded comonad.
The comultiplication \(\delta_{r, s, X} : D_{r \cdot s} X \to D_r D_s X\) and the counit \(\varepsilon_X : D_1 X \to X\) are both just the identity.
Therefore, they satisfy coassociativity and counitality.

Let's see what the locally graded category associated to the Lipschitz comonad is like.
For metric spaces \(X, Y\) and \(r \in [0, \infty]\) a short map \(f : D_r X \to Y\) is such that 
\[
	r d(x, x') \geq d(f(x), f(x'))
\]
for all \(x, x' \in X\).
Namely, a \(r\)-Lipschitz map.
