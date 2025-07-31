# newNgAppSteps

Here's how you can **create an Angular application** that uses **standalone components**, **environment configurations**, **a reactive form**, and a **service to hit an API**.

---

### ✅ 1. Create a New Angular App

```bash
ng new my-angular-app --standalone --routing --style=scss
cd my-angular-app
```

---

### ✅ 2. Set Up Environment Files

Edit `src/environments/environment.ts`:

```ts
export const environment = {
  production: false,
  apiUrl: 'https://dev.example.com/api'
};
```

Edit `src/environments/environment.prod.ts`:

```ts
export const environment = {
  production: true,
  apiUrl: 'https://prod.example.com/api'
};
```

In `angular.json`, these files are already mapped under `"fileReplacements"`.

```
          "configurations": {
            "production": {
              "fileReplacements": [
                {
                  "replace": "src/environments/environment.ts",
                  "with": "src/environments/environment.prod.ts"
                }
              ],
              "budgets": [
                {
                  "type": "initial",
                  "maximumWarning": "500kB",
                  "maximumError": "1MB"
                },
                {
                  "type": "anyComponentStyle",
                  "maximumWarning": "4kB",
                  "maximumError": "8kB"
                }
              ],
              "outputHashing": "all"
            },
            "development": {
              "optimization": false,
              "extractLicenses": false,
              "sourceMap": true
            }
          },
```

---


### ✅ 3. Generate a Standalone Component

```bash
ng generate component components/MomoTipComponent --standalone
```

This will create the component in `src/app/components/momo-component`.

---

### ✅ 4. Create a Reactive Form

Edit `form-component.component.ts`:

```ts
import { Component, inject } from '@angular/core';
import { FormBuilder, FormGroup, ReactiveFormsModule, Validators } from '@angular/forms';
import { CommonModule } from '@angular/common';
import { ApiService } from '../../services/api.service';

@Component({
  selector: 'app-form-component',
  standalone: true,
  imports: [CommonModule, ReactiveFormsModule],
  templateUrl: './form-component.component.html'
})
export class FormComponent {
  private fb = inject(FormBuilder);
  private apiService = inject(ApiService);

  form: FormGroup = this.fb.group({
    phoneNumber: ['', [Validators.required]],
    amount: ['', [Validators.required, Validators.min(1)]]
  });

  submit() {
    if (this.form.valid) {
      this.apiService.sendForm(this.form.value).subscribe({
        next: response => console.log('API Response:', response),
        error: err => console.error('API Error:', err)
      });
    }
  }
}
```

Edit `form-component.component.html`:

```html
<form [formGroup]="form" (ngSubmit)="submit()">
  <label>
    Phone Number:
    <input type="text" formControlName="phoneNumber" />
  </label>

  <label>
    Amount:
    <input type="number" formControlName="amount" />
  </label>

  <button type="submit" [disabled]="form.invalid">Submit</button>
</form>
```

---

### Model

```
export interface MomoTip {
  phoneNumber: string;
  amount: number;
}

```

### ✅ 5. Create the API Service

```bash
ng generate service services/Api
```

Edit `src/app/services/api.service.ts`:

```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { environment } from '../../environments/environment';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class ApiService {
  constructor(private http: HttpClient) {}

  sendForm(data: { phoneNumber: string; amount: number }): Observable<any> {
    return this.http.post(`${environment.apiUrl}/submit`, data);
  }
}
```

---

### ✅ 6. Update `main.ts` or Root Component to Include the Form Component

In `src/app.config.ts`:

```ts
import {
  ApplicationConfig,
  importProvidersFrom,
  provideZoneChangeDetection,
} from '@angular/core';
import { provideRouter } from '@angular/router';

import { routes } from './app.routes';
import { provideHttpClient } from '@angular/common/http';

export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(),
  ],
};
```

---

You now have:

* ✅ A standalone Angular app
* ✅ Standalone component with a reactive form
* ✅ Environment files for dev/prod
* ✅ A service using environment config
* ✅ API integration via `HttpClient`

Let me know if you want to extend this with routing or build optimizations.
