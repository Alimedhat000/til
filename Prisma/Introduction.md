**Prisma** is a modern ORM (Object Relational Mapper) for web developers that simplifies database interactions with **type safety**, clean **object-oriented syntax**, and **auto-generated query** builders. It allows developers to model their database schema declaratively using the Prisma Schema Language, and automatically generates a fully-typed client for use in their application code.

### Getting Started

```bash
npx prisma init 
```

This will generate the **Prisma** folder which includes the default `schema.prisma` file. Here, you define:

- the **datasource**, which points to your database (usually set via the `DATABASE_URL` in the `.env` file),
    
- and the **generator**, which tells Prisma to generate the Prisma Client — a type-safe database client tailored to your schema.
    

You can then define your database models in the `schema.prisma` file using a clear and declarative syntax. For example:
```prisma 
model User {
  id       Int    @id @default(autoincrement())
  email    String @unique
  password String
  articles Articles[]
}

model Article {
  id       Int    @id @default(autoincrement())
  title    String 
  body     String
  author   User   @relation(fields: [authorID], references: [id])
  authorID Int
}
```

Once your schema is defined, run the following commands to migrate the database —(sync DB tables with the Prisma client) This will also create a migration in `prisma/migrations` folder— then generate the Prisma Client:

```bash
npx prisma migrate dev --name init
npx prisma generate
```


### Querying the Database 

With the Prisma Client generated, you can now interact with your database using JavaScript or TypeScript in an intuitive and type-safe way. Example:

```js
const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();

// Create a new user
const user = await prisma.user.create({
  data: {
    email: 'test@example.com',
    password: 'securepassword123',
  },
});

// Fetch all users
const users = await prisma.user.findMany();
```

Prisma abstracts away raw SQL and gives you a clean, consistent API to work with your data — all while catching potential type errors at development time.