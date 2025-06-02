# A Study of Concrete Categorizations of Graphs
*guest post by [Maria Ramos]() and [Aya Samadzelkava]()

**Introduction**

Imagine looking at two drawings side by side: on the left, a small, loopless network with just a few edges; on the right, a more tangled structure—complete with multiple edges linking the same vertices and perhaps a loop or two. While both are "graphs", they look and behave quite differently. The rules governing what counts as an “edge” or a “loop” can vary widely from one definition to another. This ambiguity is why we talk about types of graphs.

<div style="text-align: center;">
<img src="images/graphs.jpeg"  width="400"/>
</div>


We have a similar ambiguity when we talk about graph morphisms - a way to map one graph into another. While both examples represent morphisms, the rules they follow are quite different.  On the left, you see a “strict” morphism that matches each vertex and edge precisely, preserving the structure down to the last detail. On the right, a more relaxed morphism allows edges to “collapse” or be identified in a simpler way—perhaps merging two edges into one if they serve the same role. Both are legitimate ways to interpret “graph morphisms,” yet they impose very different constraints on how graphs can transform.

<img src="images/morphisms2.jpeg"  width="400"/>

At first glance, it may seem that studying nodes and edges directly is enough. But these pictures nudge us to shift our perspective. We start thinking less about the specific shapes of graphs and more about the *rules* that define them. For instance, is more than one edge between two vertices allowed? Can loops exist? Does a morphism have to map edges to edges, or can it send edges to vertices? Each choice leads to a different notion of “graph,” and correspondingly different morphisms.

<img src="images/perspective.jpeg"  width="400"/>

Why formalize these rules? One reason is that we can bring them under the umbrella of the familiar set-theoretic framework. Each graph type is essentially a set of vertices plus a certain structure/set of rules that go with it. By classifying the ways we treat edges and morphisms, we’re using these set axioms to impose universal properties: do we have products of graphs in this category? Are there exponentials? How do we form subobjects? In effect, we are leveraging the fundamental language of sets to build consistent categories of graphs.

<img src="images/axioms.jpeg"  width="400"/>

Finally, one might ask: Why bother? The short answer is that this classification opens up a broader toolbox for both theory and applications. When you know exactly which “graph type” and which "morphism type" you are dealing with — whether it’s a simple loopless graph or a general graph with flexible morphisms — you gain clarity about what kind of constructions and transformations are valid. This in turn leads to powerful insights in modeling complex systems: from simplifying a transportation network by merging parallel routes (a more relaxed morphism) to analyzing a strict hierarchy of relationships (a strict morphism). By understanding the deeper logic that emerges from set axioms and universal constructions, you not only unify different graph definitions under one conceptual roof but also enable the transfer of results across seemingly disparate areas. 

When we examine graphs through the lens of set axioms, we shift our focus from listing nodes and edges to uncovering the universal principles that govern how these elements combine and interact. This approach treats graphs as objects defined by a set of rules—rules that dictate everything from the multiplicity of connections to the ways in which graphs can be transformed via morphisms.
At the heart of this perspective is the realization that set axioms provide a common language for defining structure. For example, by imposing axioms that specify limits, colimits, or even exponential objects, we can rigorously classify graph types. What emerges on the “other side” of this prism is a collection of universal properties that reveal which constructions are possible in a given category of graphs. 
These properties might tell us, for instance, whether the category supports an internal notion of a “function space”. For a graph category where this rule holds, we can construct an internal “function space” that represents all morphisms from one graph to another.  Having this structure is tremendously useful when you want to, say, build models of network evolution where the “rules” of transformation can themselves be studied categorically. Conversely, if the rule fails, it tells you that such internal homs do not exist, so you must resort to external, less intrinsic methods to analyze the space of graph mappings. This outcome is not merely a theoretical curiosity—it directly affects how one designs algorithms for network analysis and transformation.

In short, by filtering graph definitions through set axioms, we gain a clear, abstract blueprint of how graphs behave and interact.

**What is a conceptual Graph?**

**Definition 1** (Conceptual Graph). A conceptual graph $G$ consist of $G=(P(G), V(G); \partial_G:P(G) \rightarrow V(G)\times V(G), \iota_G: V(G) \hookrightarrow P(G) )$ where 
- $P(G)$ is the set of parts of $G$,
- $V(G)$ is the set of vertices of the graph $G$,
- $V(G) \times V(G)$ is the set of unordered pairs of vertices of $G$,
- $\partial_G$ is the incidence map and $\iota_G$ is the inclusion map of the vertex set into the part set.

The set of parts is created by joining the set of vertices and the set of edges. So the set of edges $E(G)$ can be defined by $E(G) = P(G) / \iota_G(V(G))$. The set of unordered pairs of vertices of $G$ is the set of all posible pair of vertices in a graph $G$, where the order of the vertices in each pair does not matter. So this paper works with undirected graphs. We can already consider the question of whether the definition of conceptual graphs can be broadened to include directed graphs? 
<!-- This is the command for the images, no se como especifica el nombre de la imagen
<img src="https://raw.githubusercontent.com/appliedcategorytheory/appliedcategorytheory.github.io/master/images/2022-blog-posts/3A/placeholder" alt="You alt text here."/> 
-->


**Definition 2** (Graph Homomorphism). $f:G \rightarrow H$ is a graph (homo)morphism of conceptual graphs from $G$ to $H$ if $f$ is a function 
- $f_P:P(G) \rightarrow P(H)$ and
- $f_V:V(G) \rightarrow V(H)$ that preserves incidence, that is to say,

$\partial_H(f_P(e))=f_V(x) \sim f_V(y)$ whenever $\partial_G(e) = x \sim y$ for all $e \in P(G)$, $x,y \in V(G)$.

<img src="images/grapHomo.png" alt="Table" width="400"/>

This definition allows a graph morphism to map an edge to a vertex as long as the incidence of the edges are preserved. 
When can I map an edge to a vertex, in such a way that the condition of incidence is preserved? There are two ways

1. an edge that is a loop maps to a vertex
2. an non loop edge to a vertex, implies to send the both vertices in the same vertex.

**Definition 3** (Strict Homomorphism). Let $G$ and $H$ be conceptual graphs. A strict graph (homo)morphism $f:G \rightarrow H$ is a graph morphism such that **the strict condition holds**: 

- for all edges $e \in E(G), f_P(e) \in E(H)$. 

This definition only allows mapping edges to edges, not vertices, or, we can say that strict morphisms map an edge part to a strict edge part.

We have other types of graphs. The graphs restricted to allow at most one edge between any two vertices and at most one loop, we will call **simple graph**. This is the incidence map in injective. (**example**)

Other common restriction is a graph without loops, that we will call a **loopless graph**.

The categories that we can define are a special type, they are concrete categories...

**Definition 4** (Concrete Categories) Concrete categories are categories whose objects are sets with structure and whose morphisms are functions that preserve that structure.

We have six concrete categories of graphs

1. **Grphs** The Category of Conceptual Graphs where
   - the objects are **conceptual graphs** and
   - the morphisms are *conceptual graph morphisms*.
2. **SiGrphs** The Category of  Simple Graphs where
   - the objects are **simple graphs** and
   - the morphisms are *conceptual graph morphisms*.
3. **SiLlGrphs** The Category of  Simple Loopless Graphs where
   - the objects are **simple graphs without loops** and
   - the morphisms are *conceptual graph morphisms*.
4. **StGrphs** The Category of Conceptual Graphs with Strict Morphisms where
   - the objects are **conceptual graphs** and
   - the morphisms are *strict graph morphisms*.
5. **SiStGrphs** The Category of  Simple Graphs with Strict Morphisms where
    - the objects are **simple graphs** and
    - the morphisms are *strict graph morphisms*.
6. **SiLlStGrphs** The Category of  Simple Loopless Graphs with Strict Morphisms where
    - the objects are **simple graphs without loops** and
    - the morphisms are *strict graph morphisms*.


For brevity, we will denote **Grphs** by **G**. 

<img src="images/CategoriesDiagram.png" alt="Table" width="400"/>

**The Categories of Graphs vs The Category of Sets**

**The Lawvere axioms for Sets**
- (L1) **Sets** has Limits (Colimits).
- (L2) **Sets** has exponentiation with evaluation.
- (L3) **Sets** has a subobject classifier.
- (L4) **Sets** has a natural number object.
- (L5) **Sets** has the Axiom of Choice.
- (L6) The subobject classifier in **Sets** is two valued.

**Proposition 1.** **G** satisfies axioms (L1), (L3), and (L4) and does not satisfy axioms (L2), (L5), and (L6). 

**Proposition 2.** **SiG** satisfies axioms (L1), (L3), and (L4) and does not satisfy axioms (L2), (L5), and (L6). 

**Proposition 3.** **SiLlG** satisfies axioms (L1), (L2), and (L4) and does not satisfy axioms (L3), (L5), and (L6). 

**Proposition 4.** **StG** satisfies axioms (L1), (L3), and (L4) and does not satisfy axioms (L2), (L5), and (L6). 

**Proposition 5.** **SiStG** satisfies axioms (L1), (L2), and (L4) and does not satisfy axioms (L3), (L5), and (L6). 

**Proposition 6.** **SiLlStG** does not satisfy any of the axioms of the **Sets**.

We have that, except for **SiLlStG**, all categories satisfy (L1) and (L4), do not satisfy (L5) and (L6), and differ in the axioms (L2) and (L3). 

## **G**,  **SiG**, **SiLlG**, **StG**, and **SiStG** have Limits and Colimits

Limits and colimits exist by the existence of a

- a terminal object,
- products/coproducts and
- equalizers/coequalizers $\sim$ pullback/pushouts.

For **G**, **SiG** and **SiLlG** the terminal object denoted by $\hat{1}$ is the single vertex:

<img src="images/TerminalObjectA.png" alt="Table" width="100"/>

For **StG** and **SiStG** the terminal object is one vertex and one loop:

<img src="images/TerminalObjectB.jpeg" alt="Table" width="200"/>

**SiLlStG** does not have terminal object.

*Proof:* 

**($\times$) Products/ ($+$) Coproducts**

**Pullback/ Pushouts**

What do pullbacks and pushouts mean in the category of graphs? How can we understand these concepts when we specialize to graphs?

In the example, we have the pushout $P'$ of $f$ and $g$:

<img src="images/PushoutExample.png" alt="Table" width="700"/>

## (L2) Exponentiation with Evaluation

## (L3) Subobject Classifier

<img src="images/SubODiagram.png" alt="Table" width="200"/>

The subobject classifier for **G** and **SiG** is the graph denoted by $\Omega$

<img src="images/SubobjectGSiG.png" alt="Table" width="300"/>


**In summary**

<img src="images/table1.png" alt="Table" width="650"/>



