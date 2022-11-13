---
title: "The Logical Operators In Lambda Calculus"
date: 2022-10-21T23:30:55+03:00
tags:
  - logical-operators
  - lambda-calculus
---

##### Basic notions
Lambda Calculus is a formal system introduced by Alonzo Church to study function abstraction and application.
Church originally created this system in order to solve the Entscheidungsproblem. Its history and development is worth researching, this post however will only show how to understand basic lambda expressions and how to define a few logical operators inside this system.

Lambda Calculus gets its name from the Greek letter lambda λ which inside the system is called the *abstraction operator* and is used to bind variables in expressions.

A lambda function is an anonymous function and can be described in BNF notation as:
$$\langle function \rangle ::= \lambda \langle name \rangle . \langle expression \rangle$$
Where 'name' denotes a bound variable (which may be any sequence of non-blank characters and can be thought of as the function's input), the dot '.' separates the bound variable from the expression (and from a programmer's viewpoint it can be read as "return") and 'expression' can be thought of as the function's body and may be a name, a function or a function application.

<span style="font-size:14px">*Example: The identity function, simply return the input.*</span>  
$$\lambda x.x$$

A function application is simply two lambda expressions wrapped in parentheses (this is one of many different notations). The first expression being a function expression and the second an argument expression. The application specializes the abstraction by providing a value for the bound variable. The function is said to be applied to the argument.

<span style="font-size:14px">*Example: Apply the identity function to the argument expression a. The bound variable "x" gets substituted with the value of "a" inside the function body. The abstraction operator (λx) at the left of the function body and the argument expression a at the right of the function body disappear. This expression simply evaluates to a.*</span>
$$(\lambda x.x\hspace{12mu}a) \to a$$


In order to evaluate a function application we have to replace all occurrences of the bound variable inside the body by **either** the value of the argument expression **or** the unevaluated argument expression. The first approach is called **applicative order** reduction and the second **normal order** reduction. In this post I'll be using normal order to evaluate reductions.

##### Encoding the logical operators
The main entities of lambda calculus are functions, so this is what we have to encode the boolean values into. The way this has been achieved is by representing both True and False as lambda functions that accept two arguments. We should note here that the abstraction operator λ typically binds one variable at a time so a function of two variables would be notated $ \lambda a. \lambda b. \langle expression \rangle $, that is a function that takes one argument and returns a function that takes one argument and returns an expression.
  - **True**  returns the first  argument $$\lambda a. \lambda b.a$$
  - **False** returns the second argument $$\lambda a. \lambda b.b$$

Let's apply the identity function to the True function
$$(\lambda x.x \hspace{12mu} \lambda a. \lambda b.a) \to \lambda a. \lambda b.a$$
We may allow ourselves to use the abbreviation "True" instead of the whole lambda expression encoding it.
$$(\lambda x.x \hspace{12mu} True) \to True$$

Now, the *True* and *False* functions (being functions) can also be applied to arguments, and evidently they can be used to encode the *Not* operator.
Let's first see *True* being used as a function:
$$ ((True \hspace{6mu} foo)\hspace{6mu} bar) \to foo $$

Let's expand the leftmost True in order to see the substitution process.
$$ ((\lambda a.\lambda b. a \hspace{12mu} foo)\hspace{6mu} bar) \to $$
$$ (\lambda b. foo \hspace{12mu} bar) \to $$
$$ foo $$

<!-- TODO: WALK THROUGH THE PROCESS -->

*Not* will be defined as a function expecting one boolean argument e.g. $\lambda x$ and returning the opposite boolean value. We saw that the boolean values are encoded as functions expecting two (arbitrary) arguments, and we want to achieve:
<!-- |X    |Not X| -->
<!-- |:----|:----| -->
<!-- |True |False| -->
<!-- |False|True | -->

<table style="margin-left: auto; margin-right: auto;">
  <tr><th>X</th> <th>Not X</th>
  <tr><td>True</td> <td>False</td>
  <tr><td>False</td> <td>True</td>
</table>

So this x bound variable will have to become either *True* or *False*. And since *True* and *False* are functions expecting two arguments, one of which will be returned, we will have to provide two boolean arguments (to *Not*'s input) in some order that achieves *Not*'s truth table.  
Let's define *Not* as $$\lambda x.((x \hspace{6mu} False) \hspace{6mu} True)$$ and try to apply it to *True*:
$$(\lambda x.((x \hspace{6mu} False) \hspace{6mu} True) \hspace{6mu} True) \to$$
$$((True \hspace{6mu} False) \hspace{6mu} True)\to$$
$$ False $$

From here on I will be less explanatory and briefly define the *And*, *Or*, *Implies* and *Equivalent* operators in abbreviated boolean lambda terms. I will leave the expansion as a pastime suggestion to the reader. One could also consider defining *Xor*, *Nand* etc.

$$ And = \lambda a. \lambda b.(a \hspace{6mu} b \hspace{6mu} False) $$
$$ Or = \lambda a. \lambda b.(a \hspace{6mu} True \hspace{6mu} b) $$
$$ Implies = \lambda a. \lambda b.(a \hspace{6mu} b \hspace{6mu} True) $$
$$ Equiv = \lambda a. \lambda b.(a \hspace{6mu} b \hspace{6mu} ( Not \hspace{6mu} b)) $$


---
##### Paralipomena
A variable in a lambda expression is said to be "free" if it is not “bound” by a λ. An expression is closed if it has no free variables, otherwise it is open.  
Formally, the replacement of a bound variable with an argument in a function body is called **beta reduction** (β-reduction).  
When an expression can be reduced by applying a function to an argument it is called a *redex*. When an expression contains no redexes it is said to be in *normal form*.
- Normal-order reduction: Choose the left-most redex first.
- Applicative-order reduction: Choose the right-most redex first.

Other google-worthy notions: α-conversion, η-reduction

<!-- --- -->
<!-- **Implies** and **Equivalent** truth tables -->
<!-- <table style="display: inline-block; margin-left: auto; margin-right: auto;"> -->
<!--   <tr><th>X</th> <th>X</th> <th>X Implies Y</th> -->
<!--   <tr><td>False</td> <td>False</td> <td>True</td> -->
<!--   <tr><td>False</td> <td>True</td> <td>True</td> -->
<!--   <tr><td>True</td> <td>False</td> <td>False</td> -->
<!--   <tr><td>True</td> <td>True</td> <td>True</td> -->
<!-- </table> -->

<!-- <table style="display: inline-block; margin-left: auto; margin-right: auto;"> -->
<!--   <tr><th>X</th> <th>X</th> <th>X Equiv Y</th> -->
<!--   <tr><td>False</td> <td>False</td> <td>True</td> -->
<!--   <tr><td>False</td> <td>True</td> <td>False</td> -->
<!--   <tr><td>True</td> <td>False</td> <td>False</td> -->
<!--   <tr><td>True</td> <td>True</td> <td>True</td> -->
<!-- </table> -->

---
##### Resources
https://www.cs.rochester.edu/~brown/173/readings/LCBook.pdf  
https://www.cs.yale.edu/homes/hudak/CS201S08/lambda.pdf  
https://www.cs.cornell.edu/courses/cs312/2008sp/recitations/rec26.html  
https://plato.stanford.edu/entries/lambda-calculus/  


<!-- Let's see for this case the whole reduction in lambda terms. After this we will only use the abbreviations for simplicity and readability. -->

<!-- $$ ((\lambda a. \lambda b.a \hspace{14mu} \lambda i. \lambda j.i)\hspace{14mu} \lambda x. \lambda y.y) \to $$ -->
<!-- $$ ( \lambda b. \lambda i. \lambda j.i \hspace{14mu} \lambda x. \lambda y.y) \to $$ -->
<!-- $$ \lambda i. \lambda j.i $$ -->
<!-- Bound variable a will be substituted by $ \lambda i. \lambda j.i $ in the function body a of the left inner parenthesis. Bound variable b does not appear in the body so it will just be ignored. -->
<!-- $$ \to \lambda ab.a $$ -->
