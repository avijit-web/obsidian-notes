---
tags:
  - typescript
  - red
cssclasses:
  - interface-red
---
## Interfaces are like types for objects
```
interface Transactions{
   payerAccountNumber:number;
   payeeAccountNumber:number;
}

const BankAccount = {
  accountNumber:number;
  accountHolder:number;
  balance:number;
  isActive:boolean;
  transactions:Transactions[]
}

const transactionExample1 :Transactions = {
   payerAccountNumber:123,
   payeeAccountNumber:456
}

const transactionExample2 :Transactions = {
   payerAccountNumber:456,
   payeeAccountNumber:789
}

const bankAccount : BankAccount  = {
  accountNumber : 123,
  accountHolder: 'John Doe',
  balance:4000,
  isActive:true,
  transactions:[transactionExample1,transactionExample2]
   
}
```

## Extends example

```
interface Book {
  name:string;
  price:number;
}

interface EBook extends Book {
 
  fileSize:number;
  format:string;
}

interface AudioBook extends Book {
  duration:number;
}

const book:EBook = {
 name:'Atomic habits',
 price:1200,
 fileSize:300,
 format:'pdf',
 duration:4
}

```

## Merge interfaces

```
interface Book {
  name:string;
  price:number;
}

interface Book {
  size:number;
}

const book : Book = {
 name:'Atomic habits',
 price:200,
 size:45
}

 
```

==Use `type` when you need to create a union, intersection, or mapped type. Use `interface` when defining the shape of an object or class structure. ==

==`interface` is extendable and better for OOP-style hierarchies; `type` is more flexible for complex type combinations.==


