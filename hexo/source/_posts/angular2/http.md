---
title: angular6 Http请求拦截器
date: 2020-10-24 
---

## 开始

> 前端往往会对请求多一些统一处理,或者加一些token验证功能,这时候就需要使用到拦截器了

<!-- more -->

## 实现

> 定义一个新的class并实现HttpInterceptor
```ts
import {HttpEvent, HttpHandler, HttpInterceptor, HttpRequest, HttpResponse} from '@angular/common/http';
import {Observable} from 'rxjs';
import {tap} from 'rxjs/operators';
import {Router} from '@angular/router';
 
@Injectable()
export class HttpRequestInterceptor implements HttpInterceptor {
  constructor(private router: Router) {}
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    if (req.url.endsWith('login')) {
      return next.handle(req).pipe(tap(event => {
        if (event instanceof HttpResponse) {
        }
      }, e => {
        if (e.status === 401) {
          alert('用户名或密码错误');
        }
      }));
    }
 
    const modifiedReq = req.clone({
      // 在header中添加token
      setHeaders: {Authorization: 'Bearer ' + localStorage.getItem('access_token')}
    });
    const _this = this;
    return next.handle(modifiedReq).pipe(tap(event => {
      if (event instanceof HttpResponse) {
        console.log(event);
      }
    }, e => {
      console.log(e);
      if (e.status === 401) {
        _this.router.navigateByUrl('/login');
      }
    }));
  }
 
}
```

> 最后,将这个服务添加到modules
    
```ts
{provide: HTTP_INTERCEPTORS, useClass: HttpRequestInterceptor, multi: true}
```