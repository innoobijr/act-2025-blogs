
**What is a conceptual Graph?**

**Definition 1** (Conceptual Graph). A conceptual graph $G$ consist of $G=(P(G), V(G); \partial_G:P(G) \rightarrow V(G)\times V(G), \iota_G: V(G) \hookrightarrow P(G) )$ where 
- <span style="color: BlueViolet;">$P(G)$ </span> is the set of parts of $G$,
- $V(G)$ is the set of vertices of the graph $G$,
- $V(G) \times V(G)$ is the set of unordered pairs of vertices of $G$,
- $\partial_G$ is the incidence map and $\iota_G$ is the inclusion map of the vertex set into the part set.
- 
<img src="images/Screenshot 2025-05-29 at 16.45.04.png" alt="Table" width="800"/>

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

   




