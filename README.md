**asdl4cpp** is a port of CPython's [Abstract Syntax Definition Language](https://github.com/python/cpython/blob/master/Parser/asdl_c.py) to produce C++. [ASDL](https://www.cs.princeton.edu/research/techreps/TR-554-97) is a code generator for defining *abstract* syntax, much as [ANTLR](https://www.antlr.org) can define *concrete* syntax.

ASDL uses [algebraic data types](https://en.wikipedia.org/wiki/Algebraic_data_type). Consider this definition for arithmetic expressions:

```
module Arithmetic
{
  expr = BinOp(expr left, operator op, expr right)
       | Num(int n)
       
  operator = Add | Sub | Mul | Div
}
```

We can generate C++ files from the above:

```
asdl_cpp.py -h Arithmetic.h -c Arithmetic.cpp Arithmetic.asdl
```

We can use the generated functions to create an arithmetic expression:

```
//1+2*3
expr_t e = BinOp(
                 Num(1),
                 operator_t::kAdd,
                 BinOp(
                       Num(2),
                       operator_t::kMul,
                       Num(3)
                      )
                );
```

We can also define a *visitor* to accept an expression:

```
class EvalVisitor : public BaseVisitor {
 public:
 
  std::any visitBinOp(BinOp_t node) override {
    int left = std::any_cast<int>(visit(node->left));
    int right = std::any_cast<int>(visit(node->right));
    switch (node->op) {
        case operator_t::kAdd: return left + right;
        case operator_t::kSub: return left - right;
        case operator_t::kMul: return left * right;
        case operator_t::kDiv: return left / right;
    }
  }
  
  std::any visitNum(Num_t node) override {
    return node->n;
  }

  std::any visitOperator(operator_t value) override {
    // not reached
    return 0;
  }
};
```

So now we can evaluate our previously created expression:

```
EvalVisitor eval_visitor;
int result = std::any_cast<int>(eval_visitor.visit(e));
```

----

This repo includes CMake files for building the example.

```
$ mkdir build && cd build
$ cmake ..
$ make
```
