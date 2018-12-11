# adal-angular6-example

Example project using adal-angular4 for authentication to Azure Active Directory tenent.

## Change Log
12-11-2018: Updated to Angular 7, Improved README

## Steps Used to Create this Example

1. Install the Latest Version of Angular CLI:

```
npm install -g @angular/cli@latest
```

2. Create the Project:

```
ng new adal-angular6-example --routing
```

3. Add Angular Material:

```
cd adal-angular6-example

ng add @angular/material
```

4. Add Material Module to Export Material Components:

```
ng g module Material
```

5. Add Components to MaterialModule:

```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { MatButtonModule, MatToolbarModule } from '@angular/material';

@NgModule({
  imports: [
    CommonModule,
    MatButtonModule,
    MatToolbarModule
  ],
  exports: [
    MatButtonModule,
    MatToolbarModule
  ],
  declarations: []
})
export class MaterialModule { }
```

6. Import MaterialModule into app.module.ts

```typescript
import {MaterialModule} from './material/material.module';
```

```typescript
  imports: [
   ...
   MaterialModule,
   ...
  ],
```

6. Create Navigation Components:

```
ng g component Home

ng g component NotFound

ng g component Toolbar
```

7. Set Up Routing in app-routing.module.ts:

```typescript
...
import { HomeComponent } from './home/home.component';
import { NotFoundComponent } from './not-found/not-found.component';
...
```

```typescript
...
const routes: Routes = [
  { path: '', component: HomeComponent},
  { path: '**', component: NotFoundComponent }
];
...
```

8. Install adal-angular4:

```
npm install --save adal-angular4
```

9. Import Adal Components into app.module.ts:

```typescript
...
import { AdalService, AdalGuard, AdalInterceptor } from 'adal-angular4';
...
providers: [
...
  AdalService,
  AdalGuard,
  { provide: HTTP_INTERCEPTORS, useClass: AdalInterceptor, multi: true }],
...
```

10. Set Up AdalGuard on HomeComponent Path in app-routing.module.ts:

```typescript
...
{ path: '', component: HomeComponent, canActivate: [AdalGuard] },
...
```

11. Set up ADAL configuration in environment.ts:

```typescript
export const environment = {
...
  config: {
    tenant: 'careportfol.io',
    clientId: '14c39115-7cae-4c02-b865-20d7b2d205f8',
    endpoints: {
      'https://graph.microsoft.com': '00000003-0000-0000-c000-000000000000'
    }
  }
...
};
```

12. Configure Adal in app.component.ts:

```typescript
...
import { AdalService } from 'adal-angular4';
import { environment } from '../environments/environment';
...
```

```typescript
...
export class AppComponent {

  constructor(private adalService: AdalService) {

    adalService.init(environment.config);
  }
...
```

13. Create Login in toolbar-component.ts:

```html
<mat-toolbar color="primary">
  <span>ADAL Example for Angular 6</span>

  <!-- This fills the remaining space of the current row -->
  <span class="fill-remaining-space"></span>

  <span *ngIf="!authenticated" (click)="login()">
    <button mat-raised-button>Login</button>
  </span>
  <span *ngIf="authenticated" (click)="logout()">
    <button mat-raised-button>Logout</button>
  </span>
</mat-toolbar>
```

```typescript
import { Component, OnInit } from '@angular/core';
import { AdalService } from 'adal-angular4';

@Component({
  selector: 'app-toolbar',
  templateUrl: './toolbar.component.html',
  styleUrls: ['./toolbar.component.css']
})
export class ToolbarComponent implements OnInit {

  constructor(private adalService: AdalService) { }

  ngOnInit() {

    this.adalService.handleWindowCallback();

    console.log(this.adalService.userInfo);
  }

  login() {
    this.adalService.login();
  }

  logout() {
    this.adalService.logOut();
  }

  get authenticated(): boolean {
    return this.adalService.userInfo.authenticated;
  }
}
```

14. Add ToolbarComponent to app.component.html:

```html
<app-toolbar></app-toolbar>
<router-outlet></router-outlet>
```

15. Done!