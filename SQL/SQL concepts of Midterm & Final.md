# DBMS concepts related to Midterm & Final
## Table of Contents
### Concepts related to Midterm
* [NULL in Arithmetric expression](#nullinarithmetricexpression)
* [NULL in Boolean comparison](#nullinbooleancomparison)
* [OR & AND combinated with TRUE, FALSE, UNKNOWN](#orandtruefalseunknown)

### Concepts related to Final exam
* [Key](#key)
* [Normalization](#normalization)
  * [Questions](#normalization_questions)
* [B+ tree](#b+tree)
  * [Questions](#b+_tree_questions)
* [Transaction](#transaction)
  * [Questions](transaction_questions)

## NULL in Arithmetric expression<a name="nullinarithmetricexpression"></a>

**The result of any arithmetric expression involving `NULL` is `NULL`**

For example, `NULL` + 5 returns `NULL`，`NULL` - 5 returns `NULL`，`NULL` * 5 returns `NULL`，`NULL` / 5 returns `NULL`，`NULL` % 5 returns `NULL`.

## NULL in Boolean comparison<a name="nullinbooleancomparison"></a>

**The Boolean comparison expression involving `NULL` evaluates to `FALSE` in SQL server**

For example, 
* `NULL` = `NULL` returns `UNKNOWN`, and then `UNKNOWN` is evaluated by SQL to `FALSE`
* `NOT` (10 < `NULL`) equals to `NOT` `UNKNOWN`, returns `UNKNOWN`, and then `UNKNOWN` is evaluated by SQL to `FALSE`

Actually, in SQL, `NULL` is considered as "unknown" in that case (or "does not exist"). For `NULL` = `NULL`, we don't know both the `NULL` on the left and the `NULL` on the right are both "unknown", or both "does not exist", or one "unknown" and one "does not exist". Hence, `NULL` = `NULL` evaluates to `FALSE`.

<img width="762" alt="image" src="https://github.com/Hehua-Fan/LeetCode-SQL70-Notes/assets/95437839/92c92fa4-cecc-407a-a34c-795852dde6a5">




## OR & AND combinated with TRUE, FALSE, UNKNOWN<a name="orandtruefalseunknown"></a>

**`OR`的优先级：TRUE > UNKNOWN > FALSE**

**`AND`的优先级：FALSE > UNKNOWN > TRUE**

| AND($\wedge$)    | OR($\vee$)     | NOT       |
| ---------------- | -------------- | --------- |
| F $\wedge$ F= F  | T $\vee$ T = T | NOT T = F |
| F $\wedge$ U = F | T $\vee$ U = T | NOT F = T |
| F $\wedge$ T = F | T $\vee$ F = T | NOT U = U |
| U $\wedge$ U = U | U $\vee$ U = U |           |
| U $\wedge$ T = U | U $\vee$ F = U |           |
| T $\wedge$ T = T | F $\vee$ F = F |           |

## Key<a name="key"></a>

**A candidate key** is a key that uniquely identifies rows in a table. Any of the identified candidate keys can be used as the table's primary key. Candidate keys that are not part of the primary key are called **alternate keys**. One can describe a candidate key as **a super key** that contains only the minimum number of columns necessary to determine uniqueness.

**Prime attributes** are the attributes of the candidate key which defines the uniqueness (Eg: SSN number in an employee database)

**A primary key** is a column in a table whose values uniquely identify the rows in the table. The primary key is chosen from this list of candidates based on its perceived value to the business as an identifier.

A primary key value:

- Must uniquely identify the row;
- cannot have NULL values;
- Should not change over the time;
- and Should be as short as possible.

If the primary key is a combination of more than one column then it is called as **composite key**.
<img width="487" alt="image" src="https://github.com/Hehua-Fan/LeetCode-SQL70-Notes/assets/95437839/8b504b2b-80ce-4a44-a8d2-a98aff65b519">


## Normalization<a name="normalization"></a>
**0. if a table only has trivial functional dependency, it could be in 1NF, 2NF, 3NF and BCNF. Because it will not violate the concerns of these rules.**<br>
Non-trivial functional dependency means X $\rightarrow$ Y, where Y is not a set of X. <br>
Trivial functional dependency means X $\rightarrow$ Y, where Y is a set of X.

**1. The Boyce-Codd Normal Form (BCNF) is a stricter version of the Third Normal Form (3NF). For a table to be in BCNF:**<br>
- It should be in 3NF.
- For every non-trivial functional dependency X $\rightarrow$ Y:
  - X should be a superkey.
- It only has trivial functional dependency.

**2. The Third Normal Form (3NF) is a stricter version of the Second Normal Form (2NF). For a table to be in 3NF:**<br>
- It should be in 2NF.
- For every non-trivial functional dependency X $\rightarrow$ Y:
  - X should be a superkey, or
  - Y should be a prime attribute (i.e., a part of a candidate key)
- It only has trivial functional dependency.

**3.Closure of Functional Dependency**

[Tutorial of Closure Of Functional Dependency](https://minigranth.in/dbms-tutorial/closure-of-functional-dependency)

- use it to determine if a table is in BCNF or 3NF.
  - compute the superkey at first to check if the table is in BCNF, if not, then check the candidate key, and the prime attribute, to check if the table is in 3NF
  - BTW, We need to check rapidly if it is the candidate key not superkey. How? It needs to remove each attrribute step by step, and then check if the closure of it still includes all attributes.
- use it to determine if decomposition is lessloss-join.
  - Step 1: find R1 $\cap$ R2.
  - Step 2: If the closure of R1 $\cap$ R2 includes all attributes of R1 or all attributes of R2, the decomposition is lossless-join.

### Questions<a name="normalization_questions"></a>

#### Q1. A table is in BCNF if ____

- [x] a left side in each non-trivial functional dependency for that table is a candidate key

- [ ] a right side in each non-trivial functional dependency for that table is a part of a candidate key

- [ ] a left side in each non-trivial functional dependency for that table is a part of a candidate key

- [ ] none of above

**Explaination: This is because, in BCNF, the left-hand side of every non-trivial functional dependency must be a superkey, and a candidate key is a minimal superkey.**

#### Q2. A table is in BCNF if ____

- [ ] a right side in each non-trivial functional dependency for that table is a candidate key

- [ ] a left side in each non-trivial functional dependency for that table is a part of a candidate key

- [ ] a right side in each non-trivial functional dependency for that table is a part of a candidate key

- [x] none of above

**Explaination: a part of a candidate key means prime attributes, prime attributes is not a superkey.**

#### Q3. A table is in 3NF if ____

- [x] a right side in each non-trivial functional dependency for that table is a part of a candidate key

- [ ] a right side in each non-trivial functional dependency for that table is NOT a candidate key

- [ ] a left side in each non-trivial functional dependency for that table is a part of a candidate key

- [ ] none of above

#### Q4. A table is in 3NF if ____

- [x] it has only trivial functional dependencies

- [ ] a left side in each non-trivial functional dependency for that table is NOT a part of candidate key

- [ ] a right side in each non-trivial functional dependency for that table is NOT a part of a candidate key

- [ ] none of above

#### Q5. When a table has only trivial functional dependency it is ____

- [ ] not in BCNF

- [ ] not in 3NF

- [x] both in 3NF and BCNF

- [ ] none of above

#### Q6. Check if the relational schemas R1, R2, R3, R4 specified below are in

(a) BCNF<br>
(b) 3NF

R1(A,B,C,D,E)<br>
Functional dependencies: {CD -> BE, B -> C, B -> D}

R2(A,B,C,D,E)<br>
Functional dependencies: {CD -> BE, B -> CA}

R3(A,B,C,D,E)<br>
Functional dependencies: {CD -> BE, B -> A, B -> CD}

R4(A,B,C,D,E)<br>
Functional dependencies: {CD -> ABE, B -> C}

Your answer should be represented as the following table (please, do not provide any explanation, each
answer should be either “yes”, or “no”):

|      | BCNF ? | 3NF ? |
| :----: | :------: | :-----: |
| R1   | `no`   | `no`  |
| R2   | `no`   | `no`  |
| R3   | `yes`  | `yes` |
| R4   | `no`   | `yes` |

**Explaination: We can use [Closure Of Functional Dependency](https://minigranth.in/dbms-tutorial/closure-of-functional-dependency) to solve this problem.**

———————————————————————————————————————————————————————————————————————

**R1(A,B,C,D,E)**
- CD -> BE
- B -> C
- B -> D

**1.Evaluation for BCNF:**

- Start with CD:<br>
  Closure of CD = {B, C, D, E}<br>
  This means that CD does not determine all attributes in R1. Thus, CD is not a candidate key.<br>
- Start with B:<br>
  Closure of B = {B, C, D, E}<br>
  This means that B does not determine all attributes in R1. Thus, B is not a candidate key.

**In conclusion, it is not in BCNF.**

**2.Evaluation for 3NF:**

- Start with CD $\rightarrow$ BE:<br>
  Due to CD is not a candidate key, we need to check if BE is prime attribute.
- Start with B $\rightarrow$ C:<br>
  Due to B is not a candidate key, we need to check if C is prime attribute.
- Start with B $\rightarrow$ D:<br>
  Due to B is not a candidate key, we need to check if D is prime attribute.
  
  Closure of A = {A}<br>
  Closure of B = {B,C,D,E}<br>
  Closure of C = {C}<br>
  Closure of D = {D}<br>
  Closure of E = {E}
  
  Given the above, AB is a candidate key. <br>
  prime attribute: A,B,C,D (due to AB and ACD are both candidate keys)<br>
  non-prime attribute: E<br>
  So BE is not prime attribute.
  
**In conclusion, it is not in 3NF.**

———————————————————————————————————————————————————————————————————————

**R2(A,B,C,D,E)**
- CD $\rightarrow$ BE
- B $\rightarrow$ CA

**1.Evaluation for BCNF:**

- Start with CD:<br>
  Closure of CD = {A, B, C, D, E}<br>
  This means that CD determines all attributes in R2. Thus, CD is a candidate key.<br>
- Start with B:<br>
  Closure of B = {A, B, C}<br>
  This means that B does not determine all attributes in R2. Thus, B is not a candidate key.

**In conclusion, it is not in BCNF.**

**2.Evaluation for 3NF:**

- Start with CD $\rightarrow$ BE:<br>
  Due to CD is a candidate key, this functional dependency satisfies 3NF.<br>
- Start with B $\rightarrow$ CA:<br>
  Due to B is not a candidate key, we need to check if CA is prime attribute.
  
  Closure of A = {A}<br>
  Closure of B = {A, B, C}<br>
  Closure of C = {C}<br>
  Closure of D = {D}<br>
  Closure of E = {E}
  
  Given the above, BDE is also a candidate key. <br>
  prime attribute: B,C,D,E (due to CD and BDE are both candidate keys)<br>
  non-prime attribute: A
  

**In conclusion, it is not in 3NF.**

———————————————————————————————————————————————————————————————————————

**R3(A,B,C,D,E)**
Functional dependencies: {CD -> BE, B -> A, B -> CD}

**1.Evaluation for BCNF:**

- Start with CD:<br>
  Closure of CD = {A, B, C, D, E}<br>
  This means that CD determines all attributes in R2. Thus, CD is a candidate key.<br>
- Start with B:<br>
  Closure of B = {A, B, C, D,E}<br>
  This means that B determines all attributes in R2. Thus, B is a candidate key.

**In conclusion, it is in BCNF.**

**2.Evaluation for 3NF:**
  Due to it is in BCNF, it must be in 3NF.

**In conclusion, it is in 3NF.**

———————————————————————————————————————————————————————————————————————

**R4(A,B,C,D,E)**
Functional dependencies: {CD -> ABE, B -> C}

**1.Evaluation for BCNF:**

- Start with CD:<br>
  Closure of CD = {A, B, C, D, E}<br>
  This means that CD determines all attributes in R2. Thus, CD is a candidate key.<br>
- Start with B:<br>
  Closure of B = {B, C}<br>
  This means that B does not determine all attributes in R2. Thus, B is not a candidate key.

**In conclusion, it is not in BCNF.**

**2.Evaluation for 3NF:**

- Start with CD $\rightarrow$ BE:<br>
  Due to CD is a candidate key, this functional dependency satisfies 3NF.<br>
- Start with B $\rightarrow$ CA:<br>
  Due to B is not a candidate key, we need to check if C is prime attribute.
  
  Closure of A = {A}<br>
  Closure of B = {B, C}<br>
  Closure of C = {C}<br>
  Closure of D = {D}<br>
  Closure of E = {E}
  
  Given the above, ABDE is also a candidate key. <br>
  prime attribute: A,B,C,D,E (due to CD and ABDE are both candidate keys)<br>
  So, C is prime attribute.
  
**In conclusion, it is in 3NF.**

#### Q7. Consider a relation R(A,B,C, D, E, F) and the following functional dependencies that hold on R: AB->C, C->D, F -> AD. Consider the following decompositions of R:

(a) R1(A,B,C,D), R2(A,B,E,F)<br>
(b) R1(E,F,C), R2(B,A,F,D)<br>
(c) R1(C,B,A), R2(D,E,F,A)

Which of the above decompositions are lossless-join? Your answer should be represented in the
following table (please, do not provide any explanation, each answer should be either “yes”, or “no”):

|      | Lossless-join decomposition? (yes/no) |
| :----: | :-------------------------------------: |
| a    | `yes`                                 |
| b    | `yes`                                 |
| c    | `no`                                  |

**Explaination:we can use the following criterion to check if a decomposition is lossless-join**

- Step 1: find R1 $\cap$ R2.
- Step 2: If the closure of R1 $\cap$ R2 includes all attributes of R1 or all attributes of R2, the decomposition is lossless-join.

**(a) R1(A,B,C,D), R2(A,B,E,F)**

- R1 $\cap$ R2 = A,B
- Closure of A,B is {A,B,C,D}, which includes all attributes of R1.

Thus, the answer is `yes`.

**(b) R1(E,F,C), R2(B,A,F,D)**

- R1 $\cap$ R2 = F
- Closure of F is {A,D,F}, which does not include all attributes of either R1 or R2.

Thus, the answer is `no`.

**(c) R1(C,B,A), R2(D,E,F,A)**

- R1 $\cap$ R2 = A
- Closure of A is {A}, which does not include all attributes of either R1 or R2.

Thus, the answer is `no`.

## B+ tree<a name="b+tree"></a>
**0. Reference**

(1)[Stanford B+ tree](https://web.stanford.edu/class/cs346/2015/notes/Blink.pdf)</br>
(2)[UCB B+ tree](https://cs186berkeley.net/notes/note4/#:~:text=The%20number%20d%20is%20the,each%20node%20must%20be%20sorted)</br>

**1. Terminology**

(1)

* `m` is **the maximum number of the children an internal node can contain**. It can be understood as **the number of pointers**.
* `d` is the order of the B+ tree, which means **the minimum number of the children any internal node(except root node) must contain**.
* `d` = $\lceil \frac{m}{2} \rceil$ - 1
* For root nodes, the number of keys ranges from 1 to `m` - 1, the number of children ranges from 2 to `m`
* For nodes except root nodes, the number of keys ranges from $\lceil \frac{m}{2} \rceil$ - 1 to `m` - 1, the number of children ranges from $\lceil \frac{m}{2} \rceil$ to `m`

(2)
* nodes are categorized as non-leaf nodes(internal nodes) and leaf nodes.

|      Term      |                   Details                    |     Property      |
| :------------: | :------------------------------------------: | :---------------: |
| non-leaf nodes | a root node and other nodes except root node |   have children   |
|   leaf nodes   |                                              | have not children |

**2. Properties of B+ tree (to check if the tree is B+ tree)**

* The internal nodes **should not have the duplicate** nodes
* The entries within each node must be **sorted**.
* The keys in the children to the left must be **less than** the entry
* The keys in the children to the right must be **greater than** or **equal to** the entry.

<img src='https://github.com/Hehua-Fan/LeetCode-SQL70-Notes/assets/95437839/bfeaca61-6608-475f-b725-8dbb17ddbaaa' width='300' height='120'>

### Questions<a name="b+_tree_questions"></a>

<img src='https://github.com/Hehua-Fan/LeetCode-SQL70-Notes/assets/95437839/1dc06ded-da1d-4a9b-b11e-18b52fd4d88e' width='650' height='500'>


For each of above B+-trees specify d and indicate if the tree has been built correctly. Your answer should be represented as the following table (please, do not provide any explanation):

|      | d  | Correct (yes/no) |
| :--: | :--: | :--------------: |
| (a)  |  `2`   |        `no`        |
| (b)  |  `3`   |       `yes`        |
| (c)  |  `2`   |        `no`        |
| (d)  |  `4`   |       `yes`        |
| (e)  |  `1`   |       `yes`        |
| (f)  |  `3`   |       `yes`        |

**Analytics**

* (a): `d` = $\lceil \frac{m}{2} \rceil$ - 1 = $\lceil \frac{6}{2} \rceil$ - 1 = 2. The entries within each node must be **sorted**.
* (b): `d` = $\lceil \frac{m}{2} \rceil$ - 1 = $\lceil \frac{8}{2} \rceil$ - 1 = 3.
* (c): `d` = $\lceil \frac{m}{2} \rceil$ - 1 = $\lceil \frac{5}{2} \rceil$ - 1 = 2. The keys in the children to the left must be **less than** the entry.
* (d): `d` = $\lceil \frac{m}{2} \rceil$ - 1 = $\lceil \frac{9}{2} \rceil$ - 1 = 4.
* (e): `d` = $\lceil \frac{m}{2} \rceil$ - 1 = $\lceil \frac{3}{2} \rceil$ - 1 = 1.
* (f): `d` = $\lceil \frac{m}{2} \rceil$ - 1 = $\lceil \frac{7}{2} \rceil$ - 1 = 3.


## Transaction<a name="transaction"></a>
* **Conflict-Serializable**: A schedule is conflict-serializable if the output is equivalent to a serial schedule. Conflicting operations are those that involve the same data item and where **at least one of the operations is a write**. https://www.javatpoint.com/dbms-conflict-serializable-schedule
* **Recoverable**: For any transaction $T_i$ and $T_j$, if $T_j$ loads the data item of $T_i$, then **$T_i$ needs to `commit` before $T_j$'s `commit`**.
* **Cascadless**: For any transaction $T_i$ and $T_j$, if $T_j$ loads the data item of $T_i$, then **$T_i$ needs to `commit` before $T_j$'s `read`**. If a schedule is cascadless, it is also recoverable.


### Questions<a name="transaction_questions"></a>
<img src='https://github.com/Hehua-Fan/LeetCode-SQL70-Notes/assets/95437839/4cbfa2e4-6996-4997-bae1-269a178bd609' width='350' height='280'>

Consider the following concurrent schedule S with transactions T1 and T2 (time is flowing from top to bottom):

|      | Your Answer (yes/no) |
| :--: | :--------------: |
|  Is the schedule S conflict-serializable?  |        `yes`        |
|  Is the schedule S recoverable?   |       `no`        |
|  Is the schedule S cascadeless?   |        `no`        |
