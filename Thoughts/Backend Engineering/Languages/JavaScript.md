# JavaScript (Node.js)

> *JavaScript on the server*

---

## Why Node.js?

- **Same language** - Frontend and backend
- **Event-driven** - Great for real-time
- **NPM ecosystem** - Largest package registry
- **Fast development** - Quick prototyping

---

## Popular Frameworks

| Framework | Type | Best For |
|-----------|------|----------|
| **Express** | Minimal | APIs, learning |
| **NestJS** | Opinionated | Enterprise, TypeScript |
| **Fastify** | Performance | High-throughput APIs |

---

## Express Example

```javascript
const express = require('express');
const app = express();
app.use(express.json());

let users = [];

app.get('/users', (req, res) => {
  res.json(users);
});

app.get('/users/:id', (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));
  if (!user) return res.status(404).json({ error: 'Not found' });
  res.json(user);
});

app.post('/users', (req, res) => {
  const user = {
    id: users.length + 1,
    ...req.body
  };
  users.push(user);
  res.status(201).json(user);
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

## NestJS Example

```typescript
// users.controller.ts
import { Controller, Get, Post, Body, Param } from '@nestjs/common';
import { UsersService } from './users.service';
import { CreateUserDto } from './dto/create-user.dto';

@Controller('users')
export class UsersController {
  constructor(private readonly usersService: UsersService) {}

  @Get()
  findAll() {
    return this.usersService.findAll();
  }

  @Get(':id')
  findOne(@Param('id') id: string) {
    return this.usersService.findOne(+id);
  }

  @Post()
  create(@Body() createUserDto: CreateUserDto) {
    return this.usersService.create(createUserDto);
  }
}
```

---

## Async/Await

```javascript
// Async database operations
async function getUser(id) {
  try {
    const user = await User.findById(id);
    const orders = await Order.find({ userId: id });
    return { user, orders };
  } catch (error) {
    throw new Error('Failed to fetch user');
  }
}
```

---

## Essential Packages

| Package | Purpose |
|---------|---------|
| Express | Web framework |
| Prisma | ORM |
| Jest | Testing |
| Bull | Job queues |
| Socket.io | Real-time |

---

*Back to: [[Index|Languages Home]]*
