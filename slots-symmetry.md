# Shapes/Slotted E-graphs

## Definition slotted e-graph 

Similar to (regular) egraphs:

- function symbols $f,g$
- e-class ids $a,b$
- slots $s_1, s_2, \ldots$
- slotmap $m$ ::= $[s_j \mapsto s_k, \ldots]$ bijection
- invocation $i$ ::= $m * a$
- terms   $t$ ::= $f~|~f(t_1, \ldots, t_k)~|~s_j~|~\lambda s_j.t$
- e-nodes $n$ ::= $f~|~f(i_1, \ldots, i_k)~|~s_j~|~\lambda s_j.i$
- e-classes $c$ ::= $\{ n_1, \ldots n_m \} :: \{s_{i_1}, \ldots, s_{i_k}\}$

We have a mapping $\operatorname{Classes} : \operatorname{Id} \rightarrow \operatorname{Eclass}$ to interpret the invocations. 

# Additional definitions

## slots(\_)
We define a family of (overloaded) functions $\operatorname{slots}$ for e-class ids, invocations, terms and e-nodes to a set of slots $\{ s_{i_1}, \ldots, s_{i_k} \}$.

#### slots(\_) for E-Class Id and Invocations
- $\operatorname{slots}(a) := \{s_1, \ldots, s_m \}$, given $\operatorname{Classes}(a) = \{ n_1, \ldots, n_k \} :: \{s_1, \ldots, s_m\}$
- $\operatorname{slots}(m*a) := m \circ \operatorname{slots}(a) = \{m(s_j)~\mid~ s_j \in \operatorname{slots(a)} \}$

#### slots(\_) for Terms and E-Nodes
Let $x, x_1, \ldots$ be either terms $t$ or invocations $i$.

- $\operatorname{slots}(f) := \emptyset$
- $\operatorname{slots}(f(x_1, \ldots, x_k)) := \operatorname{slots}(x_1) \cup \ldots \cup \operatorname{slots}(x_k)$
- $\operatorname{slots}(s_j) := \{s_j\}$
- $\operatorname{slots}(\lambda s_j.x) := \operatorname{slots}(x) \setminus \{ s_j \}$

$\operatorname{slots(t)}$ on terms corresponds to the set of free variables.

## The Action m * \_
We define a family of (overloaded) functions $m * \_$ for e-class ids, invocations, terms and e-nodes.

Generally, $m*x$ is only defined, if $\operatorname{slots}(x) \subseteq \operatorname{dom}(m)$.

- For any $x$ we have $m*(m'*x) = (m'*m)*x$
- with $m*m' := m \circ m' = \{ x \mapsto z ~|~ x \mapsto y \in m', y \mapsto z \in m \}$

#### m * \_ for E-Class Ids and Invocations
- $m*a$ is just $m*a$, there is no way to simplify it
- For Invocations $i = m*a$, we define $m'*i := (m'*m)*a$

#### m * \_ for Terms and E-Nodes
Let $x, x_1, \ldots$ be either terms $t$ or invocations $i$.

- $m * f := f$
- $m * f(x_1, \ldots, x_k) := f(m * x_1, \ldots, m * x_k)$
- $m * s_j := m(s_j)$
- $m * (\lambda s_j.x):= \lambda s_j. (m * x)$, assuming $s_j$ is neither in the domain nor codomain of $m$.

We follow the Barendregt convention: We assume that all bound slots are never colliding with anything else. And if they do, we just rename them.

(Note to future self: We also need the Barendregt convention for redundant slots)

We claim that this definition implies $\operatorname{slots}(m*x) = m \circ \operatorname{slots}(x)$ for all $x$.

## Examples: 
- $\operatorname{slots}(\lambda s_1. f(s_1,s_2,s_3)) = \operatorname{slots}(f(s_1,s_2,s_3)) \setminus \{s_1\} = \{s_2, s_3\}$ 
- $\lambda s_1. f(s_1,s_2,s_3) * (s_1 \mapsto s_2, s_2 \mapsto s_3,s_3 \mapsto s_1)$ does not typecheck
- $\lambda s_1. f(s_1,s_2,s_3) * (s_2 \mapsto s_3,s_3 \mapsto s_1) = \lambda s_1. f(s_1,s_2,s_3)$ needs freshness
- $[s_2 \mapsto s_3, s_{47} \mapsto s_2] * a =  [s_47 \mapsto s_2, s_2 \mapsto s_3] * a; \operatorname{slots}(a) = \{ s_2, s_47 \}$

# Containment
We define an element relation $\in \subseteq \operatorname{Enodes} \times \operatorname{Invocations}$, defined recursively as:

- If an E-node $n$ is contained in an e-class $\operatorname{Classes}(a)$ (set containment), then $n \in a * id_{\operatorname{slots}(a)}$, where $id_{\operatorname{slots}(a)}(s_j) = s_j$ is the identity slotmap on the set of slots $\operatorname{slots}(a)$.
- If $n \in i$, then $m*n \in m*i$.

## Notes
- We need to be more precise about the slots of e-nodes and e-classes (union/intersection, etc)
- We allow the shortcut for ordered (instead of named) arguments, $[s_j, s_{j'}, s_{j''}, ...] * i := [s_k \mapsto s_j, s_{k'} \mapsto s_{j'}, s_{k''} \mapsto s_{j''}, ...] * i$, assuming $slots(i) = \{s_k, s_{k'}, s_{k''}, ...\}$ and $k < k' < k'' < ...$.
  
## Group Action

Let $G \leq \operatorname{Sym}(s_1,\ldots,s_m)$, then $G$ acts on the set of enodes $n[s_1,\ldots,s_m]$ the obvious way $g n[s_1,\ldots,s_m] = n[g s_1, \ldots, g s_m]$. 

Similarly, $G \subseteq \operatorname{Sym}(s_1,\ldots,s_m)$ acts on the e-class $c[s_1, \ldots, s_m] = \{n_1, \ldots, n_k\}$ element-wise, i.e. $g c = \{ g n_1, \ldots, g n_k \}$ (Note: this doesn't type check, we need to consider the appropriate restrictions of the groups acting on the different sets of slots).

The automorphism group $\operatorname{Aut}(c)$ of an e-class is the largest subgroup $\operatorname{Aut}(c) \leq \operatorname{Sym}(s_1,\ldots,s_m)$, such that $g c = c$ for all $g \in \operatorname{Aut}(c)$.

## Strong shape computation

- enodes must be hashable
- hash must be invariant of renamings
- weak shape: canonical naming $s_1, s_2, \ldots$
- egraph idea: congruence, i.e. if $a = b \Rightarrow f(a) = f(b)$ (in memory). This does not work in weak shapes:
- example: $\operatorname{Classes}(a) = \{(f s_10 s_11), (f s_11 s_10)\} :: \{s_10,s_11\}$
  enodes: $(+ [s_10 \to s_1,s_11 \to s_2]*a [s_10 \to s_2,s_11 \to s_1]*a)$ and $(+ [s_10 \to s_1,s_11 \to s_2]*a [s_10 \to s_1,s_11 \to s_2]*a)$
  concrete terms correspond to $ (+ (f x y) (f y x))$ and $(+ (f x y) (f x y))$
- strong shape: lex-min of all equivalent weak shapes.
- Conjecture: this is the double coset contstructive orbit problem


## Open questions
- Is $\lambda$ above the most generic possible, or are there examples of languages where the binders cannot be expressed this way?
