# Shapes/Slotted E-graphs

## Definition slotted e-graph 

Similar to (regular) egraphs:

- function symbols $f,g$
- e-class ids $a,b$
- slots $s_1, s_2, \ldots$
- slotmap $m ::= s_i \mapsto s_j, \ldots$
- invocations $a[m], b[m]$
- terms   $t ::= f | f(t_1,    \ldots, t_k   ) | s_i | \lambda s_i.t$
- e-nodes $n ::= f | f(a_1[m], \ldots, a_k[m]) | s_i | \lambda s_i.a[m]$
- e-classes $c ::= \{ n_1, \ldots n_m \}[s_1, \ldots, s_m]$

We have a mapping $\operatorname{Classes} : \operatorname{Id} \rightarrow \operatorname{Eclass}$ to interpret the invocations. 

# Additional definitions

## Action

- $a * m := a[m]$
- $f * m := f$
- $m_1 * m_2 := m_2 \circ m_1$
- $a[m_1] * m_2 := a[m_1 * m_2]$
- $f(t_1, \ldots, t_k) * m := f(t_1 * m, ldots, t_k * m)$
- $f(a_1, \ldots, a_k) * m := f(a_1 * m, ldots, a_k * m)$
- $(\lambda s_i.t) * m := \lambda s_i. (t * m)$, assuming $s_i$ is neither in the domain nor codomain of $m$.

- $x*m$ is only defined, if $\operatorname{dom}(m) = \operatorname{slots}(x)$
- We follow the Barendregt convention: We assume that all bound slots are never colliding with anything else. And if they do, we just rename them.
(- Note to future self: We also need the Barendregt convention for redundant slots)
(- Note to future self: Maybe we should deprecate the invocation syntax $a[m]$ in favor of the more general $a*m$)

## Slots
We define a family of (overloaded) functions $\operatorname{slots}$ from every syntactic construct to a set of slots $\{ s_1, \ldots, s_k \}$.
- $\operatorname{slots} : \{ n_1, \ldots n_m \}[s_1, \ldots, s_m] \mapsto \{s_1, \ldots, s_m \}$
- $\operatorname{slots}(x*m) := \operatorname{slots}(x) \circ m = \{m(s_i) | s_i \in \operatorname{slots(x)} \}$
- $\operatorname{slots}(\lambda s_i.t) := \operatorname{slots}(t) \setminus \{ s_i \}$
- $\operatorname{slots}(\lambda s_i.n) := \operatorname{slots}(n) \setminus \{ s_i \}$
- $\ldots$ (for later)

## Examples: 
- $ \operatorname{slots}(\lambda s_1. f(s_1,s_2,s_3)) = \operatorname{slots}(f(s_1,s_2,s_3)) \setminus \{s_1\} = \{s_2, s_3\}$ 
- $ \lambda s_1. f(s_1,s_2,s_3) * (s_1 |-> s_2, s_2 |-> s_3,s_3 |-> s_1)$ does not typecheck
- $ \lambda s_1. f(s_1,s_2,s_3) * (s_2 |-> s_3,s_3 |-> s_1) = \lambda s_1. f(s_1,s_2,s_3)$ needs freshness




# Containment
We define an element relation $\in \subseteq \operatorname{Enodes} \times \operatorname{Invocations}$, defined recursively as:
-- If an E-node $n$ is contained in an e-class $c$ (set containment), then $n \in c[id_{\operatorname{slots}(c)}]$, where $id_{\operatorname{slots}(c)} = s_i \mapsto s_i$ is the identity slotmap on the set of slots $\operatorname{slots}(c)$.

# Properties

- Eclasses are disjoint.



## Notes
- We need to be more precise about the slots of e-nodes and e-classes (union/intersection, etc)
  
## Group Action

Let $G \leq \operatorname{Sym}(s_1,\ldots,s_m)$, then $G$ acts on the set of enodes $n[s_1,\ldots,s_m]$ the obvious way $g n[s_1,\ldots,s_m] = n[g s_1, \ldots, g s_m]$. 

Similarly, $G \subseteq \operatorname{Sym}(s_1,\ldots,s_m)$ acts on the e-class $c[s_1, \ldots, s_m] = \{n_1, \ldots, n_k\}$ element-wise, i.e. $g c = \{ g n_1, \ldots, g n_k \}$ (Note: this doesn't type check, we need to consider the appropriate restrictions of the groups acting on the different sets of slots).

The automorphism group $\operatorname{Aut}(c)$ of an e-class is the largest subgroup $\operatorname{Aut}(c) \leq \operatorname{Sym}(s_1,\ldots,s_m)$, such that $g c = c$ for all $g \in \operatorname{Aut}(c)$.



## Open questions
- Is $\lambda$ above the most generic possible, or are there examples of languages where the binders cannot be expressed this way?
