# Shapes/Slotted E-graphs

## Definition slotted e-graph 

Similar to (regular) egraphs:

- function symbols $f,g$
- e-class ids $a,b$
- slots $s_1, s_2, \ldots$
- slotmap $m$ ::= $[s_j \mapsto s_k, \ldots]$ bijection
- invocation $i$ ::= $a * m$
- terms   $t$ ::= $f | f(t_1, \ldots, t_k) | s_j | \lambda s_j.t$
- e-nodes $n$ ::= $f | f(i_1, \ldots, i_k) | s_j | \lambda s_j.i$
- e-classes $c$ ::= $\{ n_1, \ldots n_m \} ::: \{s_1, \ldots, s_m\}$

We have a mapping $\operatorname{Classes} : \operatorname{Id} \rightarrow \operatorname{Eclass}$ to interpret the invocations. 

# Additional definitions

## slots(\_)
We define a family of (overloaded) functions $\operatorname{slots}$ for e-class ids, invocations, terms and e-nodes to a set of slots $\{ s_1, \ldots, s_k \}$.

#### slots(\_) for E-Class Id and Invocations
- $\operatorname{slots}(a) := \{s_1, \ldots, s_m \}$, given $Classes(a) = \{ n_1, \ldots, n_k \} ::: \{s_1, \ldots, s_m\}$
- $\operatorname{slots}(a*m) := \operatorname{slots}(a) \circ m = \{m(s_j) | s_j \in \operatorname{slots(a)} \}$

#### slots(\_) for Terms and E-Nodes
Let $x, x_1, \ldots$ be either terms $t$ or invocations $i$.

- $\operatorname{slots}(f) := \emptyset$
- $\operatorname{slots}(f(x_1, \ldots, x_k)) := \operatorname{slots}(x_1) \cup \ldots \cup \operatorname{slots}(x_k)$
- $\operatorname{slots}(s_j) := \{s_j\}$
- $\operatorname{slots}(\lambda s_j.x) := \operatorname{slots}(x) \setminus \{ s_j \}$

$\operatorname{slots(t)}$ on terms corresponds to the set of free variables.

## The Action \_ * m
We define a family of (overloaded) functions $\_ * m$ for e-class ids, invocations, terms and e-nodes.

Generally, $x*m$ is only defined, if $\operatorname{slots}(x) \subseteq \operatorname{dom}(m)$.

- For any $x$ we have $(x*m)*m' = x*(m*m')$
- with $m*m' := m' \circ m = \{ x \mapsto z ~|~ x \mapsto y \in m, y \mapsto z \in m' \}$

#### \_ * m for E-Class Ids and Invocations
- $a*m$ is just $a*m$, there is no way to simplify it
- For Invocations $i = a*m'$, we define $i*m := a*(m'*m)$

#### \_ * m for Terms and E-Nodes
Let $x, x_1, \ldots$ be either terms $t$ or invocations $i$.

- $f * m := f$
- $f(x_1, \ldots, x_k) * m := f(x_1 * m, \ldots, x_k * m)$
- $s_j * m := m(s_j)$
- $(\lambda s_j.x) * m := \lambda s_j. (x * m)$, assuming $s_j$ is neither in the domain nor codomain of $m$.

We follow the Barendregt convention: We assume that all bound slots are never colliding with anything else. And if they do, we just rename them.

(Note to future self: We also need the Barendregt convention for redundant slots)

We claim that this definition implies $slots(x*m) = m \circ slots(x)$ for all $x$.

## Examples: 
- $\operatorname{slots}(\lambda s_1. f(s_1,s_2,s_3)) = \operatorname{slots}(f(s_1,s_2,s_3)) \setminus \{s_1\} = \{s_2, s_3\}$ 
- $\lambda s_1. f(s_1,s_2,s_3) * (s_1 \mapsto s_2, s_2 \mapsto s_3,s_3 \mapsto s_1)$ does not typecheck
- $\lambda s_1. f(s_1,s_2,s_3) * (s_2 \mapsto s_3,s_3 \mapsto s_1) = \lambda s_1. f(s_1,s_2,s_3)$ needs freshness
- $a[s_2 \mapsto s_3, s_47 \mapsto s_2] =  a[s_47 \mapsto s_2, s_2 \mapsto s_3]; slots(a) = \{ s_2, s_47 \}$

# Containment
We define an element relation $\in \subseteq \operatorname{Enodes} \times \operatorname{Invocations}$, defined recursively as:

- If an E-node $n$ is contained in an e-class $Classes(a)$ (set containment), then $n \in a[id_{\operatorname{slots}(a)}]$, where $id_{\operatorname{slots}(a)}(s_j) = s_j$ is the identity slotmap on the set of slots $\operatorname{slots}(a)$.
- If $n \in i$, then $n*m \in i*m$.

## Notes
- We need to be more precise about the slots of e-nodes and e-classes (union/intersection, etc)
- We allow the shortcut for ordered (instead of named) arguments, $i[s_j, s_{j'}, s_{j''}, ...] := i[s_k -> s_j, s_{k'} -> s_{j'}, s_{k''} -> s_{j''}, ...]$, assuming $slots(i) = \{s_k, s_{k'}, s_{k''}, ...\}$ and $k < k' < k'' < ...$.
  
## Group Action

Let $G \leq \operatorname{Sym}(s_1,\ldots,s_m)$, then $G$ acts on the set of enodes $n[s_1,\ldots,s_m]$ the obvious way $g n[s_1,\ldots,s_m] = n[g s_1, \ldots, g s_m]$. 

Similarly, $G \subseteq \operatorname{Sym}(s_1,\ldots,s_m)$ acts on the e-class $c[s_1, \ldots, s_m] = \{n_1, \ldots, n_k\}$ element-wise, i.e. $g c = \{ g n_1, \ldots, g n_k \}$ (Note: this doesn't type check, we need to consider the appropriate restrictions of the groups acting on the different sets of slots).

The automorphism group $\operatorname{Aut}(c)$ of an e-class is the largest subgroup $\operatorname{Aut}(c) \leq \operatorname{Sym}(s_1,\ldots,s_m)$, such that $g c = c$ for all $g \in \operatorname{Aut}(c)$.

## Strong shape computation

- enodes must be hashable
- hash must be invariant of renamings
- weak shape: canonical naming $s_1, s_2, \ldots$
- egraph idea: congruence, i.e. if $a = b -> f(a) = f(b)$ (in memory). This does not work in weak shapes:
- example: $classes(a) = \{(f s_10 s_11), (f s_11 s_10)\} ::: \{s_10,s_11\}$
  enodes: $(+ a[s_10 \to s_1,s_11 \to s_2] a[s_10 \to s_2,s_11 \to s_1])$ and $(+ a[s_10 \to s_1,s_11 \to s_2] a[s_10 \to s_1,s_11 \to s_2])$
  concrete terms correspond to $ (+ (f x y) (f y x))$ and $(+ (f x y) (f x y))$
- strong shape: lex-min of all equivalent weak shapes.
- Conjecture: this is the double coset contstructive orbit problem


## Open questions
- Is $\lambda$ above the most generic possible, or are there examples of languages where the binders cannot be expressed this way?
