# Shapes/Slotted E-graphs

## Definition slotted e-graph 

Similar to (regular) egraphs:

- function symbols $f,g$
- e-class ids $a,b$
- slots $s_1, s_2, \ldots$
- slotmap $m$ ::= $s_j \mapsto s_k, \ldots$ bijection.
- invocation $i$ ::= $a * m$
- terms   $t$ ::= $ f | f(t_1, \ldots, t_k) | s_j | \lambda s_j.t$
- e-nodes $n$ ::= $ f | f(i_1, \ldots, i_k) | s_j | \lambda s_j.i$
- e-classes $c$ ::= $ \{ n_1, \ldots n_m \}[s_1, \ldots, s_m]$

We have a mapping $\operatorname{Classes} : \operatorname{Id} \rightarrow \operatorname{Eclass}$ to interpret the invocations. 

# Additional definitions

## Action

- $f * m := f$
- $m_1 * m_2 := m_2 \circ m_1$
- $(a * m_1) * m_2 := a (m_1 * m_2)$
- $f(t_1, \ldots, t_k) * m := f(t_1 * m, ldots, t_k * m)$
- $f(i_1, \ldots, i_k) * m := f(i_1 * m, ldots, i_k * m)$
- $(\lambda s_j.t) * m := \lambda s_j. (t * m)$, assuming $s_j$ is neither in the domain nor codomain of $m$.
- $(\lambda s_j.i) * m := \lambda s_j. (i * m)$, assuming $s_j$ is neither in the domain nor codomain of $m$.
- $s_j * m := m(s_j)$
- $x*m$ is only defined, if $\operatorname{slots}(x) \subseteq \operatorname{dom}(m)$
- We follow the Barendregt convention: We assume that all bound slots are never colliding with anything else. And if they do, we just rename them.
(- Note to future self: We also need the Barendregt convention for redundant slots)

## Slots
We define a family of (overloaded) functions $\operatorname{slots}$ from every syntactic construct to a set of slots $\{ s_1, \ldots, s_k \}$.
- $\operatorname{slots} : \{ n_1, \ldots n_m \}[s_1, \ldots, s_m] \mapsto \{s_1, \ldots, s_m \}$
- $\operatorname{slots}(x*m) := \operatorname{slots}(x) \circ m = \{m(s_i) | s_i \in \operatorname{slots(x)} \}$
- $\operatorname{slots}(\lambda s_i.t) := \operatorname{slots}(t) \setminus \{ s_i \}$
- $\operatorname{slots}(\lambda s_i.n) := \operatorname{slots}(n) \setminus \{ s_i \}$
- $\ldots$ (for later)

## Examples: 
- $ \operatorname{slots}(\lambda s_1. f(s_1,s_2,s_3)) = \operatorname{slots}(f(s_1,s_2,s_3)) \setminus \{s_1\} = \{s_2, s_3\}$ 
- $ \lambda s_1. f(s_1,s_2,s_3) * (s_1 \to s_2, s_2 \to s_3,s_3 |-> s_1)$ does not typecheck
- $ \lambda s_1. f(s_1,s_2,s_3) * (s_2 \to s_3,s_3 \to s_1) = \lambda s_1. f(s_1,s_2,s_3)$ needs freshness
- $ a[s_2  \to s_3, s_47 \to s_2] =  a[s_47 \to s_2, s_2 \to s_3]; slots(a) = \{ s_2, s_47 \}$

# Containment
We define an element relation $\in \subseteq \operatorname{Enodes} \times \operatorname{Invocations}$, defined recursively as:
- If an E-node $n$ is contained in an e-class $c$ (set containment), then $n \in c[id_{\operatorname{slots}(c)}]$, where $id_{\operatorname{slots}(c)} = s_i \mapsto s_i$ is the identity slotmap on the set of slots $\operatorname{slots}(c)$.
- If $n \in a$, then $n*m \in a*m$.

# Properties

- Eclasses are disjoint.
- $m$s are (partial) permutations, i.e. bijections (?)


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
- example: $classes(a) = {(f s_10 s_11), (f s_11 s_10)}[s_10,s_11]$
  enodes: $(+ a[s_10 \to s_1,s_11 \to s_2] a[s_10 \to s_2,s_11 \to s_1])$ and $(+ a[s_10 \to s_1,s_11 \to s_2] a[s_10 \to s_1,s_11 \to s_2])$
  concrete terms correspond to $ (+ (f x y) (f y x))$ and $(+ (f x y) (f x y))$
- strong shape: lex-min of all equivalent weak shapes.
- Conjecture: this is the double coset contstructive orbit problem


## Open questions
- Is $\lambda$ above the most generic possible, or are there examples of languages where the binders cannot be expressed this way?
