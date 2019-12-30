
This repository is the output of the https://angular.io/start tutorial which helps beginners to get used to angular concepts, very briefly. The notes below were gathered with the intention of summarizing the concepts mentioned in the tutorial for future refence and they were also taken from https://angular.io/start.

To start the app:
 
1. npm install
2. ng serve --open

# Quick Takeaways 

## Part I

### __*ngFor__
   
  *ngFor is a "structural directive". Structural directives shape or reshape the DOM's structure, typically by adding, removing, and manipulating the elements to which they are attached. Any directive with an asterisk, \*, is a structural directive.

  ``` html
  <div *ngFor="let product of products">
    ...
  </div>
  ```

### __*ngIf__
  A structural directive that conditionally includes a template based on the value of an expression coerced to Boolean. When the expression evaluates to true, Angular renders the template provided in a then clause, and when false or null, Angular renders the template provided in an optional else clause. The default template for the else clause is blank.
  ``` html
  <p *ngIf="product.description">
      Description: {{product.description}}
  </p>
  ```
  
### __Interpolation {{ }}__
   {{ }} is interpolation syntax. Interpolation renders a property's value as text.
  ``` html
  <p *ngIf="product.description">
      Description: {{product.description}}
  </p>
  ```

### __Property binding [ ]__

  ``` html
  <h3>    
    <a [title]="product.name + ' details'">
        {{product.name}}
    </a>
  </h3>
  ```

### __Event binding ( )__
  ``` html
  <button (click)="share()">
      Share
  </button>  
  ```


### src/app/product-list/product-list.component.html
``` html
<h2>Products</h2>

<div *ngFor="let product of products">
  <h3>    
    <a [title]="product.name + ' details'">
      {{product.name}}
    </a>
  </h3>

    <p *ngIf="product.description">
      Description: {{product.description}}
    </p>

    <button (click)="share()">
      Share
    </button>
</div>
```



### src/app/product-list/product-list.component.ts
``` typescript
import { Component } from '@angular/core';
import { products } from '../products';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css']
})
export class ProductListComponent {
  products = products;

  share() {
    window.alert('The product has been shared!');
  }
}
```
<p align="center">
<img src="https://angular.io/generated/images/guide/start/template-syntax-product-share-button.png" alt="Product list layout" title="Product list layout">
</p>



<p align="center">
<img src="https://angular.io/generated/images/guide/start/template-syntax-product-share-alert.png" alt="Share button clicked" title="Share button clicked">
</p>

## Part II
### __Components__
  Components define areas of responsibility in the user interface, or UI, that let you reuse sets of UI functionality. You've already built one with the product list component.

  A component consists of three things:

  * A component class that handles data and functionality. In the previous section, the product data and the share() method in the component class handle data and functionality, respectively.
  
  * An HTML template that determines the UI. In the previous section, the product list's HTML template displays the name, description, and a "Share" button for each product.
  
  * Component-specific styles that define the look and feel. Though product list does not define any styles, this is where component CSS resides.



<p align="center">
<img src="https://angular.io/generated/images/guide/start/app-components.png" alt="My Store's component structure" title="My Store's component structure">
</p>


_The next step is to create a new alert feature that takes a product as an input. The alert checks the product's price, and, if the price is greater than $700, displays a "Notify Me" button that lets users sign up for notifications when the product goes on sale._

### __Decorators__
  Notice the @Component() decorator. This indicates that the following class is a component. It provides metadata about the component, including its selector, templates, and styles.

### __Input__
  If we want to take an input from a parent component we can import/use _Input_ from _@angular/core_.
  ``` typescript
  import { Input } from '@angular/core';
  ```

  Having a variable with __@Input()__ decorator in front indicates that the property value passes in from the component's parent, the product list component.
  ``` typescript
  export class ProductAlertsComponent implements OnInit {
    @Input() product;
    constructor() { }

    ngOnInit() {
    }
  }
  ```

  And take the input by property binding on the parent component's html src/app/product-list/product-list.component.html 
  
  ``` html
  <button (click)="share()">
    Share
  </button>

  <app-product-alerts
    [product]="product">
  </app-product-alerts>

  ```

  The new product alert component takes a product as input from the product list. With that input, it shows or hides the "Notify Me" button, based on the price of the product. The Phone XL price is over $700, so the "Notify Me" button appears on that product.

<p align="center">
<img src="https://angular.io/generated/images/guide/start/product-alert-button.png" alt="Child component element" title="Child component element">
</p>

### __Output__
To make the "Notify Me" button work, you need to configure two things:

* the product alert component to emit an event when the user clicks "Notify Me" 
* the product list component to act on that event

``` typescript
import { Output, EventEmitter } from '@angular/core';
```

In the component class, define a property named notify with an @Output() decorator and an instance of EventEmitter(). This allows the product alert component to emit an event when the value of the notify property changes.

``` typescript
export class ProductAlertsComponent {
  @Input() product;
  @Output() notify = new EventEmitter();
}
```
In the product alert template, product-alerts.component.html, update the "Notify Me" button with an event binding to call the notify.emit() method.

``` html
<p *ngIf="product.price > 700">
  <button (click)="notify.emit()">Notify Me</button>
</p>
```

Next, define the behavior that should happen when the user clicks the button. Recall that it's the parent, product list component—not the product alerts component—that acts when the child raises the event. In product-list.component.ts, define an onNotify() method, similar to the share() method.

Finally, update the product list component to receive output from the product alerts component.

In product-list.component.html, bind the app-product-alerts component (which is what displays the "Notify Me" button) to the onNotify() method of the product list component.

``` html
<button (click)="share()">
  Share
</button>

<app-product-alerts
  [product]="product" 
  (notify)="onNotify()">
</app-product-alerts>

```
<p align="center">
<img src="https://angular.io/generated/images/guide/start/product-alert-notification.png" alt="Child component in action" title="Child component in action">
</p>

Congratulations! You've completed your first Angular app!

You have a basic online store catalog with a product list, "Share" button, and "Notify Me" button. You've learned about the foundation of Angular: components and template syntax. You've also learned how the component class and template interact, and how components communicate with each other.

### src/app/product-alerts/product-alerts.component.ts
``` typescript
import { Component, OnInit } from '@angular/core';
import {Input} from '@angular/core';
import {Output, EventEmitter} from '@angular/core';


@Component({
  selector: 'app-product-alerts',
  templateUrl: './product-alerts.component.html',
  styleUrls: ['./product-alerts.component.css']
})
export class ProductAlertsComponent implements OnInit {
  @Input() product;
  @Output() notify = new EventEmitter();
  
  constructor() { }

  ngOnInit() {
  }
}
```

### src/app/product-alerts/product-alerts.component.html
``` html
<p *ngIf="product.price > 700">
  <button (click)="notify.emit()">
    Notify Me!
  </button>
</p>
```

### src/app/product-list/product-list.component.html
``` typescript
import { Component } from '@angular/core';
import { products } from '../products';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html',
  styleUrls: ['./product-list.component.css']
})
export class ProductListComponent {
  products = products;

  share() {
    window.alert('The product has been shared!');
  }

  onNotify(){
    window.alert("You will be notified when the product goes on sale.");
  }
}
```

**More on Output and EventEmitter**
[https://ultimatecourses.com/blog/component-events-event-emitter-output-angular-2]