## Implement Rate Limiting Using Throttler

>#### visit- https://www.npmjs.com/package/@nestjs/throttler
```bash
npm i @nestjs/throttler
```
---

#### `app.module.ts`
```bash
import { Module } from '@nestjs/common';
import { AppController } from './app.controller';
import { AppService } from './app.service';
import { seconds, ThrottlerGuard, ThrottlerModule } from '@nestjs/throttler';
import { APP_GUARD } from '@nestjs/core';

@Module({
  imports: [ThrottlerModule.forRoot({
    throttlers: [
      {
        name: 'ShortTermThrottler',
        ttl: seconds(60),
        limit: 3,
      }
    ],
    errorMessage: 'Too many requests, please try again later.',
  }), ],
  controllers: [AppController],
  providers: [AppService, { provide: APP_GUARD, useClass: ThrottlerGuard }],
})
export class AppModule {}
```
---

#### `app.controller.ts`
```bash
import { Controller, Get } from '@nestjs/common';
import { AppService } from './app.service';
import { Throttle } from '@nestjs/throttler';

@Controller()
export class AppController {
  constructor(private readonly appService: AppService) {}

  @Get()
  @Throttle({
    ShortTermThrottler: {
      limit: 3, ttl: 60000,
    }
  })
  getHello(): string {
    //return this.appService.getHello();
    return 'This is a rate-limit route!'
  }
}
```
---


>#### 3bar er beshi request ba reload dile errormessage show korbe
>## OUTPUT
> <img width="426" height="182" alt="image" src="https://github.com/user-attachments/assets/79578822-aaca-4843-a3aa-f016092e38f4" />

> <img width="617" height="157" alt="image" src="https://github.com/user-attachments/assets/ff354784-7f54-4526-bf4a-77169b8b1fea" />
