## Implement Rate Limiting Using Throttler

>#### visit- https://www.npmjs.com/package/@nestjs/throttler
```bash
npm i @nestjs/throttler
```
---

>#### app.module.ts e add koro- ThrottlerModule.forRoot({throttlers: [{name:'ShortTermThrottler',ttl: seconds(60),limit: 3,}],errorMessage: 'Too many requests, please try again later.',}) & providers: [AppService,{ provide: APP_GUARD, useClass: ThrottlerGuard}],

#### `app.module.ts`
```bash
import { MiddlewareConsumer, Module, NestModule } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { UserController } from './user/user.controller';
import { ProductService } from './product/product.service';
import { ProductController } from './product/product.controller';
import { MynameController } from './myname/myname.controller';
import { UserRolesController } from './user-roles/user-roles.controller';
import { ExceptionController } from './exception/exception.controller';
import { LoggerMiddleware } from './middleware/logger/logger.middleware';
import { DatabaseService } from './database/database.service';
import { DatabaseController } from './database/database.controller';
import { ConfigModule } from '@nestjs/config';
import { UserBdModule } from './user-bd/user-bd.module';
import { TypeOrmModule } from '@nestjs/typeorm';
import { EmployeeBdModule } from './employee-bd/employee-bd.module';
import { AuthModule } from './auth/auth.module';
import { MongooseModule } from '@nestjs/mongoose';
import { GraphQLModule } from '@nestjs/graphql';
import { ApolloDriver, ApolloDriverConfig } from '@nestjs/apollo';
import { join } from 'path';
import { PrismaModule } from './prisma/prisma.module';
import { BooksModule } from './books/books.module';
import { seconds, ThrottlerGuard, ThrottlerModule } from '@nestjs/throttler';
import { APP_GUARD } from '@nestjs/core';


@Module({
  imports: [ ConfigModule.forRoot({isGlobal: true,}), GraphQLModule.forRoot<ApolloDriverConfig>({
    driver: ApolloDriver,
    autoSchemaFile: join(process.cwd(), 'src/schema.gql'),
    sortSchema: true,
    playground: true,
  }), TypeOrmModule.forRoot({
    type: 'postgres',
    url: process.env.DATABASE_URL,
    autoLoadEntities: true,
    synchronize: true,
  }), ThrottlerModule.forRoot({
    throttlers: [
      {
      name: 'ShortTermThrottler',
      ttl: seconds(60),
      limit: 3,
      }
    ],
    errorMessage: 'Too many requests, please try again later.',
  }), MongooseModule.forRoot(process.env.MONGO_URL!), UserBdModule, EmployeeBdModule, AuthModule, PrismaModule, BooksModule],
  controllers: [AppController, UserController, ProductController, MynameController, UserRolesController, ExceptionController, DatabaseController ],
  providers: [AppService, ProductService, DatabaseService, { provide: APP_GUARD, useClass: ThrottlerGuard}],
})
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer){
    consumer.apply(LoggerMiddleware).forRoutes('*');
  } 
}
```
---
