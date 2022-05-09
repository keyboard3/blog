---
title: 函数组件中定义函数组件弊端
top: false
cover: false
toc: true
mathjax: true
date: 2021-10-29 14:47:26
password:
summary:
tags: [react,ecmascript,function]
categories:
---
嵌套函数定义在执行的时候，每次生成的是个新函数对象。这个告诉我们，不要在函数组件里定义子函数组件，函数执行的时候子函数组件会被先卸载再挂载的。

## 示例
```js
const funs1=[],funs2=[];
function funParent() {
    funChil1();
    funChil2();
    function funChil1() {
      funs1.push(funChil1);
    }
}
function funChil2() {
  funs2.push(funChil2);
}
funParent();
funParent();
console.log("fun1:",funs1[0]===funs1[1]);
console.log("fun2:",funs2[0]===funs2[1]);
```
## ECMAScript 标准文档查找过程
- [Function Object [[Call]]](https://262.ecma-international.org/12.0/#sec-ecmascript-function-objects-call-thisargument-argumentslist)
  - [PrepareForOrdinaryCall](https://262.ecma-international.org/12.0/#sec-prepareforordinarycall)
    - [NewFunctionEnvironment](https://262.ecma-international.org/12.0/#sec-newfunctionenvironment)
      - [Function Environment Records](https://262.ecma-international.org/12.0/#sec-function-environment-records)
  - [OrdinaryCallEvaluateBody](https://262.ecma-international.org/12.0/#sec-ordinarycallevaluatebody)
    - [Runtime Semantics: EvaluateBody](https://262.ecma-international.org/12.0/#sec-runtime-semantics-evaluatebody)
    - [Runtime Semantics: EvaluateFunctionBody](https://262.ecma-international.org/12.0/#sec-functiondeclarationinstantiation)
      - [FunctionDeclarationInstantiation](https://262.ecma-international.org/12.0/#sec-functiondeclarationinstantiation)
        - b.  Let fo be [InstantiateFunctionObject](https://262.ecma-international.org/12.0/#sec-runtime-semantics-instantiatefunctionobject) of f with argument lexEnv.
          - 2. Let F be [OrdinaryFunctionCreate](https://262.ecma-international.org/12.0/#sec-ordinaryfunctioncreate)([%Function.prototype%](https://262.ecma-international.org/12.0/#sec-properties-of-the-function-prototype-object), sourceText, [FormalParameters](https://262.ecma-international.org/12.0/#prod-FormalParameters), [FunctionBody](https://262.ecma-international.org/12.0/#prod-FunctionBody), non-lexical-this, scope)
            - Let F be ! [OrdinaryObjectCreate](https://262.ecma-international.org/12.0/#sec-ordinaryobjectcreate)(functionPrototype, internalSlotsList).