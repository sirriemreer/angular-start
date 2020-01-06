
This repository is the output of the https://angular.io/start tutorial which helps beginners to get used to angular concepts, very briefly. The notes below were gathered with the intention of summarizing the concepts mentioned in the tutorial for future refence and they were completely taken from https://angular.io/start tutorial.

To start the app:
 
1. npm install
2. ng serve --open

# Notes

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


#### src/app/product-list/product-list.component.html
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



#### src/app/product-list/product-list.component.ts
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

#### src/app/product-alerts/product-alerts.component.ts
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

#### src/app/product-alerts/product-alerts.component.html
``` html
<p *ngIf="product.price > 700">
  <button (click)="notify.emit()">
    Notify Me!
  </button>
</p>
```

#### src/app/product-list/product-list.component.html
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


## Part III

### Routing
The Angular router enables you to show different components and data to the user based on where the user is in the application. The router enables navigation from one view to the next as users perform application tasks:

* Enter a URL in the address bar, and the browser navigates to a corresponding page.
* Click links on the page, and the browser navigates to a new page.
* Click the browser's back and forward buttons, and the browser navigates backward and forward through the history of pages you've seen.

Lets register a route amd define its RouterLink directive. The app is already set up to use the Angular router and to use routing to navigate to the product list component you modified earlier. Let's define a route to show individual product details.

#### src/app/app.module.ts
``` typescript
@NgModule({
  imports: [
    BrowserModule,
    ReactiveFormsModule,
    RouterModule.forRoot([
      { path: '', component: ProductListComponent },
      { path: 'products/:productId', component: ProductDetailsComponent },
    ])
  ],
```
Define a link using the RouterLink directive. The routerLink defines how the user navigates to the route (or URL) declaratively in the component template.

We want the user to click a product name to display the details for that product.

#### src/app/product-list/product-list.component.html
``` html
<div *ngFor="let product of products; index as productId">

  <h3>
    <a [title]="product.name + ' details'" [routerLink]="['/products', productId]">
      {{ product.name }}
    </a>
  </h3>
<!-- . . . -->
</div>
```

Test the router by clicking a product name. The app displays the product details component, which currently always says "product-details works!" (We'll fix this in the next section.)

Notice that the URL in the preview window changes. The final segment is products/1.

<p align="center">
<img src="https://angular.io/generated/images/guide/start/product-details-works.png" alt="Routing works!" title="Routing works!">
</p>

### Using route information

The product details component handles the display of each product. The Angular Router displays components based on the browser's URL and your defined routes. You'll use the Angular Router to combine the products data and route information to display the specific details for each product.

#### src/app/product-details/product-details.component.ts

``` typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

import { products } from '../products';
```

``` typescript
export class ProductDetailsComponent implements OnInit {
  product;

  constructor(
    private route: ActivatedRoute,
  ) { }

}
```

__The ActivatedRoute is specific to each routed component loaded by the Angular Router. It contains information about the route, its parameters, and additional data associated with the route.__

In the ngOnInit() method, subscribe to route params and fetch the product based on the productId.Angular calls ngOnInit() shortly after creating a component.

The route parameters correspond to the path variables defined in the route. The productId is provided from the URL that was matched to the route. You use the productId to display the details for each unique product.

``` typescript
ngOnInit() {
  this.route.paramMap.subscribe(params => {
    this.product = products[+params.get('productId')];
  });
}
```

Update the template to display product details information inside an *ngIf.

``` html
<h2>Product Details</h2>

<div *ngIf="product">
  <h3>{{ product.name }}</h3>
  <h4>{{ product.price | currency }}</h4>
  <p>{{ product.description }}</p>

</div>
```

<p align="center">
<img src="https://angular.io/generated/images/guide/start/product-details-routed.png" alt="Using the data from the route" title="Using the data from the route">
</p>

Now, when the user clicks on a name in the product list, the router navigates you to the distinct URL for the product, swaps out the product list component for the product details component, and displays the product details.

So as of now, the products are linked from the product list page to individual products and users can click on a product name from the list to see details in a new view, with a distinct URL (route).

### Part IV

## Managing Data
At the end of Routing, the online store application has a product catalog with two views: a product list and product details. Users can click on a product name from the list to see details in a new view, with a distinct URL (route).

In this section, you'll create the shopping cart. You'll:
* Update the product details page to include a "Buy" button, which adds the current product to a list of products managed by a cart service.
* Add a cart component, which displays the items you added to your cart.
* Add a shipping component, which retrieves shipping prices for the items in the cart by using Angular's HttpClient to retrieve shipping data from a .json file.

## Services
Services are an integral part of Angular applications. In Angular, a service is an instance of a class that can be made available to any part of your application using Angular's dependency injection system.

Services are the place where you share data between parts of your application. For the online store, the cart service is where you store your cart data and methods.


### Create the shopping cart service
Up to this point, users can view product information, and simulate sharing and being notified about product changes. They cannot, however, buy products.

In this section, you'll add a "Buy" button to the product details page. You'll also set up a cart service to store information about products in the cart.

Generate a cart service. In the CartService class, define an items property to store the list (array) of the current products in the cart. Define methods to add items to the cart, return cart items, and clear the cart items:

#### src/app/cart.service.ts
``` typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class CartService {

  items = [];

  addToCart(product) {
    this.items.push(product);
  }

  getItems() {
    return this.items;
  }

  clearCart() {
    this.items = [];
    return this.items;
  }
  constructor() {}

}
```
### Use the cart service
Update the product details component to use the cart service. You'll add a "Buy" button to the product details view. When the "Buy" button is clicked, you'll use the cart service to add the current product to the cart.

Import and inhect the cart service in prodcut-details component.

#### src/app/product-details/product-details.component.ts
``` typescript
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

import { products } from '../products';
import { CartService } from '../cart.service';
```

``` typescript
export class ProductDetailsComponent implements OnInit {
  constructor(
    private route: ActivatedRoute,
    private cartService: CartService
  ) { }
}
```

Define the addToCart() method, which adds the current product to the cart.

The addToCart() method:

* Receives the current product
* Uses the cart service's #addToCart() method to add the product to the cart
* Displays a message that the product has been added to the cart

``` typescript
export class ProductDetailsComponent implements OnInit {
  addToCart(product) {
    window.alert('Your product has been added to the cart!');
    this.cartService.addToCart(product);
  }
}
```

Update the product details template to have a "Buy" button that adds the current product to the cart.

#### src/app/product-details/product-details.component.html
``` html
<h2>Product Details</h2>

<div *ngIf="product">
  <h3>{{ product.name }}</h3>
  <h4>{{ product.price | currency }}</h4>
  <p>{{ product.description }}</p>

  <button (click)="addToCart(product)">Buy</button>
</div>
```

<p align="center">
<img src="https://angular.io/generated/images/guide/start/product-details-buy.png" alt="Buying is enabled" title="Buying is enabled">
</p>

<p align="center">
<img src="https://angular.io/generated/images/guide/start/buy-alert.png" alt="Click buy" title="Click buy">
</p>

### Create the cart page
At this point, users can put items in the cart by clicking "Buy", but they can't yet see their cart.

We'll create the cart page in two steps:

* Create a cart component and set up routing to the new component. At this point, the cart page will only have default text.
* Display the cart items.

### Set up the component:
To create the cart page, you begin by following the same steps you did to create the product details component and to set up routing for the new component.

Generate a cart component, named cart.

#### src/app/cart/cart.component.ts
``` typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-cart',
  templateUrl: './cart.component.html',
  styleUrls: ['./cart.component.css']
})
export class CartComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

}
```

Add routing (a URL pattern) for the cart component.

Reminder: Open app.module.ts and add a route for the component CartComponent, with a path of cart:

#### src/app/app.module.ts
``` typescript
@NgModule({
  imports: [
    BrowserModule,
    ReactiveFormsModule,
    RouterModule.forRoot([
      { path: '', component: ProductListComponent },
      { path: 'products/:productId', component: ProductDetailsComponent },
      { path: 'cart', component: CartComponent },
    ])
  ],
```

### Display the cart items
Services can be used to share data across components:

* The product details component already uses the cart service (CartService) to add products to the cart.
* In this section, you'll update the cart component to use the cart service to display the products in the cart.

1. Open cart.component.ts.

2. Set up the component to be able to use the cart service. (This is the same way you set up the product details component to use the cart service, above.)
  * Import the CartService from the cart.service.ts file.

   #### src/app/cart/cart.component.ts

   ``` typescript
    import { Component } from '@angular/core';
    import { CartService } from '../cart.service';
    ```
  * Inject the CartService to manage cart information.

   ``` typescript
    export class CartComponent {

    constructor(
        private cartService: CartService
      ) { }
    }
    ```
3. Define the items property to store the products in the cart.
 
 ``` typescript
 export class CartComponent {
  items;

  constructor(
    private cartService: CartService
  ) { }
}
 ```

4. Set the items using the cart service's getItems() method. (You defined this method when you generated cart.service.ts.)

The resulting CartComponent class should look like this:

``` typescript
export class CartComponent implements OnInit {
  items;

  constructor(
    private cartService: CartService
  ) { }

  ngOnInit() {
    this.items = this.cartService.getItems();
  }
}
```

5. Update the template with a header ("Cart"), and use a <div> with an *ngFor to display each of the cart items with its name and price.

The resulting CartComponent template should look like this:

#### src/app/cart/cart.component.html
``` html
<h3>Cart</h3>

<div class="cart-item" *ngFor="let item of items">
  <span>{{ item.name }}</span>
  <span>{{ item.price | currency }}</span>
</div>
```



Test the cart component by clicking Buy button for couple of the products and clicking Checkout button.

<p align="center">
<img src="https://angular.io/generated/images/guide/start/cart-page-full.png" alt="Cart" title="Cart">
</p>

### Retrieve shipping prices

Data returned from servers often takes the form of a stream. Streams are useful because they make it easy to transform the data that is returned, and to make modifications to the way data is requested. The Angular HTTP client (HttpClient) is a built-in way to fetch data from external APIs and provide them to your application as a stream.

In this section, you'll use the HTTP client to retrieve shipping prices from an external file.

### Predefined shipping data
For the purpose of this Getting Started guide, we have provided shipping data in assets/shipping.json. You'll use this data to add shipping prices for items in the cart.

#### src/assets/shipping.json
``` json
[
  {
    "type": "Overnight",
    "price": 25.99
  },
  {
    "type": "2-Day",
    "price": 9.99
  },
  {
    "type": "Postal",
    "price": 2.99
  }
]
```
### Enable HttpClient for app
Before you can use Angular's HTTP client, you must set up your app to use HttpClientModule.

__Angular's HttpClientModule registers the providers needed to use a single instance of the HttpClient service throughout your app. The HttpClient service is what you inject into your services to fetch data and interact with external APIs and resources.__

1. Open app.module.ts. This file contains imports and functionality that is available to the entire app.
2. Import HttpClientModule from the @angular/common/http package.

#### src/app/app.module.ts

``` typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { RouterModule } from '@angular/router';
import { HttpClientModule } from '@angular/common/http';
```

3. __Add HttpClientModule to the imports array of the app module (@NgModule). This registers Angular's HttpClient providers globally.__

``` typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { RouterModule } from '@angular/router';
import { HttpClientModule } from '@angular/common/http';
import { ReactiveFormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { TopBarComponent } from './top-bar/top-bar.component';
import { ProductListComponent } from './product-list/product-list.component';
import { ProductAlertsComponent } from './product-alerts/product-alerts.component';
import { ProductDetailsComponent } from './product-details/product-details.component';

@NgModule({
  imports: [
    BrowserModule,
    HttpClientModule,
    ReactiveFormsModule,
    RouterModule.forRoot([
      { path: '', component: ProductListComponent },
      { path: 'products/:productId', component: ProductDetailsComponent },
      { path: 'cart', component: CartComponent },
    ])
  ],
  declarations: [
    AppComponent,
    TopBarComponent,
    ProductListComponent,
    ProductAlertsComponent,
    ProductDetailsComponent,
    CartComponent,
  ],
  bootstrap: [
    AppComponent
  ]
})
export class AppModule { }
```

### Enable HttpClient for cart service

Open cart.service.ts. Import HttpClient from the @angular/common/http package.Inject HttpClient into the constructor of the CartService component class.

#### src/app/cart.service.ts

``` typescript
import { Injectable } from '@angular/core';

import { HttpClient } from '@angular/common/http';
```

``` typescript
export class CartService {
  items = [];

  constructor(
    private http: HttpClient
  ) {}
}
```

### Define the get() method

As you've seen, multiple components can leverage the same service. Later in this tutorial, the shipping component will use the cart service to retrieve shipping data via HTTP from the shipping.json file. Here you'll define the get() method that will be used.

Continue working in cart.service.ts.

Below the clearCart() method, define a new getShippingPrices() method that uses the HttpClient#get() method to retrieve the shipping data (types and prices).

#### src/app/cart.service.ts
``` typescript
export class CartService {
  items = [];

  constructor(
    private http: HttpClient
  ) {}

  addToCart(product) {
    this.items.push(product);
  }

  getItems() {
    return this.items;
  }

  clearCart() {
    this.items = [];
    return this.items;
  }

  getShippingPrices() {
    return this.http.get('/assets/shipping.json');
  }
}
```

### Define the shipping page

Now that your app can retrieve shipping data, you'll create a shipping component and associated template.

Generate a new component named shipping.

#### src/app/shipping/shipping.component.ts
``` typescript
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-shipping',
  templateUrl: './shipping.component.html',
  styleUrls: ['./shipping.component.css']
})
export class ShippingComponent implements OnInit {

  constructor() { }

  ngOnInit() {
  }

}
```

In app.module.ts, add a route for shipping. Specify a path of shipping and a component of ShippingComponent.

#### src/app/app.module.ts

``` typescript
@NgModule({
  imports: [
    BrowserModule,
    HttpClientModule,
    ReactiveFormsModule,
    RouterModule.forRoot([
      { path: '', component: ProductListComponent },
      { path: 'products/:productId', component: ProductDetailsComponent },
      { path: 'cart', component: CartComponent },
      { path: 'shipping', component: ShippingComponent },
    ])
  ],
  declarations: [
    AppComponent,
    TopBarComponent,
    ProductListComponent,
    ProductAlertsComponent,
    ProductDetailsComponent,
    CartComponent,
    ShippingComponent
  ],
  bootstrap: [
    AppComponent
  ]
})
export class AppModule { }
```

The new shipping component isn't hooked into any other component yet, but you can see it in the preview pane by entering the URL specified by its route. 

Modify the shipping component so it uses the cart service to retrieve shipping data via HTTP from the shipping.json file.

  * Import the cart service

  #### src/app/shipping/shipping.component.ts

  ``` typescript
  import { Component, OnInit } from '@angular/core';

  import { CartService } from '../cart.service';
  ```

  * Define a shippingCosts property.

  ``` typescript
  export class ShippingComponent implements OnInit {
  shippingCosts;
  }
  ```

  * Inject the cart service into the ShippingComponent class. Set the shippingCosts property using the getShippingPrices() method from cart service.
  
  ``` typescript
  export class ShippingComponent implements OnInit {
  shippingCosts;

  constructor(
    private cartService: CartService
  ) {  }

  ngOnInit() {
    this.shippingCosts = this.cartService.getShippingPrices();
  }

  }
  ```

Update the shipping component's template to display the shipping types and prices using async pipe:

#### src/app/shipping/shipping.component.html

``` html
<h3>Shipping Prices</h3>

<div class="shipping-item" *ngFor="let shipping of shippingCosts | async">
  <span>{{ shipping.type }}</span>
  <span>{{ shipping.price | currency }}</span>
</div>
```

Add a link from the cart page to the shipping page:

#### src/app/cart/cart.component.html

``` html
<h3>Cart</h3>

<p>
  <a routerLink="/shipping">Shipping Prices</a>
</p>

<div class="cart-item" *ngFor="let item of items">
  <span>{{ item.name }}</span>
  <span>{{ item.price | currency }}</span>
</div>
```

Test your shipping prices feature.

Click on the "Checkout" button to see the updated cart. (Remember that changing the app causes the preview to refresh, which empties the cart.)

<p align="center">
<img src="https://angular.io/generated/images/guide/start/cart-empty-with-shipping-prices.png" alt="Shipping prices link" title="Shipping prices link">
</p>

<p align="center">
<img src="https://angular.io/generated/images/guide/start/shipping-prices.png" alt="Shipping prices" title="Shipping prices">
</p>


You have an online store application with a product catalog and shopping cart. You also have the ability to look up and display shipping prices.

## Part V

At the end of Managing Data, the online store application has a product catalog and a shopping cart.

In this section, you'll finish the app by adding a form-based checkout feature. You'll create a form to collect user information as part of checkout.

### Forms in Angular

Forms in Angular take the standard capabilities of the HTML based forms and add an orchestration layer to help with creating custom form controls, and to supply great validation experiences. __There are two parts to an Angular Reactive form, the objects that live in the component to store and manage the form, and the visualization of the form that lives in the template.__

#### Define the checkout form model

First, you'll set up the checkout form model. The form model is the source of truth for the status of the form and is defined in the component class.

1. Open cart.component.ts.
2. Angular's FormBuilder service provides convenient methods for generating controls. As with the other services you've used, you need to import and inject the service before you can use it:
  * Import the FormBuilder service from the @angular/forms package.
  
  #### src/app/cart/cart.component.ts
  ``` typescript
  import { Component } from '@angular/core';
  import { FormBuilder } from '@angular/forms';

  import { CartService } from '../cart.service';
  ```
  __The FormBuilder service is provided by the ReactiveFormsModule, which is already defined in the AppModule you modified previously (in app.module.ts).__

  * Inject the FormBuilder service.
  #### src/app/cart/cart.component.ts
  ``` typescript
  export class CartComponent {
  items;

  constructor(
    private cartService: CartService,
    private formBuilder: FormBuilder,
  ) {  }
  }
  ```
3. In the CartComponent class, define the checkoutForm property to store the form model.During checkout, the app will prompt the user for a name and address. So that you can gather that information later, set the checkoutForm property with a form model containing name and address fields, using the FormBuilder#group() method.

#### src/app/cart/cart.component.ts
``` typescript
export class CartComponent {
  items;
  checkoutForm;

  constructor(
    private cartService: CartService,
    private formBuilder: FormBuilder,
  ) {
    this.items = this.cartService.getItems();

    this.checkoutForm = this.formBuilder.group({
      name: '',
      address: ''
    });
  }
}
```

4. For the checkout process, users need to be able to submit the form data (their name and address). When the order is submitted, the form should reset and the cart should clear.

In cart.component.ts, define an onSubmit() method to process the form. Use the CartService#clearCart() method to empty the cart items and reset the form after it is submitted. (In a real-world app, this method also would submit the data to an external server.)

The entire cart component is shown below:

``` typescript
import { Component } from '@angular/core';
import { FormBuilder } from '@angular/forms';

import { CartService } from '../cart.service';

@Component({
  selector: 'app-cart',
  templateUrl: './cart.component.html',
  styleUrls: ['./cart.component.css']
})
export class CartComponent {
  items;
  checkoutForm;

  constructor(
    private cartService: CartService,
    private formBuilder: FormBuilder,
  ) {
    this.items = this.cartService.getItems();

    this.checkoutForm = this.formBuilder.group({
      name: '',
      address: ''
    });
  }

  onSubmit(customerData) {
    // Process checkout data here
    console.warn('Your order has been submitted', customerData);

    this.items = this.cartService.clearCart();
    this.checkoutForm.reset();
  }
}
```

__The form model is defined in the component class. To reflect the model in the view, you'll need a checkout form.__

### Create the checkout form

Next, you'll add a checkout form at the bottom of the "Cart" page.

1. At the bottom of the template (cart.component.html), add an empty HTML form to capture user information.

2. Use a formGroup property binding to bind the checkoutForm to the form tag in the template. Also include a "Purchase" button to submit the form.

#### src/app/cart/cart.component.html
``` html
<form [formGroup]="checkoutForm">

  <button class="button" type="submit">Purchase</button>

</form>
```

3. On the form tag, use an ngSubmit event binding to listen for the form submission and call the onSubmit() method with the checkoutForm value.

``` html
<form [formGroup]="checkoutForm" (ngSubmit)="onSubmit(checkoutForm.value)">
</form>
```

4. Add input fields for name and address. __Use the formControlName attribute binding to bind the checkoutForm form controls for name and address to their input fields.__ The final complete component is shown below:

``` html
<h3>Cart</h3>

<p>
  <a routerLink="/shipping">Shipping Prices</a>
</p>

<div class="cart-item" *ngFor="let item of items">
  <span>{{ item.name }} </span>
  <span>{{ item.price | currency }}</span>
</div>

<form [formGroup]="checkoutForm" (ngSubmit)="onSubmit(checkoutForm.value)">

  <div>
    <label for="name">
      Name
    </label>
    <input id="name" type="text" formControlName="name">
  </div>

  <div>
    <label for="address">
      Address
    </label>
    <input id="address" type="text" formControlName="address">
  </div>

  <button class="button" type="submit">Purchase</button>

</form>
```



<p align="center">
<img src="https://angular.io/generated/images/guide/start/cart-with-items-and-form.png" alt="Form added" title="Form added">
</p>


Congratulations! You have a complete online store application with a product catalog, a shopping cart, and a checkout function.