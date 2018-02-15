﻿ts-simple-ast
=============

[![npm version](https://badge.fury.io/js/ts-simple-ast.svg)](https://badge.fury.io/js/ts-simple-ast)
[![Build Status](https://travis-ci.org/dsherret/ts-simple-ast.svg?branch=master)](https://travis-ci.org/dsherret/ts-simple-ast)
[![Coverage Status](https://coveralls.io/repos/dsherret/ts-simple-ast/badge.svg?branch=master&service=github)](https://coveralls.io/github/dsherret/ts-simple-ast?branch=master)
[![stable](http://badges.github.io/stability-badges/dist/stable.svg)](http://github.com/badges/stability-badges)

[TypeScript](https://github.com/Microsoft/TypeScript) compiler wrapper. Provides a simple way to navigate and manipulate TypeScript and JavaScript code.

## Library Development - Progress Update (13 February 2018)

### Version 7

The TypeScript peer dependency has been dropped, but there should be no loss of functionality! If there is, please open an issue.

The TypeScript compiler object used by this library can now be accessed via the `ts` named export. Also non-node TypeScript compiler objects used in the public API of this  library are now exported directly as named exports:

```ts
import Ast, {ts, SyntaxKind, ScriptTarget} from "ts-simple-ast";
```

### General Progress

Most common code manipulation/generation use cases are implemented, but there's still a lot of work to do.

Please open an issue if find a feature missing or bug that isn't in the issue tracker.

### Report

View a generated report on what nodes have been wrapped in the [wrapped-nodes.md](wrapped-nodes.md) file.

## Documentation

Work in progress: https://dsherret.github.io/ts-simple-ast/

## Getting Started

1. [Installing](https://dsherret.github.io/ts-simple-ast/)
2. [Instantiating](https://dsherret.github.io/ts-simple-ast/setup/)
3. [Adding source files](https://dsherret.github.io/ts-simple-ast/setup/adding-source-files)
4. [Getting source files](https://dsherret.github.io/ts-simple-ast/navigation/getting-source-files)
5. [Navigating](https://dsherret.github.io/ts-simple-ast/navigation/example)
6. [Manipulating](https://dsherret.github.io/ts-simple-ast/manipulation/)

## Example

```ts
import Ast from "ts-simple-ast";

// add source files to ast
const ast = new Ast();
const sourceFile = ast.createSourceFile("MyClass.ts", "class MyClass {}\nlet myClass: MyClass;\nexport default MyClass;");
ast.addExistingSourceFiles("**/folder/**/*.ts");
ast.createSourceFile("misc.ts", {
    classes: [{
        name: "SomeClass",
        isExported: true
    }],
    enums: [{
        name: "SomeEnum",
        isExported: true,
        members: [{ name: "member" }]
    }]
});

// get information from ast
const classDeclaration = sourceFile.getClassOrThrow("MyClass");
classDeclaration.getName();          // returns: "MyClass"
classDeclaration.hasExportKeyword(); // returns: false
classDeclaration.isDefaultExport();  // returns: true

// manipulate ast
sourceFile.addEnum({ name: "MyEnum" });
classDeclaration.rename("NewName");
classDeclaration.addProperty({
    name: "myProp",
    initializer: "5"
});
classDeclaration.setIsDefaultExport(false);

// result
sourceFile.getFullText(); // returns: "class NewName {\n    myProp = 5;\n}\nlet myClass: MyClass;\n\nenum MyEnum {\n}\n"
sourceFile.save();        // save it asynchronously to MyFile.ts

// get underlying compiler node from the typescript AST from any node
const sourceFileCompilerNode = sourceFile.compilerNode;
```

Or navigate existing compiler nodes created with the TypeScript compiler:

```ts
import {createWrappedNode, ClassDeclaration, ts} from "ts-simple-ast";

// some code that creates a class declaration using the ts object
const classNode: ts.ClassDeclaration = ...; 

// create and use a wrapped node
const classDec = createWrappedNode(classNode) as ClassDeclaration;
const firstProperty = classDec.getProperties()[0];

// ... do more stuff here ...
```

## Resources

* [AST Viewers](https://dsherret.github.io/ts-simple-ast/setup/ast-viewers)
