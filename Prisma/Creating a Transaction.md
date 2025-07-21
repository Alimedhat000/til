To perform multiple operations as a single atomic transaction using Prisma, you can use the `prisma.$transaction()` function. This ensures that either **all operations succeed**, or **none are applied** if any fail.


```js
await prisma.$transaction([
	prisma.collectionProduct.deleteMany(),
	prisma.variant.deleteMany(),
	prisma.image.deleteMany(),
	prisma.productOption.deleteMany(),
	prisma.product.deleteMany(),
	prisma.collection.deleteMany(),
]);
```

In this example, all delete operations are executed within a single transaction. If any one of them fails, **the entire transaction is rolled back**, preserving database integrity.